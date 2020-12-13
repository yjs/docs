---
description: IndexedDB database provider for Yjs
---

# y-indexeddb

Use the IndexedDB database provider to store your shared data persistently in the browser. The next time you join the session, your changes will be loaded from the local browser database.

* Minimizes the amount of data exchanged between server and client
* Makes offline editing possible

{% embed url="https://github.com/yjs/y-indexeddb" caption="" %}

## Getting Started

The following guide shows you some advanced features of the y-indexeddb database provider. There is a dedicated getting-started guide on creating offline-capable applications with Yjs.

{% page-ref page="../../getting-started/allowing-offline-editing.md" %}

{% tabs %}
{% tab title="Use" %}
```javascript
import { IndexeddbPersistence } from 'y-indexeddb'

const provider = new IndexeddbPersistence(docName, ydoc)

provider.on('synced', () => {
  console.log('content from the database is loaded')
})
```
{% endtab %}

{% tab title="Install" %}
```bash
npm i --save y-indexeddb
```
{% endtab %}
{% endtabs %}

## API

**`provider = new IndexeddbPersistence(docName: string, ydoc: Y.Doc)`**  
    Create a y-indexeddb persistence provider. Specify `docName` as a unique string that identifies this document. In most cases, you want to use the same identifier that is used as the room-name in the connection provider.

**`provider.on('synced'', function(idbPersistence: IndexeddbPersistence))`**  
    The `"synced"` event is fired when the connection to the database has been established and all available content has been loaded. The event is also fired when no content is available yet.

**`provider.set(key: any, value: any): Promise<any>`**  
    Set a custom property on the provider instance. You can use this to store relevant meta-information for the persisted document. However, the content will not be synced with other peers.

**`provider.get(key: any): Promise<any>`**  
    Retrieve a stored value.

**`provider.del(key: any): Promise<undefined>`**  
    Delete a stored value.

**`provider.destroy(): Promise`**  
    Close the connection to the database and stop syncing the document. This method is automatically called when the Yjs document is destroyed \(e.g. `ydoc.destroy()`\).

**`provider.clearData(): Promise`**  
    Destroy this database and remove the stored document and all related meta-information from the database.

