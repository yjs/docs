---
description: Embedding Yjs documents into Yjs documents
---

# Subdocuments

Yjs documents can be embedded into shared types. This allows you to manage vast amounts of Yjs documents as part of a root document.

```javascript
// CLient One
const rootDoc = new Y.Doc()
const folder = rootDoc.getMap()

const subDoc = new Y.Doc()
subDoc.getText().insert(0, 'some initial content')
folder.set('my-document.txt', subDoc)
```

An obvious use-case is to manage documents in a folder structure. Each note could be represented as a subdocument that is lazily loaded to memory when needed. By default, subdocuments are empty until they are explicitly loaded.

```javascript
// Client Two
const subDoc = rootDoc.getMap().get('my-document.text')
const subDocText = subDoc.getText()
subDocText.toString() // => "" - content is empty

// Data needs to be loaded first..
subDoc.load()

// Then the providers will fetch data from the database / network
// and eventually fill the content
subDoc.on('synced', () => {
  subDocText.toString() // => "some initial content"
})

// It is hard to determine when data was actually synced.
// The synced event is still helpful to show the user that this user synced
// with other users.
// It is safer to observe the data types and don't listen
// to an explicit sync event.
subDocText.observe(() => {
  // data changed..
})
```

Subdocuments are lazily loaded after they have been explicitly loaded to memory. A subdocument can be destroyed `doc.destroy()` to free all used memory and destroy existing data bindings. The document can be accessed again to force the provider to load the content again.

```javascript
const subDoc = rootDoc.getMap().get('my-document.text')
subDoc.load()

// After some time you might not need to render the document anymore.
// You want to destroy the existing data bindings and load a different document
...

subDoc.destroy() // free all memory and destroy all data bindings to this document.

// You should not access the destroyed document again.
// Instead you can create a new one by requesting the document again.
const subDocReloaded = rootDoc.getMap().get('my-document.text')
subDocReloaded.load()
```

It is up to the providers to sync subdocuments. It is possible to create a very efficient sync mechanism using sub-documents. The providers can sync collections of documents in one flush instead of having multiple requests. It is also possible to handle authorization over the folder structure. But all official Yjs providers currently think of sub-documents as separate entities. A new feature that was introduced in Yjs@13.4.0 is that all documents are given a GUID. The documents are identified with GUIDs and used as a room-name to sync documents. This allows you to duplicate data in the document structure.

```javascript
const rootDoc = new Y.Doc()

const doc = new Y.Doc()
doc.guid // => "123e4567-e89b-12d3-a456-426614174000" - A random UUIDv4

rootDoc.getMap().set('file.txt', doc)

// we can include a copy of a subdocument by giving it the same UUIDv4
const copy = new Y.Doc({ guid: doc.guid })
rootDoc.getMap().set('copy.txt', copy)

// `doc` and `copy` will automatically sync because they have the same guid
```

By default, all subdocuments must be explicitly loaded before they are filled with content. It is possible to define `Y.Doc({ autoLoad: true })` to specify that all peers should automatically load the document.

Providers listen to `subdocs` events to get notified when subdocuments are added, removed, or loaded.

```typescript
doc.on('subdocs', ({ added: Set<Y.Doc>, removed: Set<Y.Doc>, loaded: Set<Y.Doc> }) => {
  // use added / removed to sync documents in the background
  // use loaded to fill document content
})
```

Providers \(e.g. y-websocket, y-indexeddb\) are responsible for syncing subdocuments. Not all providers support subdocuments yet. A simple method to implement lazy-loading documents is to create a provider instance to the `doc.guid`-room once a document is loaded:

```javascript
doc.on('subdocs', ({ loaded }) => {
  loaded.forEach(subdoc => {
    new WebrtcProvider(subdoc.guid, subdoc)
  })
})
doc.getSubdocs() // Get the Set<Y.Doc> of all subdocuments
```

