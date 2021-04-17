---
description: API documentation of the Awareness CRDT
---

# Awareness

Awareness is an optional feature that works well together with Yjs. As I described in [Awareness & Presence](../getting-started/adding-awareness.md), the feature is not part of the `yjs` module. It is defined in [y-protocols](https://github.com/yjs/y-protocols) and is usually implemented by the providers. If you want to implement your custom provider, you can use the Awareness CRDT, or implement a custom protocol. It's up to you.

If you haven't already, you should read the getting-started guide on awareness.

{% page-ref page="../getting-started/adding-awareness.md" %}

## Awareness CRDT

```javascript
import * as awarenessProtocol from 'y-protocols/awareness.js'
```

The awareness protocol implements a simple network agnostic CRDT that manages user status \(who is online?\) and propagate awareness information like cursor location, username, or email address. Each client can update its own local state and listen to state changes of remote clients.

Each client has an awareness state. Remote awareness states are stored in a Map that maps from remote client id to an awareness state. An _awareness state_ is an increasing clock attached to a schemaless JSON object.

Whenever the client changes its local state, it increases the clock and propagates its own awareness state to all peers. When a client receives a remote awareness state and overwrites the client's state if the received state is newer than the local state of that client. If the state is `null`, the client is marked as offline. If a client doesn't receive updates from a remote peer for 30 seconds, it marks the remote client as offline. Hence each client must broadcast its own awareness state in a regular interval to make sure that remote clients don't mark it as offline.

### **Awareness CRDT API**

```javascript
awareness = new awarenessProtocol.Awareness(ydoc)

// It is usually created & maintained by the provider
awareness = provider.awareness
```

**`awareness = new awarenessProtocol.Awareness(ydoc: Y.Doc)`**  
    Create a new awareness instance.

**`awareness.clientID: number`**  
    A unique identifier that identifies this client. 

**`awareness.destroy()`**  
    Destroy the awareness instance and all associated state and event-handlers.

**`awareness.getLocalState(): Object<string, any> | null`**  
    Get the local awareness state.

**`awareness.setLocalState(state: Object<string, any>)`**  
    Set or update the local awareness state. Set `null` to mark the local client as offline. The `state` object must be a key-value store that maps to JSON-encodable values.

**`awareness.setLocalStateField(string, any)`**  
    Only set or update a single key-value pair in the local awareness state.

**`awareness.getStates(): Map<string, Object<string, any>>`**  
    Get all awareness states \(remote and local\). Maps from `clientID` to awareness state. The clientID is usually the `ydoc.clientID`.

**`awareness.on('update', ({ added: Array, updated: Array removed: Array }, [transactionOrigin:any]) => ..)`**  
    Listen to remote and local awareness changes. This event is called even when the awareness state does not change but is only updated to notify other users that this client is still online. Use this event if you want to propagate awareness state to other users.

**`awareness.on('change', ({ added: Array, updated: Array removed: Array }, [transactionOrigin:any]) => ..)`**  
    Listen to remote and local state changes. Get notified when a state is either added, updated, or removed.

## Awareness Protocol

The awareness protocol is implemented by most providers. It allows you to use the Awareness CRDT to propagate presence and awareness information. Although it is not a requirement, it is recommended that all providers that interact with Yjs implement this protocol. If you want to implement the awareness protocol into your custom provider, this section is for you.

### Awareness Protocol API

**`awarenessProtocol.encodeAwarenessUpdate(awareness: Awareness, clients: Array<number>): Uint8Array`**  
    Encode the awareness states of the specified clients into an update encoded as `Uint8Array`.

**`awarenessProtocol.applyAwarenessUpdate(awareness: Awareness, update: Uint8array)`**  
    Apply an awareness update created with `encodeAwarenessUpdate` to an instance of the Awareness CRDT.

**`awarenessProtocol.removeAwarenessStates(awareness: Awareness, clients: Array<number>, origin: any)`**  
    Remove the awareness states of the specified clients. This will call the `update` and the `change` event handler of the Awareness CRDT. Sometimes you want to mark yourself or others as offline. As soon as you know that a client is offline, you should call this function. It is not part of the Awareness CRDT, because it should only be used by the provider that implements awareness.

### Adding Awareness Support to a Provider

Awareness CRDT updates work similarly to Yjs updates. First, you sync with a client. Then you exchange incremental updates with that client using the `update` event. The only difference is that the Awareness CRDT doesn't support _state vectors_ to exchange a minimal amount of information. That only makes things easier and has little performance impact because awareness states are usually pretty small.

```javascript
// Encode the complete Awareness state
const encodedAwState = awarenessProtocol.encodeAwarenessUpdate(
  provider.awareness,
  Array.from(provider.awareness.getStates().keys())
)
// Make sure to share the complete awareness state whenever you
// connect to a new client. Similarly to the Yjs CRDT, it doesn't
// matter if the remote client receives the same state-updates several
// times. What is important is that the state is distributed.

// Whenever the local state changes, communicate that change to all connected clients
awareness.on('update', ({ added, updated, removed }) => {
  const changedClients = added.concat(updated).concat(removed)
  broadcastAwarenessMessage(awarenessProtocol.encodeAwarenessUpdate(awareness, changedClients))
})
```

That's basically it. Here are just a few small tricks that I like to implement in providers:

When you know that a client will disconnect, you might as well send a small update to other users to let them know that you went offline. It is not strictly necessary, because the Awareness CRDT will notice that you are offline after a timeout. But you can at least try:

```javascript
window.addEventListener('beforeunload', () => {
  awarenessProtocol.removeAwarenessStates(
    this.awareness, [doc.clientID], 'window unload'
  )
})
```

When your connection already broke up, you should mark all remote clients as offline. That just makes sense to the user that is using your software.

```javascript
websocket.onclose = () => {
  // mark everyone but the current client as offline
  awarenessProtocol.removeAwarenessStates(
    provider.awareness,
    Array.from(provider.awareness.getStates().keys())
      .filter(client => client !== provider.doc.clientID),
    'connection closed'
  )
}
```

When you get lost, you should have a look at one of the existing providers that implement the awareness protocol. The y-websocket is a fairly simple provider and might be a good starting point for you.

{% embed url="https://github.com/yjs/y-websocket/" caption="" %}

