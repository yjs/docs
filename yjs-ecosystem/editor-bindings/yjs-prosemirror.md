---
description: Shared Editing with the ProseMirror Editor
---

# ProseMirror

[ProseMirror](https://prosemirror.net/) is a fantastic toolkit to build your own richtext editor. [TipTap](yjs-tiptap.md), [Remirror](remirror.md), and [Atlaskit ](https://atlaskit.atlassian.com/packages/editor/editor-core/example/full-page)are all based on ProseMirror. The [y-prosemirror](https://github.com/yjs/y-prosemirror/) module exports ProseMirror plugins that make any ProseMirror-based editor collaborative. The module even ensures that the ProseMirror document model adheres to the schema. The following demo shows how shared editing, cursors, shared undo/redo, and versions can be implemented using the ProseMirror toolkit.

{% embed url="https://github.com/yjs/y-prosemirror/" caption="" %}



{% embed url="https://stackblitz.com/edit/y-prosemirror?embed=1&file=index.ts&hideExplorer=1" %}

{% embed url="https://stackblitz.com/edit/y-prosemirror?embed=1&file=index.ts&hideExplorer=1" %}

## Caveats

Index positions don't work as expected in ProseMirror if you use this module. Instead of indexes, you should use [relative positions](../../api/relative-positions.md) that are based on the Yjs document. Relative positions always point to the place where you originally put them \(relatively speaking\). In peer-to-peer editing, it is impossible to transform index positions so that everyone ends up with the same positions. 

Features such as comments should either be implemented as document state or using relative positions.

A relevant discussion to this topic is found in the ProseMirror discussion board:

{% embed url="https://discuss.prosemirror.net/t/offline-peer-to-peer-collaborative-editing-using-yjs/2488" %}

## Versions and showing the differences

This is still a bit experimental, but you can create versions \(snapshots of the current state of the document\) and show the differences between versions. When we allow offline editing, it is important to show to the user the changes that happened while he was away \(a diff of changes\). The user can then resolve any potential conflicts. A basic example of how versions can be implemented is shown in the [demos repository](https://github.com/yjs/yjs-demos/tree/master/prosemirror-versions).

{% embed url="https://demos.yjs.dev/prosemirror-versions/prosemirror-versions.html" caption="Source code: https://github.com/yjs/yjs-demos/tree/master/prosemirror-versions" %}

In the basic versions example, we disable garbage collection of deleted content because we want to preserve the deleted strings. This is a problem that is mostly overcome, and you can play with the approach that I implemented on yjs.dev.

In peer-to-peer shared editing, there is no linear history of edits. I suggest that every user preserves its own history of versions. For example, the user could create a version before it receives changes. Another approach is to create versions regularly or let the user handle them. You can share the versions using Yjs types, but this would enforce every user to preserve the complete editing history, although these users might not be interested in all versions.

The yjs.dev website has a ProseMirror example that shows that versions work even with a lot of collaborators. The document has been online since February 2020 and still doesn't slow down. Only content that is relevant to the local version history is preserved.

{% embed url="https://yjs.dev/" %}



