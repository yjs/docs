---
description: Implementation Details - The inner working of Yjs
---

# Internals

### CRDT Paper

Yjs is a CRDT implementation. It implements an adaptation of the YATA CRDT with improved runtime performance.

{% embed url="https://www.researchgate.net/publication/310212186_Near_Real-Time_Peer-to-Peer_Shared_Editing_on_Extensible_Data_Types" %}

### Implementation Details

Choosing efficient data structures is critical when implementing a CRDT. The following document gives an overview of the data structures used in Yjs.

{% embed url="https://github.com/yjs/yjs/blob/main/INTERNALS.md" %}

### Internals Visualization

Visualization of different CRDT algorithms (including Yjs/YATA and Automerge/RGA).

{% embed url="https://text-crdt-compare.surge.sh/" %}

### Optimizations Overview

JavaScript manages memory automatically using a garbage collection approach. Yjs is a particularly efficient implementation of the YATA CRDT that works well in the browser and in NodeJS. This article analyzes the performance of Yjs in JavaScript.&#x20;

{% embed url="https://blog.kevinjahns.de/are-crdts-suitable-for-shared-editing" %}

### Codebase Walkthrough

{% embed url="https://youtu.be/0l5XgnQ6rB4" %}
