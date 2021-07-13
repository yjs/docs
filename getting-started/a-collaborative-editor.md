---
description: A five minute guide to make an editor collaborative
---

# A Collaborative Editor

Yjs is a modular framework for syncing things in real-time - like editors!

This guide will walk you through the main concepts of Yjs. First, we are going to create a collaborative editor and sync it with clients. You will get introduced to Yjs documents and to providers, that allow you to sync through different network protocols. Next, we talk about [Awareness & Presence](adding-awareness.md) which are very important aspects of collaborative software. I created a separate section for [Offline Support](allowing-offline-editing.md) that shows you how to create offline-ready applications by just adding a few lines of code. The last section is an in-depth guide to [Shared Types](working-with-shared-types.md).

{% hint style="info" %}
If you are impatient jump to the live demo at the bottom of the page 😉
{% endhint %}

Let's get started by deciding on an editor to use.  Yjs doesn't ship with a customized editor. There are already a lot of awesome open-source editors projects out there. Yjs supports many of them using extensions. Editor bindings are a concept in Yjs that allow us to bind the state of a third-party editor to a synceable Yjs document. This is a list of all known editor bindings:

{% page-ref page="../ecosystem/editor-bindings/" %}

For the purpose of this guide, we are going to use the [Quill](https://quilljs.com/) editor - a great rich-text editor that is easy to setup. For a complete reference on how to setup Quill I refer to [their documentation](https://quilljs.com/playground/).

{% tabs %}
{% tab title="JavaScript" %}
```javascript
import Quill from 'quill'

const quill = new Quill(document.querySelector('#editor'), {
  modules: {
    cursors: true,
    toolbar: [
      // adding some basic Quill content features
      [{ header: [1, 2, false] }],
      ['bold', 'italic', 'underline'],
      ['image', 'code-block']
    ],
    history: {
      // Local undo shouldn't undo changes
      // from remote users
      userOnly: true
    }
  },
  placeholder: 'Start collaborating...',
  theme: 'snow' // 'bubble' is also great
})
```
{% endtab %}

{% tab title="HTML" %}
```markup
<link href="https://cdn.quilljs.com/1.3.6/quill.snow.css" rel="stylesheet">

<div id="editor" />
```
{% endtab %}

{% tab title="Install" %}
```bash
npm i quill
```
{% endtab %}
{% endtabs %}

Next, we are going to install Yjs and the [y-quill](../ecosystem/editor-bindings/quill.md) editor binding.

```bash
npm i yjs y-quill
```

```javascript
import * as Y from 'yjs'
import { QuillBinding } from 'y-quill'

// A Yjs document holds the shared data
const ydoc = new Y.Doc()
// Define a shared text type on the document
const ytext = ydoc.getText('quill')

// Create an editor-binding which
// "binds" the quill editor to a Y.Text type.
const binding = new QuillBinding(ytext, quill)
```

The `ytext` object is a shared data structure for representing text. It also supports formatting attributes \(i.e. **bold** and _italic_\). Yjs automatically resolves concurrent changes on shared data so we don't have to worry about conflict resolution anymore. Then we synchronize `ytext` with the `quill` editor and keep them in-sync using the `QuillBinding`. Almost all editor bindings work like this. You can simply exchange the editor binding if you switch to another editor.

But don't stop here, the editor doesn't sync to other clients yet! We need to choose a **provider** or [implement our own communication protocol](../tutorials/creating-a-custom-provider.md) to exchange document updates with other peers.

{% page-ref page="../ecosystem/connection-provider/" %}

Each provider has pros and cons. The[ y-webrtc](../ecosystem/connection-provider/y-webrtc.md) provider connects clients directly with each other and is a perfect choice for demo applications because it doesn't require you to set up a server. But for a real-world application, you often want to sync the document to a server. In any case, we got you covered. It is easy to change the provider because they all implement the same interface.

{% tabs %}
{% tab title="y-webrtc" %}
```javascript
import { WebrtcProvider } from 'y-webrtc'

const provider = new WebrtcProvider('quill-demo-room', ydoc)
```
{% endtab %}

{% tab title="y-websocket." %}
```javascript
import { WebsocketProvider } from 'y-websocket'

// connect to the public demo server (not in production!)
const provider = new WebsocketProvider(
  'wss://demos.yjs.dev', 'quill-demo-room', ydoc
)
```
{% endtab %}

{% tab title="y-dat" %}
```javascript
import { DatProvider } from 'y-dat'

// set null in order to create a fresh
const datKey = '7b0d584fcdaf1de2e8c473393a31f52327793931e03b330f7393025146dc02fb'
const provider = new DatProvider(datKey, ydoc)
```
{% endtab %}

{% tab title="Installation" %}
```bash
npm i y-webrtc # or
npm i y-websocket # or
npm i y-dat
```
{% endtab %}
{% endtabs %}

Providers work similarly to editor bindings. They sync Yjs documents through a communication protocol or a database. Most providers have in common that they use the concept of room-names to connect Yjs documents. In the above example, all documents that specify `'quill-demo-room'` as the room-name will sync.

{% hint style="info" %}
**Providers are meshable.** You can connect multiple providers to a Yjs instance at the same time. Document updates will automatically sync through the different communication channels. Meshing providers can improve reliability through redundancy and decrease network delay.
{% endhint %}

By combining Yjs with providers and editor bindings we created our first collaborative editor. In the following sections, we will explore more Yjs concepts like awareness, shared types, and offline editing.

But for now, let's enjoy what we built. I included the same fiddle twice so you can observe the editors sync in real-time. Aware, the editor content is synced with all users visiting this page!

{% embed url="https://stackblitz.com/edit/y-quill?embed=1&file=index.ts&hideExplorer=1" %}

{% embed url="https://stackblitz.com/edit/y-quill?embed=1&file=index.ts&hideExplorer=1" %}



