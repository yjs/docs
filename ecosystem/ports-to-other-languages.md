---
description: Yjs has been ported to other languages.
---

# Ports to other languages

{% embed url="https://github.com/yjs/ycs" %}
C# port of Yjs
{% endembed %}

## ygo

A pure Go port of Yjs. Wire-compatible with the JavaScript reference (V1/V2 update codecs, awareness, snapshot), with the full CRDT engine (YArray, YMap, YText with rich-text formatting and embeds, YXml\*), built-in WebSocket and HTTP sync transports, and an UndoManager. In production at Re:Earth.

{% embed url="https://github.com/reearth/ygo" %}
Go port of Yjs
{% endembed %}

## ygo (Deln0r/ygo)

Pure-Go port of Yjs. Reads and writes the V1 and V2 update formats byte for byte with yjs@13.6.33, checked in both directions by 307 cross-language fixture scenarios; the README lists the few cases that differ. Includes a y-websocket-compatible WebSocket server with SQLite persistence, a Go sync client for y-websocket and Hocuspocus servers, UndoManager, and iOS/Android bindings via gomobile. No CGO.

{% embed url="https://github.com/Deln0r/ygo" %}
Go port of Yjs
{% endembed %}

### Work in progress

{% embed url="https://github.com/yjs/yrs" %}
Rust port of Yjs (WIP)
{% endembed %}

{% embed url="https://github.com/yjs/ywasm" %}
Wasm port of Yjs (WIP)
{% endembed %}

