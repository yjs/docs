---
description: Adding offline support with y-indexeddb.
---

# Offline Support

We covered that network providers sync document updates over a network protocol to other peers. Database providers sync document updates to a database. The [y-indexeddb](https://github.com/yjs/y-indexeddb) provider syncs document updates to an [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB\_API) database - a low-level NoSQL store that is supported by all modern browsers.

Adding offline support in Yjs is as easy as including the [y-indexeddb](https://github.com/yjs/y-indexeddb) provider:

{% tabs %}
{% tab title="Use" %}
```javascript
import { IndexeddbPersistence } from 'y-indexeddb'

const ydoc = new Y.Doc()
const roomName = 'my-room-name'
const persistence = new IndexeddbPersistence(roomName, ydoc)

// The persistence provider works similarly to the network providers:
// const network = new WebrtcProvider(roomName)
```
{% endtab %}

{% tab title="Install" %}
```bash
npm i y-indexeddb --save
```
{% endtab %}
{% endtabs %}

Now every change is persisted to a local database. The next time you visit your site, your document will be loaded from the IndexedDB database. Only the latest changes are synced over the database provider.

You can listen to `synced` events that fire when your client loaded content from the IndexedDB database:

```javascript
persistence.once('synced', () => { console.log('initial content loaded') })
```

Another advantage of using y-indexeddb is that it replicates state to every peer that ever visited the document. In case any peer (e.g. the server) loses some data, the other peers will eventually sync the latest document state back to the server.

{% hint style="info" %}
y-indexeddb works with any other provider. Again, Yjs providers are meshable. You can use several providers at the same time to achieve maximum reliability.
{% endhint %}

Database providers also allow native applications to sync document state to a local database. There is a growing collection of providers that work in different environments available in the [ecosystem section](../ecosystem/database-provider/).

### Loading HTML content without network access

In case you manage all application state with Yjs, you'll have an easy time adding offline support to your app. Simply add y-indexeddb and include a service worker to make the website accessible even without network access.

A [service worker](https://developer.mozilla.org/en-US/docs/Web/API/Service\_Worker\_API) acts like a proxy that persists your HTML/JS/CSS in the web browser. When all data is persisted using service workers, you can even load your website without internet access.

This resource is a great starting point to build your own service worker:

{% embed url="https://microsoft.github.io/win-student-devs/#/30DaysOfPWA/core-concepts/05" %}













