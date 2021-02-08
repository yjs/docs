---
description: How to sync documents with other peers.
---

# Document Updates

Changes on the shared document are encoded into binary encoded \(highly compressed\) _document updates_. Document updates are _commutative, associative,_ and _idempotent_. This means that you can apply them in any order and multiple times. All clients will sync up when they received all document updates.

## Update API

**`Y.applyUpdate(Y.Doc, update:Uint8Array, [transactionOrigin:any])`**   
    Apply a document update on the shared document. Optionally you can specify `transactionOrigin` that will be stored on `transaction.origin` and `ydoc.on('update', (update, origin) => ..)`.

**`Y.encodeStateAsUpdate(Y.Doc, [encodedTargetStateVector:Uint8Array]): Uint8Array`**   
    Encode the document state as a single update message that can be applied on the remote document. Optionally, specify the target state vector to only write the missing differences to the update message.

**`Y.encodeStateVector(Y.Doc): Uint8Array`**   
    Computes the state vector and encodes it into an Uint8Array. A state vector describes the state of the local client. The remote client can use this to exchange only the missing differences.

**`ydoc.on('update', eventHandler: function(update: Uint8Array, origin: any, doc: Y.Doc))`**   
    Listen to incremental updates on the Yjs document. This is part of the [Y.Doc API](y.doc.md#event-handler).  Send the computed incremental update to all connected clients, or store it in a database.

**`Y.logUpdate(Uint8Array)`** \(experimental\)  
    Log the contents of a document update to the console. This utility function is only meant for debugging and understanding the Yjs document format. It is marked as experimental because it might be changed or removed at any time.

## Alternative Update API

It is possible to sync clients and compute delta updates without loading the Yjs document to memory. Yjs exposes an API to compute the differences directly on the binary document updates. This allows you to sync efficiently while only maintaining the compressed binary-encoded document state in-memory.  [\(see example\)](document-updates.md#example-syncing-clients-without-loading-the-y-doc)

**`Y.mergeUpdates(Array<Uint8Array>): Uint8Array`**   
    Merge several document updates into a single document update while removing duplicate information. The merged document update is always smaller than the separate updates because of the compressed encoding.

**`Y.encodeStateVectorFromUpdate(Uint8Array): Uint8Array`**   
    Computes the state vector from a document update and encodes it into an Uint8Array.

**`Y.diffUpdate(update: Uint8Array, stateVector: Uint8Array): Uint8Array`**   
    Encode the missing differences to another update message. This function works similarly to `Y.encodeStateAsUpdate(ydoc, stateVector)` but works on updates instead.

## Examples

### **Example: Listen to update events and apply them on a remote client**

```javascript
const doc1 = new Y.Doc()
const doc2 = new Y.Doc()

doc1.on('update', update => {
  Y.applyUpdate(doc2, update)
})

doc2.on('update', update => {
  Y.applyUpdate(doc1, update)
})

// All changes are also applied to the other document
doc1.getArray('myarray').insert(0, ['Hello doc2, you got this?'])
doc2.getArray('myarray').get(0) // => 'Hello doc2, you got this?'
```

### Syncing clients

Yjs internally maintains a [state vector](https://github.com/yjs/yjs#State-Vector) that denotes the next expected clock from each client. In a different interpretation, it holds the number of modifications created by each client. When two clients sync, you can either exchange the complete document structure or only the differences by sending the state vector to compute the differences.

#### **Example: Sync two clients by exchanging the complete document structure**

```javascript
const state1 = Y.encodeStateAsUpdate(ydoc1)
const state2 = Y.encodeStateAsUpdate(ydoc2)
Y.applyUpdate(ydoc1, state2)
Y.applyUpdate(ydoc2, state1)
```

#### **Example: Sync two clients by computing the differences**

This example shows how to sync two clients with a minimal amount of data exchanged by computing the differences using the state vector of the remote client. Syncing clients using the state vector requires another roundtrip but can save a lot of bandwidth.

```javascript
const stateVector1 = Y.encodeStateVector(ydoc1)
const stateVector2 = Y.encodeStateVector(ydoc2)
const diff1 = Y.encodeStateAsUpdate(ydoc1, stateVector2)
const diff2 = Y.encodeStateAsUpdate(ydoc2, stateVector1)
Y.applyUpdate(ydoc1, diff2)
Y.applyUpdate(ydoc2, diff1)
```

#### Example: Syncing clients without loading the Y.Doc

```javascript
// encode the current state as a binary buffer
let currentState1 = Y.encodeStateAsUpdate(ydoc1)
let currentState2 = Y.encodeStateAsUpdate(ydoc2)
// now we can continue syncing clients without
// using the Y.Doc
ydoc1.destroy()
ydoc2.destroy()

const stateVector1 = Y.encodeStateVectorFromUpdate(currentState1)
const stateVector2 = Y.encodeStateVectorFromUpdate(currentState2)
const diff1 = Y.diffUpdate(currentState1, stateVector2)
const diff2 = Y.diffUpdate(currentState2, stateVector1)

// sync clients
currentState1 = Y.mergeUpdates([currentState1, diff2])
currentState1 = Y.mergeUpdates([currentState2, diff1])
```

### Example: Base64 encoding

We compress document updates to a highly compressed binary format. Therefore, document updates are represented as [Uint8Arrays](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array). An `Uint8Array` represents binary data similarly to a [NodeJS' Buffer](https://nodejs.org/api/buffer.html) . The difference is that `Uint8Array` is available in all JavaScript environments. The catch is that you can't [`JSON.stringify`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)/[`JSON.parse`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse) the data because there is no JSON representation for binary data.  However, most communication protocols support binary data. If you still need to transform the data into a string, you can use [Base64 encoding](https://en.wikipedia.org/wiki/Base64). For example, by using the [`js-base64`](https://www.npmjs.com/package/js-base64) library:

{% tabs %}
{% tab title="JavaScript" %}
```javascript
import { fromUint8Array, toUint8Array } from 'js-base64'

const documentState = Y.encodeStateAsUpdate(ydoc) // is a Uint8Array
// Transform Uint8Array to a Base64-String
const base64Encoded = fromUint8Array(documentState)
// Transform Base64-String back to an Uint8Array
const binaryEncoded = toUint8Array(base64Encoded)
```
{% endtab %}

{% tab title="Install" %}
```bash
npm install js-base64
```
{% endtab %}
{% endtabs %}



