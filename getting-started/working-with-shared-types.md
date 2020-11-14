# Working with Shared Types

By now, we have learned how to make an editor collaborative and how to sync document updates using different providers. But we haven't covered the most unique feature of Yjs yet: Shared Types.

Shared types allow you to make every aspect of your application collaborative. For example, you could sync your react-state using shared types. You can sync diagrams, drawings, and even whole 3d worlds using shared types to automatically resolve conflicts.

But a shared type is nothing complicated. It is just a common data type like [Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array), [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map), or [Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set). The only difference is that it automatically syncs & persists its state \(using the providers\) and that you can observe changes on shared types.

We already learned about the Y.Text type that we "bound" to an editor instance to automatically sync a rich-text editor. Yjs supports many other shared types like Y.Array, Y.Map, and Y.Xml. A complete list, including documentation for each type, can be found in the [shared types section](../api/shared-types/).

First, we define a shared type on a Yjs document. Then we can manipulate it and observe changes.

```javascript
import * as Y from 'yjs'

const ydoc = new Y.Doc()
// Define a Y.Array named "my array"
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
// Or you simply observe deep changes on yarray (allowing you to observe child-events as well)
yarray.observeDeep(events => { console.log('All deep events: ', events) })
subArray.insert(0, ['this works']) // => All deep events: [..]
// You can't insert the array at another place. A shared type can only exist in one place.
yarray.insert(0, [subArray]) // Throws exception!

```

The other data types work similarly to Y.Array. For the complete documentation, you should have a look at the shared types section that covers each type and the event format in detail. 

{% page-ref page="../api/shared-types/" %}

### Transactions

All changes must happen in a transaction. When you mutate a shared type without creating a transaction \(e.g. `yarray.insert(..)`\), Yjs will automatically create a transaction before manipulating the object. You can create transactions explicitly like this:

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

If possible, you should bundle as many changes in a single transaction as possible. The advantage is that you limit the number of observer calls. You also avoid other expensive event-handler calls. 

Yjs fires events in the following order:

* `ydoc.on('beforeTransaction', event => { .. })` -  Called before any transaction, allowing you to store relevant information before changes happen.
* Now the transaction function is executed.
* `ydoc.on('beforeObserverCalls', event => {})`
* `ytype.observe(event => { .. })` -  All observers are called.
* `ytype.observeDeep(event => { .. })` -  All deep observers are called. 
* `ydoc.on('afterTransaction', event => {})` 
* `ydoc.on('update', update => { .. })` - This update message is propagated by the providers.

Especially when manipulating many objects, it makes sense to reduce the number of update messages created. So use transactions whenever possible.

### Creating a file-system with Yjs 

\[todo\]
