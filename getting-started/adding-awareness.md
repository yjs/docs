---
description: Propagating awareness information such as presence &  cursor locations.
---

# Awareness & Presence

Awareness features are an integral part of collaborative applications. In the last chapter, we made an editor collaborative by syncing content among all users. But we can communicate more information to help our users work together. By sharing cursor locations and presence information, we help our users to actively work together. Most applications also assign a unique name and color to each user. This kind of information can be generally classified as "Awareness" information. It gives you hints about what other users are currently doing. 

We could share even more awareness information like the mouse position of each user, or a live video recording of each user. But when we share too much information, we distract our users from the task at hand. So it is important to find the right balance, that makes sense for your application.

{% hint style="info" %}
Sharing no awareness information at all is also an option. Then skip this chapter, or come back later. 
{% endhint %}

Awareness information isn't stored in the Yjs document, as it doesn't need to be persisted across sessions. Instead, we use a tiny state-based Awareness CRDT that propagates JSON objects to all users. When you go offline, your own awareness state is automatically deleted and all users are notified that you went offline. While this feature is optional, it is recommended that network providers implement the awareness protocol to make it easier to switch providers. All our network providers implement the Awareness CRDT. 

In this part of the tutorial we use the Awareness CRDT to render remote cursor locations in the Quill editor. Furthermore, we implement a basic interface to render a list of remote users and assign them a user-name that can be updated in real-time.

## Quick start: Awareness CRDT

The following example shows how you retrieve the Awareness CRDT from the network provider. Then we can set some properties that are propagated to all users. We define the`"user"` property to set the name and preferred color for the current user. 

```javascript
// All of our network providers implement the awareness crdt
const awareness = provider.awareness

// You can observe when a any user updated their awareness information
awareness.on('change', changes => {
  // Whenever somebody updates their awareness information,
  // we log all awareness information from all users.
  console.log(Array.from(awareness.getStates().values()))
})

// You can think of your own awareness information as a key-value store.
// We update our "user" field to propagate relevant user information.
awareness.setLocalAwarenessField('user', {
  // Define a print name that should be displayed
  name: 'Emmanuelle Charpentier',
  // Define a color that should be associated to the user:
  color: '#ffb61e' // should be a hex color
})
```

The fields of the Awareness CRDT are not standardized. You can set any JSON-encodable value. The editor bindings commonly use the `"cursor"` field to communicate cursor locations. They use the `"user"` field to render the cursor object in a unique color, with the user name above the cursor location.

![Example of y-quill using different colors.](../.gitbook/assets/awareness-cursors-small.png)

All editor bindings that support rendering cursor information accept an awareness instance to render cursor information. If the `"user"` awareness field is unspecified, then the editor binding will render the cursor in a default color using a random user name.

```javascript
const binding = new QuillBinding(ytext, quill, provider.awareness)
```

For now, we have covered everything we need to know to continue with our tutorial. The complete API documentation for the Awareness CRDT is available in a separate section:

{% page-ref page="../api/about-awareness.md" %}

## Adding common awareness features to our collaborative editor

In the below live-code example, I added common awareness features to our editor project. It assigns a random color and a random user-name to each user using the [`do_username`](https://www.npmjs.com/package/do_username) npm package. Furthermore, it renders all present users in a list in their respective colors.

{% embed url="https://stackblitz.com/edit/y-quill-awareness" %}

{% embed url="https://stackblitz.com/edit/y-quill-awareness" %}





