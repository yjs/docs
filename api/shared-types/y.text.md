---
description: A shared type that represents Text & RichText
---

# Y.Text



```javascript
import * as Y from 'yjs'

const ydoc = new Y.Doc()

// You can define a Y.Text as a top-level type or a nested type

// Method 1: Define a top-level type
const ytext = ydoc.getText('my text type') 
// Method 2: Define Y.Text that can be included into the Yjs document
const ytextNested = new Y.Text()

// Nested types can be included as content into any other shared type
ydoc.getMap('another shared structure').set('my nested text', ytextNested)

// Common methods
ytext.insert(0, 'abc') // insert three elements
ytext.format(1, 2, { bold: true }) // delete second element 
ytext.toString() // => 'abc'
ytext.toDelta() // => [{ insert: 'a' }, { insert: 'bc', attributes: { bold: true }}]
```

## API

**`ytext = new Y.Text(initialContent): Y.Text`**  
    Create an instance of Y.Text with existing content.

**`ytext.doc: Y.Doc | null`** \(readonly\)  
    The Yjs document that this type is bound to. Is `null` when it is not bound yet.

**`ytext.parent: Y.AbstractType | null`** \(readonly\)  
    The parent that holds this type. Is `null` if this `ytext` is a top-level type.

**`ytext.length: number`** \(readonly\)  
    The length of the string in UTF-16 code units. Since JavaScripts' String implementation uses the same character encoding `ytext.toString().length === ytext.length`.

**`ytext.insert(index: number, content: string[, format: Object<string,any>])`**  
    Insert content at a specified `index`. Optionally, you may specify formatting attributes that are applied to the inserted string. By default, the formatting attributes before the insert position will be used.

**`ytext.format(index: number, length: number, format: Object<string,any>)`**  
    Assign formatting attributes to a range of text.

**`ytext.applyDelta(delta: Delta)`**  
    Apply a Text-Delta to the Y.Text instance.

**`ytext.delete(index: number, length: number)`**  
    Delete `length` characters starting from `index`.

**`ytext.toString(): string`**  
    Retrieve the string-representation \(without formatting attributes\) from the Y.Text instance.

**`ytext.toDelta(): Delta`**  
    Retrieve the Text-Delta-representation of the Y.Text instance. The Text-Delta is equivalent to [Quills' Delta format](https://quilljs.com/docs/delta/).

**`ytext.toJSON(): string`**  
    Retrieves the string representation of the Y.Text instance.

**`ytext.clone(): Y.Text`**  
    Clone this type into a fresh Y.Text instance. The returned type can be included into the Yjs document.

**`ytext.observe(function(YTextEvent, Transaction))`**  
    Registers a change observer that will be called synchronously every time this shared type is modified. In the case this type is modified in the observer call, the event listener will be called again after the current event listener returns.

**`ytext.unobserve(function)`**  
    Unregisters a change observer that has been registered with `ytext.observe`.

**`ytext.observeDeep(function(Array<Y.Event>, Transaction))`**  
    Registers a change observer that will be called synchronously every time this type or any of its children is modified. In the case this type is modified in the event listener, the event listener will be called again after the current event listener returns. The event listener receives all Events created by itself or any of its children.

**`ytext.unobserveDeep(function)`**  
    Unregisters a change observer that has been registered with `ytext.observeDeep`.

## Delta Format

\[todo\]

\[formatting attributes\]

## Observing changes: Y.TextEvent

\[todo\]

```javascript
yarray.observe(yarrayEvent => {
  yarrayEvent.target === yarray // => true

  // Find out what changed: 
  // Option 1: A set of keys that changed
  ymapEvent.keysChanged // => Set<strings>
  // Option 2: Compute the differences
  ymapEvent.changes.keys // => Map<string, { action: 'add'|'update'|'delete', oldValue: any}>
  
  // sample code.
  yarrayEvent.changes.keys.forEach((change, key) => {
    if (change.action === 'add') {
      console.log(`Property "${key}" was added. Initial value: "${ymap.get(key)}".`)
    } else if (change.action === 'update') {
      console.log(`Property "${key}" was updated. New value: "${ymap.get(key)}". Previous value: "${change.oldValue}".`)
    } else if (change.action === 'delete') {
      console.log(`Property "${key}" was deleted. New value: undefined. Previous value: "${change.oldValue}".`)
    }
  })
})

ymap.set('key', 'value') // => Property "key" was added. Initial value: "value".
ymap.set('key', 'new') // => Property "key" was updated. New value: "new". Previous value: "value".
ymap.delete('key') // => Property "key" was deleted. New value: undefined. Previous Value: "new".

```

### Y.TextEvent API

See [Y.Event](../y.event.md) API. The API is inherited from Y.Event.I'm still in the process of moving the documentation to this place. For now, you can find the API docs in the README:





