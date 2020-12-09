---
description: API documentation of the Awareness CRDT
---

# Awareness

Awareness is an optional feature that works well together with Yjs. As I described in [Awareness & Presence](../../getting-started/adding-awareness.md), the feature is not part of the `yjs` module. It is defined in [y-protocols](https://github.com/yjs/y-protocols) and is usually implemented by the providers. If you want to implement your custom provider, you can use the Awareness CRDT, or implement a custom protocol. It's up to you.

{% embed url="https://github.com/yjs/y-protocols" caption="y-protocols repository" %}

#### Awareness CRDT

```javascript
import * as awarenessProtocol from 'y-protocols/awareness.js'
```

The Awareness protocol implements a simple network agnostic CRDT that manages user status \(who is online?\) and propagate awareness information like cursor location, username, or email address. Each client can update its own local state and listen to state changes of remote clients.

Each client has an awareness state. Remote awareness states are stored in a Map that maps from remote client id to an awareness state. An _awareness state_ is an increasing clock attached to a schemaless JSON object.

Whenever the client changes its local state, it increases the clock and propagates its own awareness state to all peers. When a client receives a remote awareness state and overwrites the client's state if the received state is newer than the local state of that client. If the state is `null`, the client is marked as offline. If a client doesn't receive updates from a remote peer for 30 seconds, it marks the remote client as offline. Hence each client must broadcast its own awareness state in a regular interval to make sure that remote clients don't mark it as offline.

### **Awareness API**

```javascript
awareness = new awarenessProtocol.Awareness(ydoc)

// It is usually created & maintained by the provider
awareness = provider.awareness
```

**`awareness = new awarenessProtocol.Awareness(ydoc: Y.Doc)`**  
    Create a new awareness instance.

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
    Listen to remote and local state changes. Get notified when a state is either added, updated, or removed.

**`awareness.on('change', ({ added: Array, updated: Array removed: Array }, [transactionOrigin:any]) => ..)`**  
    Listen to remote and local awareness changes. This event is called even when the awareness state does not change but is only updated to notify other users that this client is still online. Use this event if you want to propagate awareness state to other users.

### Example: Sync awareness with other clients

\[todo\]



  


