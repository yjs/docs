# Document Updates

Changes on the shared document are encoded into _document updates_. Document updates are _commutative_ and _idempotent_. This means that they can be applied in any order and multiple times.

#### **Example: Listen to update events and apply them on a remote client**

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

Yjs internally maintains a [state vector](https://github.com/yjs/yjs#State-Vector) that denotes the next expected clock from each client. In a different interpretation, it holds the number of structs created by each client. When two clients sync, you can either exchange the complete document structure or only the differences by sending the state vector to compute the differences.

**Example: Sync two clients by exchanging the complete document structure**

```javascript
const state1 = Y.encodeStateAsUpdate(ydoc1)
const state2 = Y.encodeStateAsUpdate(ydoc2)
Y.applyUpdate(ydoc1, state2)
Y.applyUpdate(ydoc2, state1)
```

**Example: Sync two clients by computing the differences**

This example shows how to sync two clients with a minimal amount of exchanged data by computing only the differences using the state vector of the remote client. Syncing clients using the state vector requires another roundtrip but can save a lot of bandwidth.

```javascript
const stateVector1 = Y.encodeStateVector(ydoc1)
const stateVector2 = Y.encodeStateVector(ydoc2)
const diff1 = Y.encodeStateAsUpdate(ydoc1, stateVector2)
const diff2 = Y.encodeStateAsUpdate(ydoc2, stateVector1)
Y.applyUpdate(ydoc1, diff2)
Y.applyUpdate(ydoc2, diff1)
```

## Update API

**`Y.applyUpdate(Y.Doc, update:Uint8Array, [transactionOrigin:any])`**   
    Apply a document update on the shared document. Optionally you can specify `transactionOrigin` that will be stored on `transaction.origin` and `ydoc.on('update', (update, origin) => ..)`.

**`Y.encodeStateAsUpdate(Y.Doc, [encodedTargetStateVector:Uint8Array]): Uint8Array`**   
    Encode the document state as a single update message that can be applied on the remote document. Optionally specify the target state vector to only write the differences to the update message.

**`Y.encodeStateVector(Y.Doc): Uint8Array`**   
    Computes the state vector and encodes it into an Uint8Array.

