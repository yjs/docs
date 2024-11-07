---
description: >-
  Modular building blocks for building collaborative applications like Google
  Docs and Figma.
---

# Introduction

{% hint style="info" %}
This documentation website is a work in progress. The best source of information is still the [Yjs README](https://github.com/yjs/yjs) and the [yjs-demos](https://github.com/yjs/yjs-demos) repository.
{% endhint %}

Yjs is a high-performance [CRDT](https://en.wikipedia.org/wiki/Conflict-free\_replicated\_data\_type) for building collaborative applications that sync automatically.

It exposes its internal CRDT model as _shared data types_ that can be manipulated concurrently. Shared types are similar to common data types like `Map` and `Array`. They can be manipulated, fire events when changes happen, and automatically merge without merge conflicts.

## Quick Start

This is a working example of how shared types automatically sync. We also have a [getting-started guide](getting-started/a-collaborative-editor.md), API documentation, and lots of [live demos with source code](https://github.com/yjs/yjs-demos).

```javascript
import * as Y from 'yjs'

// Yjs documents are collections of
// shared objects that sync automatically.
const ydoc = new Y.Doc()
// Define a shared Y.Map instance
const ymap = ydoc.getMap()
ymap.set('keyA', 'valueA')

// Create another Yjs document (simulating a remote user)
// and create some conflicting changes
const ydocRemote = new Y.Doc()
const ymapRemote = ydocRemote.getMap()
ymapRemote.set('keyB', 'valueB')

// Merge changes from remote
const update = Y.encodeStateAsUpdate(ydocRemote)
Y.applyUpdate(ydoc, update)

// Observe that the changes have merged
console.log(ymap.toJSON()) // => { keyA: 'valueA', keyB: 'valueB' }
```

## Editor Support

Yjs supports several popular text and rich-text editors. We are working with different projects to enable collaboration-support through Yjs.

{% content-ref url="ecosystem/editor-bindings/prosemirror.md" %}
[prosemirror.md](ecosystem/editor-bindings/prosemirror.md)
{% endcontent-ref %}

{% content-ref url="ecosystem/editor-bindings/tiptap2.md" %}
[tiptap2.md](ecosystem/editor-bindings/tiptap2.md)
{% endcontent-ref %}

{% content-ref url="ecosystem/editor-bindings/monaco.md" %}
[monaco.md](ecosystem/editor-bindings/monaco.md)
{% endcontent-ref %}

{% content-ref url="ecosystem/editor-bindings/quill.md" %}
[quill.md](ecosystem/editor-bindings/quill.md)
{% endcontent-ref %}

{% content-ref url="ecosystem/editor-bindings/codemirror.md" %}
[codemirror.md](ecosystem/editor-bindings/codemirror.md)
{% endcontent-ref %}

{% content-ref url="ecosystem/editor-bindings/remirror.md" %}
[remirror.md](ecosystem/editor-bindings/remirror.md)
{% endcontent-ref %}

## Network Agnostic 📡

Yjs doesn't make any assumptions about the network technology you are using. As long as all changes eventually arrive, the documents will sync. The order in which document updates are applied doesn't matter.

You can [integrate Yjs into your existing communication infrastructure](tutorials/creating-a-custom-provider.md) or use one of the [several existing network providers](ecosystem/connection-provider/) that allow you to jump-start your application backend.

Scaling shared editing backends is not trivial. Most shared editing solutions depend on a single source of truth - a central server - to perform conflict resolution. Yjs doesn't need a central source of truth. This enables you to design the backend using ideas from distributed system architecture. In fact, Yjs can be scaled indefinitely, as it is shown in the [y-redis section](tutorials/untitled-3.md).

If you don't want to maintain your own backend, a number of providers offer Yjs as a service, including [Liveblocks](https://liveblocks.io/yjs), [Y-Sweet](https://jamsocket.com/y-sweet), and [Tiptap](https://tiptap.dev/product/collaboration).

Yjs is truly network agnostic and can be used as a data model for decentralized and [Local-First software](https://www.inkandswitch.com/local-first.html).

Just start somewhere. Since the "network provider" is clearly separated from Yjs and the various integrations, it is pretty easy to switch to different providers.

## Rich Ecosystem 🔥

Yjs is a modular approach that allows the community to make any editor collaborative using any network technology. It has thought-through solutions for almost all shared-editing related problems.

We built a rich ecosystem of extensions around Yjs. There are ready-to-use editor integrations for many popular (rich-)text editors, adapters to different network technologies (like WebRTC, WebSocket, or Hyper), and persistence providers that store document updates in a database.

## Unmatched Performance🚀

Yjs is the fastest CRDT implementation by far.

{% embed url="https://github.com/dmonad/crdt-benchmarks" %}
