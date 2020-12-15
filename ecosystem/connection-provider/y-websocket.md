---
description: WebSocket Provider for Yjs that ships with a extendable server implementation
---

# y-websocket

The Websocket Provider implements a classical client-server model. Clients connect to a single endpoint over Websocket. The server distributes awareness information and document updates among clients.

The Websocket Provider is a solid choice if you want a central source that handles authentication and authorization. Websockets also send header information and cookies, so you can use existing authentication mechanisms with this server.

* Supports cross-tab communication. When you open the same document in the same browser, changes on the document are exchanged via cross-tab communication \([Broadcast Channel](https://developer.mozilla.org/en-US/docs/Web/API/Broadcast_Channel_API) and [localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) as fallback\).
* Supports the exchange of awareness information \(e.g. cursors\).

{% embed url="https://github.com/yjs/y-websocket" caption="" %}

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

\[todo\]

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

Send a debounced callback to an HTTP server \(`POST`\) on document update.

Can take the following ENV variables:

* `CALLBACK_URL` : Callback server URL
* `CALLBACK_DEBOUNCE_WAIT` : Debounce time between callbacks \(in ms\). Defaults to 2000 ms 
* `CALLBACK_DEBOUNCE_MAXWAIT` : Maximum time to wait before callback. Defaults to 10 seconds
* `CALLBACK_TIMEOUT` : Timeout for the HTTP call. Defaults to 5 seconds
* `CALLBACK_OBJECTS` : JSON of shared objects to get data \(`'{"SHARED_OBJECT_NAME":"SHARED_OBJECT_TYPE}'`\)

```bash
CALLBACK_URL=http://localhost:3000/ CALLBACK_OBJECTS='{"prosemirror":"XmlFragment"}' npm start
```

This sends a debounced callback to `localhost:3000` 2 seconds after receiving an update \(default `DEBOUNCE_WAIT`\) with the data of an XmlFragment named `"prosemirror"` in the body.

### Scaling

\[todo\]

{% page-ref page="../database-provider/y-redis.md" %}



