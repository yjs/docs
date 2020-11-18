# Y.Doc

```javascript
import * as Y from 'yjs'

const doc = new Y.Doc()
```

### Y.Doc API

**`doc.clientID`**   
    A unique id that identifies this client \(readonly\).

**`doc.gc`**   
     Whether garbage collection is enabled on this doc instance. Set `doc.gc = false` to disable gc and be able to restore old content. See [Internals](internals.md) for more information about how garbage collection works.

**`doc.transact(function(Transaction):void [, origin:any])`**   
    Every change on the shared document happens in a transaction. Observer calls and the `update` event are called after each transaction. You should bundle changes into a single transaction to reduce event calls. I.e. `doc.transact(() => { yarray.insert(..); ymap.set(..) })` triggers a single change event.  
You can specify an optional `origin` parameter that is stored on `transaction.origin` and `on('update', (update, origin) => ..)`.  


### Event Handler

\[The order of calls\]

\[the format of event handler messages\]

\[..\]

 \#\# Document





