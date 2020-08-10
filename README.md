---
description: >-
  Modular building blocks for collaborative applications like Google Docs or
  Figma.
---

# Introduction

{% hint style="info" %}
This documentation website is a work in progress. The best source of information is still the [Yjs README](https://github.com/yjs/yjs) and the [yjs-demos](https://github.com/yjs/yjs-demos) repository.
{% endhint %}

Yjs is a high-performance [CRDT](https://en.wikipedia.org/wiki/Conflict-free_replicated_data_type) for building collaborative applications that sync automatically.

It exposes its internal CRDT model as _shared types_. Shared types are just common data types like `Map` or `Array`. They can be manipulated like regular data types, they fire events when changes happen, and you can always merge them without merge conflicts. Always!

## Editor Support

Yjs supports many of the most popular text and rich-text editors. And the list keeps growing!

{% page-ref page="ecosystem/editor-bindings/prosemirror.md" %}

{% page-ref page="ecosystem/editor-bindings/tiptap.md" %}

{% page-ref page="ecosystem/editor-bindings/monaco.md" %}

{% page-ref page="ecosystem/editor-bindings/quill.md" %}

{% page-ref page="ecosystem/editor-bindings/codemirror.md" %}

{% page-ref page="ecosystem/editor-bindings/remirror.md" %}

## Network Agnostic 📡

Yjs doesn't make any assumption about the network technology you are using. As long as all changes eventually arrive, the documents will sync. The order in which document updates are applied doesn't matter.

You can integrate Yjs into your existing communication infrastructure, or use one of the [several existing network providers](ecosystem/connection-provider/) that allow you to jump-start your application backend.

Scaling shared editing backends is not trivial. Most shared editing solutions depend on a single source of truth - a central server - to perform conflict resolution. Since Yjs doesn't need a central source of truth, we can design the backend using ideas from distributed system architecture. In fact, Yjs can be scaled indefinitely as it is shown in the [y-redis section](tutorials/untitled-3.md).

And why not design an application using P2P technologies without sharing data with a server? Yjs is perfectly capable of doing that. We include several examples of how Yjs can be used without a backend and without any setup 

## Rich Ecosystem 🔥 

Yjs is a modular approach that allows the community to make any editor collaborative using any network technology. It has thought-through solutions for almost all shared-editing related problems.

We built a rich ecosystem of extensions around Yjs. There are ready-to-use editor integrations for many popular \(rich-\)text editors, adapters to different network technologies \(like WebRTC, WebSocket, or Hyper\), and persistence providers that store document updates in a database.

## Unmatched Performance🚀 

Yjs is the fastest CRDT implementation, by far.

{% embed url="https://github.com/dmonad/crdt-benchmarks" %}

## 



