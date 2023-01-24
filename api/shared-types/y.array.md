---
description: A shared type to store data in a sequence-like data structure
---

# Y.Array



```javascript
import * as Y from 'yjs'

const ydoc = new Y.Doc()

// You can define a Y.Array as a top-level type or a nested type

// Method 1: Define a top-level type
const yarray = ydoc.getArray('my array type') 
// Method 2: Define Y.Array that can be included into the Yjs document
const yarrayNested = new Y.Array()

// Nested types can be included as content into any other shared type
yarray.set('my nested array', yarrayNested)

// Common methods
yarray.insert(0, [1, 2, 3]) // insert three elements
yarray.delete(1, 1) // delete second element 
yarray.toArray() // => [1, 3]
```

## API

**`Y.Array.from(Array<JSON | Uint8Array | Y.AbstractType>): Y.Array`**\
&#x20;   An alternative factory function to create a Y.Array based on existing content.

**`yarray.doc: Y.Doc | null`** (readonly)\
&#x20;   The Yjs document that this type is bound to. Is `null` when it is not bound yet.

**`yarray.parent: Y.AbstractType | null`**\
&#x20;   The parent that holds this type. Is `null` if this `yarray` is a top-level type.

**`yarray.length: number`**\
&#x20;   The number of elements that this Y.Array holds.

**`yarray.insert(index: number, content: Array<JSON | Uint8Array | Y.AbstractType>)`**\
&#x20;   Insert content at a specified `index`. Note that - for performance reasons - content is always an array of elements. I.e. `yarray.insert(0, [1])` inserts 1 at position 0.

**`yarray.delete(index: number, length: number)`**\
&#x20;   Delete `length` Y.Array elements starting from `index`.

**`yarray.push(content: Array<JSON | Uint8Array | Y.AbstractType>)`**\
&#x20;   Append content at the end of the Y.Array. Same as `yarray.insert(yarray.length, content)`.

**`yarray.unshift(content: Array<JSON | Uint8Array | Y.AbstractType>)`**\
&#x20;   Prepend content to the beginning of the Y.Array. Same as `yarray.insert(0, content)`.

**`yarray.get(index: number): JSON | Uint8Array | Y.AbstractType`**\
&#x20;   Retrieve the n-th element.

**`yarray.slice([start: number [, end: number]]): Array<JSON | Uint8Array | Y.AbstractType>`**\
&#x20;   Retrieve a range of content starting from index `start` (inclusive) to index `end` (exclusive). Negative indexes can be used to indicate offsets from the end of the Y.Array. I.e. `yarray.slice(-1)` returns the last element. `yarray.slice(0, -1)` returns all but the last element. Works similarly to the [Array.slice](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Array/slice) method.

**`yarray.toJSON(): Array<JSON | Uint8Array>`**\
&#x20;   Retrieve the JSON representation of this type. The result is a fresh Array that contains all Y.Array elements. Elements that are shared types are transformed to JSON as well, using their `toJSON` method. The result may contain Uint8Arrays which are not JSON-encodable.

**`yarray.forEach(function(value: any, index: number, yarray: Y.Array))`**\
&#x20;   Execute the provided function once on every element.

**`yarray.map(function(value: any, index: number, yarray: Y.Array): T): Array<T>`**\
&#x20;   Creates a new Array filled with the results of calling the provided function on each element in the Y.Array.

**`yarray[Symbol.Iterator]: Iterator`**\
&#x20;   Returns an [Iterator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration\_protocols) of values for the Y.Array. This allows you to iterate over the `yarray` using a for..of loop: `for (const value of yarray) { .. }`

**`yarray.clone(): Y.Array`**\
&#x20;   Clone all values into a fresh Y.Array instance. The returned type can be included into the Yjs document.

**`yarray.observe(function(YArrayEvent, Transaction))`**\
&#x20;   Registers a change observer that will be called synchronously every time this shared type is modified. In the case this type is modified in the observer call, the event listener will be called again after the current event listener returns.

**`yarray.unobserve(function)`**\
&#x20;   Unregisters a change observer that has been registered with `yarray.observe`.

**`yarray.observeDeep(function(Array<Y.Event>, Transaction))`**\
&#x20;   Registers a change observer that will be called synchronously every time this type or any of its children is modified. In the case this type is modified in the event listener, the event listener will be called again after the current event listener returns. The event listener receives all Events created by itself or any of its children.

**`yarray.unobserveDeep(function)`**\
&#x20;   Unregisters a change observer that has been registered with `yarray.observeDeep`.

## Observing changes: Y.ArrayEvent

The `yarray.observe` callback fires `Y.ArrayEvent` events that you can use to calculate the changes that happened during a transaction. We use an adaption of the [Quill delta format](https://quilljs.com/docs/delta/) to calculate the differences. Instead of strings, our ArrayDelta works on Arrays. You can find more examples and information about the delta format in our [Y.Event API](../y.event.md#delta-format).

```javascript
yarray.observe(yarrayEvent => {
  yarrayEvent.target === yarray // => true

  // Find out what changed: 
  // Log the Array-Delta Format to calculate the difference to the last observe-event
  console.log(yarrayEvent.changes.delta)
})

yarray.insert(0, [1, 2, 3]) // => [{ insert: [1, 2, 3] }]
yarray.delete(2, 1) // [{ retain: 2 }, { delete: 1 }]

console.log(yarray.toArray()) // => [1, 2]

// The delta-format is very useful when multiple changes
// are performed in a single transaction
ydoc.transact(() => {
  yarray.insert(1, ['a', 'b'])
  yarray.delete(2, 2) // deletes 'b' and 2
}) // => [{ retain: 1 }, { insert: ['a'] }, { delete: 1 }]

console.log(yarray.toArray()) // => [1, 'a']
```

### Y.ArrayEvent API

See [Y.Event](../y.event.md) API. The API is inherited from Y.Event.I'm still in the process of moving the documentation to this place. For now, you can find the API docs in the README:

{% embed url="https://github.com/yjs/yjs#API" %}
API DOCS
{% endembed %}

