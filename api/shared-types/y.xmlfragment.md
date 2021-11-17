---
description: A shared type to manage a collection of Y.Xml* Nodes
---

# Y.XmlFragment

```javascript
import * as Y from 'yjs'

const ydoc = new Y.Doc()

// You can define a Y.XmlFragment as a top-level type or a nested type

// Method 1: Define a top-level type
const yxmlFragment = ydoc.getXmlFragment('fragment-name')
// Method 2: Define Y.XmlFragment that can be included into the Yjs document
const yxmlNested = new Y.XmlFragment('fragment-name')

// Common methods
const yxmlText = new Y.XmlText()
yxmlFragment.insert(0, [yxmlText])
yxmlFragment.firstChild === yxmlText
yxmlFragment.insertAfter(yxmlText, [new Y.XmlElement('node-name')])
yxmlFragment.get(0) === yxmlText // => true

//show result in dev console
console.log(yxmlFragment.toDOM())
```

## API

**`yxmlFragment.doc: Y.Doc | null`** (readonly)\
The Yjs document that this type is bound to. Is `null` when it is not bound yet.

**`yxmlFragment.parent: Y.AbstractType | null`**\
The parent that holds this type. Is `null` if this `yxmlFragment` is a top-level type.

**`yxmlFragment.firstChild: Y.XmlElement | Y.XmlText | null`**\
The first child that holds this type holds. Is `null` if this type doesn't hold any children.

**`yxmlFragment.length: number`**\
The number of child-elements that this Y.XmlFragment holds.

**`yxmlFragment.insert(index: number, content: Array<Y.XmlElement | Y.XmlText>)`**\
Insert content at a specified `index`. Note that - for performance reasons - content is always an array of elements. I.e. `yxmlFragment.insert(0, [new Y.XmlElement()])` inserts a single element at position 0.

**`yxmlFragment.insertAfter(ref: Y.XmlElement | Y.XmlText | null, content: Array<Y.XmlElement | Y.XmlText>)`**\
Insert content after a reference element. If the reference element `ref` is null, then the content is inserted at the beginning.

**`yxmlFragment.delete(index: number, length: number)`**\
Delete `length` elements starting from `index`.

**`yxmlFragment.push(content: Array<Y.XmlElement | Y.XmlText>)`**\
Append content at the end of the Y.XmlElement. Equivalent to `yxmlFragment.insert(yxmlFragment.length, content)`.

**`yxmlFragment.unshift(content: Array<Y.XmlElement | Y.XmlText>)`**\
Prepend content to the beginning of the Y.Array. Same as `yxmlFragment.insert(0, content)`.

**`yxmlFragment.get(index: number): Y.XmlElement | Y.XmlText`**\
Retrieve the n-th element.

**`yxmlFragment.slice([start: number [, end: number]]): Array<Y.XmlElement | Y.XmlText>`**\
Retrieve a range of content starting from index `start` (inclusive) to index `end` (exclusive). Negative indexes can be used to indicate offsets from the end of the Y.XmlFragment. I.e. `yxmlFragment.slice(-1)` returns the last element. `yxmlFragment.slice(0, -1)` returns all but the last element. Works similarly to the [Array.slice](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Array/slice) method.

**`yxmlFragment.toJSON(): String`**\
Retrieve the JSON representation of this type. The result is an XML string.

**`yxmlFragment.createTreeWalker(filter: function(yxml: Y.XmlElement | Y.XmlText): boolean): Iterable`**\
Create an [Iterable](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration\_protocols) that walks through all children of this type (not only direct children). The returned iterable returns every element that the filter accepts. I.e. the following code iterates through all `Y.XmlElements` that have the node-name `'p'`.

```javascript
// Log all <p> nodes that are children of this Y.XmlFragment
for (const paragraph of yxmlFragment.createTreeWalker(yxml => yxml.nodeName === 'p')) {
  ..
}
```

**`yxmlFragment.clone(): Y.XmlFragment`**\
Clone all values into a fresh Y.XmlFragment instance. The returned type can be included into the Yjs document.

** toDOM():DocumentFragment ** Transforms this type and all children to new DOM elements.

**`yxmlFragment.observe(function(Y.XmlEvent, Transaction))`**\
Registers a change observer that will be called synchronously every time this shared type is modified. In the case this type is modified in the observer call, the event listener will be called again after the current event listener returns.

**`yxmlFragment.unobserve(function)`**\
Unregisters a change observer that has been registered with `yxmlFragment.observe`.

**`yxmlFragment.observeDeep(function(Array<Y.Event>, Transaction))`**\
Registers a change observer that will be called synchronously every time this type or any of its children is modified. In the case this type is modified in the event listener, the event listener will be called again after the current event listener returns. The event listener receives all Events created by itself and any of its children.

**`yxmlFragment.unobserveDeep(function)`**\
Unregisters a change observer that has been registered with `yxmlFragment.observeDeep`.

## Observing changes: Y.XmlEvent

The `yxmlFragment.observe` callback fires `Y.XmlEvent` events that you can use to calculate the changes that happened during a transaction. We use an adaption of the [Quill delta format](https://quilljs.com/docs/delta/) to calculate insertions & deletions of child-elements. You can find more examples and information about the delta format in our [Y.Event API](../y.event.md#delta-format).

```javascript
yxmlFragment.observe(yxmlElent => {
  yxmlEvent.target === yarray // => true

  // Observe when child-elements are added or deleted. 
  // Log the Xml-Delta Format to calculate the difference to the last observe-event
  console.log(yxmlEvent.changes.delta)
})

yxmlFragment.insert(0, [new Y.XmlText()]) // => [{ insert: [yxmlText] }]
yxmlFragment.delete(0, 1) // [{ delete: 1 }]
```

### Y.XmlEvent API

\[todo]

See [Y.Event](../y.event.md) API. The API is inherited from Y.Event.I'm still in the process of moving the documentation to this place. For now, you can find the API docs in the README:

{% embed url="https://github.com/yjs/yjs#API" %}
API DOCS
{% endembed %}
