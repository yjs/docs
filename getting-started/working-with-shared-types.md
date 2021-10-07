# Shared Types

By now, we have learned how to make an editor collaborative and how to sync document updates using different providers. But we haven't covered the most unique feature of Yjs yet: Shared Types.

Shared types allow you to make every aspect of your application collaborative. For example, you could sync your react-state using shared types. You can sync diagrams, drawings, and even whole 3d worlds using shared types to automatically resolve conflicts. 

Shared types are similar to common data types like [Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array), [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map), or [Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set). The only difference is that they automatically sync & persist their state \(using the providers\) and that you can observe them.

We already learned about the Y.Text type that we "bound" to an editor instance to automatically sync a rich-text editor. Yjs supports many other shared types like Y.Array, Y.Map, and Y.Xml. A complete list, including documentation for each type, can be found in the [shared types section](../api/shared-types/).

Shared type instances must be connected to a Yjs document so we can sync them. First, we define a shared type on a Yjs document. Then we can manipulate it and observe changes.

```javascript
import * as Y from 'yjs'

const ydoc = new Y.Doc()
// Define an instance of Y.Array named "my array"
// Every peer that defines "my array" like this will sync content with this peer.
const yarray = ydoc.getArray('my array')

// We can register change-observers like this
yarray.observe(event => {
  // Log a delta every time the type changes
  // Learn more about the delta format here: https://quilljs.com/docs/delta/
  console.log('delta:', event.changes.delta)
})

// There are a few caveats that you need to understand when working with shared types
// It is best to explain this in a few lines of code:

// We can insert & delete content
yarray.insert(0, ['some content']) // => delta: [{ insert: ['some content'] }]
// Note that the above method accepts an array of content to insert. 
// So the final document will look like this:
yarray.toArray() // => ['some content']
// We can insert anything that is JSON-encodable. Uint8Arrays also work.
yarray.insert(0, [1, { bool: true }, new Uint8Array([1,2,3])]) // => delta: [{ insert: [1, { bool: true }, Uint8Array([1,2,3])] }]
yarray.toArray() // => [1, { bool: true }, Uint8Array([1,2,3]), 'some content']
// You can even insert Yjs types, allowing you to create nested structures
const subArray = new Y.Array()
yarray.insert(0, [subArray]) // => delta: [{ insert: [subArray] }]
// Note that the above observer doesn't fire when you insert content into subArray
subArray.insert(0, ['nope']) // [observer not called]
// You need to create an observer on subArray instead
subArray.observe(event => { .. })
// Alternatively you can observe deep changes on yarray (allowing you to observe child-events as well)
yarray.observeDeep(events => { console.log('All deep events: ', events) })
subArray.insert(0, ['this works']) // => All deep events: [..]
// You can't insert the array at another place. A shared type can only exist in one place.
yarray.insert(0, [subArray]) // Throws exception!

```

The other data types work similarly to Y.Array. The complete documentation is available in the shared types section that covers each type and the event format in detail. 

{% page-ref page="../api/shared-types/" %}

### Caveats

There are some things that are not possible with shared types, but that are possible with normal data types. Most importantly, it is not possible to move a type that was inserted into a Yjs document to a different location. The other important caveat is that you shouldn't modify JSON that you inserted or retrieved from a shared type. Yjs doesn't clone the inserted objects to improve performance. So when you modify a JSON object, you will actually change the internal representation of Yjs without notifying other peers of that change.

```javascript
// 1. An inserted array must not be moved to a different location
yarray.insert(0, ymap.get("my other array") as Y.Array) // will throw an error
// 2. It is discouraged to modify JSON that is inserted or retrieved from a Yjs type
//    This might lead to documents that don't synchronize anymore.
const myObject = { val: 0 }
ymap.set(0, myObject)
ymap.get(0).val = 1 // Doesn't throw an error, but is highly discouraged
myobject.val = 2 // Also doesn't throw an error, but is also discouraged.
```

### Transactions

All changes must happen in a transaction. When you mutate a shared type without creating a transaction \(e.g. `yarray.insert(..)`\), Yjs will automatically create a transaction before manipulating the shared object. You can create transactions explicitly like this:

```javascript
const ydoc = new Y.Doc()
const ymap = ydoc.getMap('favorites')

// set an initial value - to demonstrate the how changes in ymap are represented
ymap.set('food', 'pizza')

// observers are called after each transaction
ymap.observe(event => {
  console.log('changes', event.changes.keys)
})

ydoc.transact(() => {
  ymap.set('food', 'pencake')
  ymap.set('number', 31)
}) // => changes: Map({ number: { action: 'added' }, food: { action: 'updated', oldValue: undefined } })

```

Event handlers and observers are called after each transaction. If possible, you should bundle as many changes in a single transaction as possible. The advantage is that you reduce expensive observer calls and create fewer updates that are sent to other peers.

Yjs fires events in the following order:

* `ydoc.on('beforeTransaction', event => { .. })` -  Called before any transaction, allowing you to store relevant information before changes happen.
* Now the transaction function is executed.
* `ydoc.on('beforeObserverCalls', event => {})`
* `ytype.observe(event => { .. })` - Observers are called.
* `ytype.observeDeep(event => { .. })` -  Deep observers are called. 
* `ydoc.on('afterTransaction', event => {})` - Called after each transaction.
* `ydoc.on('update', update => { .. })` - This update message is propagated by the providers.

Especially when manipulating many objects, it makes sense to reduce the creation of update messages. So use transactions whenever possible.

### Managing multiple collaborative documents in a shared type

We often want to manage multiple collaborative documents in a single Yjs document. You can manage multiple documents using shared types. In the following demo project, I implemented functionality to add & delete documents. The list of all documents is updated in real-time as well. 

{% embed url="https://stackblitz.com/edit/y-quill-doc-list" %}



{% embed url="https://stackblitz.com/edit/y-quill-doc-list" %}

You could extend the above demo project to ..

* .. be able to delete specific documents
* .. have a collaborative document-name. You could introduce a Y.Map that holds the document-name, the document-content, and the creation-date.
* .. extend the document list to a fully-fledged file system based on shared types.

### Collaborative Drawing App

\[..\]

### Conclusion

Shared types are not just great for collaborative editing. They are a unique kind of data structure that can be used to sync any kind of state across servers, browsers, and [soon also native applications](https://github.com/yjs/yrs).  Yjs is well suited for creating collaborative applications and gives you all the tools you need to create complex applications that can compete with Google Workspace. But shared types might be useful in high-performance computing as well for sharing state across threads; or in gaming for syncing data to remote clients directly without a roundtrip to a server. Since Yjs & shared types don't depend on a central server, these data structures are the ideal building blocks for decentralized, privacy-focused applications as well. 

I hope that this section gave you some inspiration for using shared types.

### 

### 





