---
description: WebSocket Provider for Yjs that ships with a extendable server implementation
---

# y-websocket

The Websocket Provider implements a conventional client-server model. Clients connect to a single endpoint over Websocket. The server distributes document updates and awareness information among clients. You can configure providers on the server as well, which allow you to persist document updates or scale your infrastructure.

The Websocket Provider is a solid choice if you want a central source that handles authentication and authorization. Websockets also send header information and cookies, so you can use existing authentication mechanisms with this server.

* Supports cross-tab communication. When you open the same document in the same browser, changes on the document are exchanged via cross-tab communication ([Broadcast Channel](https://developer.mozilla.org/en-US/docs/Web/API/Broadcast\_Channel\_API) and [localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) as fallback).
* Supports the exchange of awareness information (e.g. cursors).

{% embed url="https://github.com/yjs/y-websocket" %}

## Getting Started

{% tabs %}
{% tab title="JavaScript" %}
```javascript
import * as Y from 'yjs'
import { WebsocketProvider } from 'y-websocket'

const doc = new Y.Doc()
const wsProvider = new WebsocketProvider('ws://localhost:1234', 'my-roomname', doc)

wsProvider.on('status', event => {
  console.log(event.status) // logs "connected" or "disconnected"
})
```
{% endtab %}

{% tab title="Install" %}
```
npm i y-websocket
```
{% endtab %}
{% endtabs %}

### Special case: Using y-websocket in NodeJS

The WebSocket provider requires a [`WebSocket`](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket) object to create a connection to a server. You can polyfill WebSocket support in Node.js using the [`ws` package](https://www.npmjs.com/package/ws).

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const ws = require('ws')

const wsProvider = new WebsocketProvider(
  'ws://localhost:1234', 'my-roomname',
  doc,
  { WebSocketPolyfill: ws }
)
```
{% endtab %}

{% tab title="Install" %}
```bash
npm i ws
```
{% endtab %}
{% endtabs %}

## API

```javascript
import { WebsocketProvider } from 'y-websocket'
```

**`wsProvider = new WebsocketProvider(serverUrl: string, room: string, ydoc: Y.Doc [, wsOpts: WsOpts])`**\
****    Create a new websocket-provider instance. As long as this provider, or the connected `ydoc`, is not destroyed, the changes will be synced to other clients via the connected server. Optionally, you may specify a configuration object. The following default values of `wsOpts` can be overwritten.&#x20;

```javascript
wsOpts = {
  // Set this to `false` if you want to connect manually using wsProvider.connect()
  connect: true,
  // Specify a query-string that will be url-encoded and attached to the `serverUrl`
  // I.e. params = { auth: "bearer" } will be transformed to "?auth=bearer"
  params: {}, // Object<string,string>
  // You may polyill the Websocket object (https://developer.mozilla.org/en-US/docs/Web/API/WebSocket).
  // E.g. In nodejs, you could specify WebsocketPolyfill = require('ws')
  WebsocketPolyfill: Websocket,
  // Specify an existing Awareness instance - see https://github.com/yjs/y-protocols
  awareness: new awarenessProtocol.Awareness(ydoc)
}
```

**`wsProvider.wsconnected: boolean`**\
****    True if this instance is currently connected to the server.

**`wsProvider.wsconnecting: boolean`**\
****    True if this instance is currently connecting to the server.

**`wsProvider.shouldConnect: boolean`**\
****    If false, the client will not try to reconnect. &#x20;

**`wsProvider.bcconnected: boolean`**\
****    True if this instance is currently communicating to other browser-windows via [BroadcastChannel](https://developer.mozilla.org/en-US/docs/Web/API/BroadcastChannel).

**`wsProvider.synced: boolean`**\
****    True if this instance is currently connected and synced with the server.

**`wsProvider.disconnect()`**\
****    Disconnect from the server and don't try to reconnect.

**`wsProvider.connect()` **\
****    Establish a websocket connection to the websocket-server. Call this if you recently disconnected or if you set `wsOpts.connect = false`.

**`wsProvider.destroy()`**\
****    Destroy this `wsProvider` instance. Disconnects from the server and removes all event handlers.

**`wsProvider.on('sync', function(isSynced: boolean))`**\
****    Add an event listener for the `sync` event that is fired when the client received content from the server.

## Websocket Server:

Start a y-websocket server:

```bash
PORT=1234 npx y-websocket-server
```

Since npm symlinks the `y-websocket-server` executable from your local `./node_modules/.bin` folder, you can simply run npx. The `PORT` environment variable defaults to 1234.

### Websocket Server with Persistence

Persist document updates in a LevelDB database. See [LevelDB Persistence](../database-provider/y-leveldb.md) for more information.

```bash
PORT=1234 YPERSISTENCE=./dbDir node ./node_modules/y-websocket/bin/server.js
```

### Websocket Server with HTTP callback

Send a debounced callback to an HTTP server (`POST`) on document update.

Can take the following environment variables:

* `CALLBACK_URL` : Callback server URL
* `CALLBACK_DEBOUNCE_WAIT` : Debounce time between callbacks (in ms). Defaults to 2000 ms&#x20;
* `CALLBACK_DEBOUNCE_MAXWAIT` : Maximum time to wait before the callback. Defaults to 10 seconds
* `CALLBACK_TIMEOUT` : Timeout for the HTTP call. Defaults to 5 seconds
* `CALLBACK_OBJECTS` : JSON of shared objects to get data (`'{"SHARED_OBJECT_NAME":"SHARED_OBJECT_TYPE}'`)

```bash
CALLBACK_URL=http://localhost:3000/ CALLBACK_OBJECTS='{"prosemirror":"XmlFragment"}' npm start
```

This sends a debounced callback to `localhost:3000` 2 seconds after receiving an update (default `DEBOUNCE_WAIT`) with the data of an XmlFragment named `"prosemirror"` in the body.

### Scaling

These are mere suggestions on how you could scale your server environment. You can use the y-websocket server implementation as a baseline to implement your own scaling approach.

**Option 1:** Websocket servers communicate with each other via a PubSub server. A room is represented by a PubSub channel. The downside of this approach is that the same shared document may be handled by many servers. But the upside is that this approach is fault-tolerant, does not have a single point of failure, and is fit for route balancing.

**Option 2:** Sharding with _consistent hashing_. Each document is handled by a unique server. This pattern requires an entity, like etcd, that performs regular health checks and manages servers. Based on the list of available servers (which is managed by etcd) a proxy calculates which server is responsible for each requested document. The disadvantage of this approach is that load distribution may not be fair. Still, this approach may be the preferred solution if you want to store the shared document in a database - e.g. for indexing.

{% content-ref url="../database-provider/y-redis.md" %}
[y-redis.md](../database-provider/y-redis.md)
{% endcontent-ref %}

