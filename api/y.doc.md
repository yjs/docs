# Y.Doc



```javascript
import * as Y from 'yjs'

const doc = new Y.Doc()
```

### Y.Doc API

**`doc.clientID: number`**   
    A unique id that identifies this client \(readonly\).

**`doc.gc: boolean`**   
     Whether garbage collection is enabled on this doc instance. Set `doc.gc = false` to disable gc and be able to restore old content. See [Internals](internals.md) for more information about how garbage collection works.

**`doc.transact(function(Transaction): void [, origin:any])`**   
    Every change on the shared document happens in a transaction. Observer calls and the `update` event are called after each transaction. You should bundle changes into a single transaction to reduce event calls. I.e. `doc.transact(() => { yarray.insert(..); ymap.set(..) })` triggers a single change event.  
You can specify an optional `origin` parameter that is stored on `transaction.origin` and `on('update', (update, origin) => ..)`.

**`doc.get(string, Y.[TypeClass]): [Type]`**  
    Define a shared type.

**`doc.toArray(string = ''): Y.Array`**  
    Define a shared Y.Array type. Is equivalent to `y.get(string, Y.Array)`.

**`doc.getMap(string = ''): Y.Map`**  
    Define a shared Y.Map type. Is equivalent to `y.get(string, Y.Map)`.

**`doc.getXmlFragment(string = ''): Y.XmlFragment`**  
    Define a shared Y.XmlFragment type. Is equivalent to `y.get(string, Y.XmlFragment)`.

**`doc.on(eventName: string, function(event))`**  
    Register an [event handler](y.doc.md#event-handler).

**`doc.once(eventName: string, function(event))`**  
    Register an [event handler](y.doc.md#event-handler). But only call it once.

**`doc.off(eventName: string, function(event))`**  
    Unregister an [event handler](y.doc.md#event-handler).

### Event Handler

`doc.on('beforeTransaction', function(tr: Transaction, doc: Y.Doc)`  
    The event handler is called right before every transaction. 

`doc.on('beforeObserverCalls', function(tr: Transaction, doc: Y.Doc)`  
    The event handler is called right before observers on shared types are called.

`doc.on('afterTransaction', function(tr: Transaction, doc: Y.Doc)`  
    The event handler is called right before every transaction. 

`doc.on('update', function(update: Uint8Array, origin: any, doc: Y.Doc)`  
    Listen to update messages on the shared document. As long as all update messages are propagated to all users, everyone will eventually consent to the same state. See more about this in the [Document Updates](document-updates.md) chapter.  
    You can generate update messages from the transaction as well, but since creating update messages is relatively expensive we try to generate it once and call this event handler.

`doc.on('updateV2', function(update: Uint8Array, origin: any, doc: Y.Doc)`  
    \(EXPERIMENTAL\) This is an alternative update message format that is up to 10x more efficient. Should not be used in production.

`doc.on('subdocs', function(changes: { loaded: Set<Y.Doc>, added: Set<Y.Doc>, removed: Set<Y.Doc> })`  
    Event triggered when subdocuments are added/removed or loaded. See [Subdocuments](subdocuments.md) on how this event can be used. 

### Order of events

All changes to a Yjs document / shared types happen in a transaction. Events are called in the following order:

1. `ydoc.on('beforeTransaction', event => { .. })`
2. The transaction is executed.
3. `ydoc.on('beforeObserverCalls', event => {})`
4. `ytype.observe(event => { .. })` 
5. `ytype.observeDeep(event => { .. })` 
6. `ydoc.on('afterTransaction', event => {})` 
7. `ydoc.on('update', update => { .. })` 

