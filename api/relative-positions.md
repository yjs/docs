---
description: A powerful position encoding that transforms back to index positions
---

# Y.RelativePosition

When working with collaborative documents, we often need to work with positions. Positions may represent cursor locations, selection ranges, or even assign a comment to a range of text. Normal index-positions (expressed as integers) are not convenient to use because the index-range is invalidated as soon as a remote change manipulates the document. Relative positions give you a powerful API to express positions.

A _relative position_ is fixated to an element in the shared document and is not affected by remote changes. I.e. given the document `"a|c"`, the relative position is attached to `c`. When a remote user modifies the document by inserting a character before the cursor, the cursor will stay attached to the character `c`. `insert(1, 'x')("a|c") = "ax|c"`. When the _relative position_ is set to the end of the document, it will stay attached to the end of the document.

{% hint style="info" %}
Relative positions are guaranteed to always point to the same location ⇒ When all clients sync up, all relative positions will translate to the same index-position. This is not possible in OT-like solutions [(explanation)](https://marijnhaverbeke.nl/blog/collaborative-editing-cm.html).
{% endhint %}

## Y.RelativePosition API

**`Y.createRelativePositionFromTypeIndex(type: Y.AbstractType, index: number [, assoc=0]): Y.RelativePosition`**\
****    Create a relative position fixated to the i-th element in any sequence-like shared type (if `assoc >= 0`). By default, the position associates with the character that comes after the specified index position. If `assoc < 0`, then the relative position associates with the character before the specified index position.&#x20;

**`Y.createAbsolutePositionFromRelativePosition(Y.RelativePosition, Y.Doc): { type: Y.AbstractType, index: number, assoc: number } | null`**\
&#x20;   Create an absolute position from a relative position. If the relative position cannot be referenced, or the type is deleted, then the result is null.

**`Y.encodeRelativePosition(Y.RelativePosition): Uint8Array`**\
&#x20;   Encode a relative position to an Uint8Array. Binary data is the preferred encoding format for [document updates](document-updates.md). If you prefer JSON encoding, you can simply `JSON.stringify` / `JSON.parse` the relative position instead.

**`Y.decodeRelativePosition(Uint8Array): RelativePosition`**\
&#x20;   Decode a binary-encoded relative position to a RelativePositon object.

### **Example: Transform to RelativePosition and back**

```javascript
const relPos = Y.createRelativePositionFromTypeIndex(ytext, 2)
const pos = Y.createAbsolutePositionFromRelativePosition(relPos, doc)
pos.type === ytext // => true
pos.index === 2 // => true
```

### **Example: Send relative position to a remote client (JSON)**

```javascript
const relPos = Y.createRelativePositionFromTypeIndex(ytext, 2)
const encodedRelPos = JSON.stringify(relPos)
// send encodedRelPos to remote client..
const parsedRelPos = JSON.parse(encodedRelPos)
const pos = Y.createAbsolutePositionFromRelativePosition(parsedRelPos, remoteDoc)
pos.type === remoteytext // => true
pos.index === 2 // => true
```

### **Example: Send relative position to a remote client (Uint8Array)**

```javascript
const relPos = Y.createRelativePositionFromTypeIndex(ytext, 2)
const encodedRelPos = Y.encodeRelativePosition(relPos)
// send encodedRelPos to remote client..
const parsedRelPos = Y.decodeRelativePosition(encodedRelPos)
const pos = Y.createAbsolutePositionFromRelativePosition(parsedRelPos, remoteDoc)
pos.type === remoteytext // => true
pos.index === 2 // => true
```
