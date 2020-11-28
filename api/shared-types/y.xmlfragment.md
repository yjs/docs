# Y.XmlFragment



```javascript
import * as Y from 'yjs'

const ydoc = new Y.Doc()

// You can define a Y.XmlFragment as a top-level type or a nested type

// Method 1: Define a top-level type
const yxmlFragment = ydoc.getXmlFragment('my xml-fragment type')
// Method 2: Define Y.XmlFragment that can be included into the Yjs document
const yxmlNested = new Y.XmlFragment()

// Common methods
const yxmlText = new Y.XmlText()
yxmlFragment.insert(0, [yxmlText])
yxmlFragment.firstChild === yxmlText
yxmlFragment.insertAfter(yxmlText, new Y.XmlElement())
yxmlFragment.get(0) === yxmlText // => true
```

## API

**`yxmlFragment.doc: Y.Doc | null`** \(readonly\)  
    The Yjs document that this type is bound to. Is `null` when it is not bound yet.

**`yxmlFragment.parent: Y.AbstractType | null`**  
    The parent that holds this type. Is `null` if this `yxmlFragment` is a top-level type.

**`yxmlFragment.length: number`**  
    The number of child-elements that this Y.XmlFragment holds.

**`yxmlFragment.insert(index: number, content: Array<Y.XmlElement | Y.XmlText>)`**  
    Insert content at a specified `index`. Note that - for performance reasons - content is always an array of elements. I.e. `yxmlFragment.insert(0, [new Y.XmlElement()])` inserts a single element at position 0.

**`yxmlFragment.delete(index: number, length: number)`**  
    Delete `length` elements starting from `index`.

**`yxmlFragment.push(content: Array<Y.XmlElement | Y.XmlText>)`**  
    Append content at the end of the Y.XmlElement. Equivalent to `yxmlFragment.insert(yxmlFragment.length, content)`.

**`yxmlFragment.unshift(content: Array<Y.XmlElement | Y.XmlText>)`**  
    Prepend content to the beginning of the Y.Array. Same as `yxmlFragment.insert(0, content)`.

**`yxmlFragment.get(index: number): Y.XmlElement | Y.XmlText`**  
    Retrieve the n-th element.

**`yxmlFragment.slice([start: number [, end: number]]): Array<Y.XmlElement | Y.XmlText>`**  
    Retrieve a range of content starting from index `start` \(inclusive\) to index `end` \(exclusive\). Negative indexes can be used to indicate offsets from the end of the Y.XmlFragment. I.e. `yxmlFragment.slice(-1)` returns the last element. `yxmlFragment.slice(0, -1)` returns all but the last element. Works similarly to the [Array.slice](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice) method.

**`yxmlFragment.toJSON(): String`**  
    Retrieve the JSON representation of this type. The result is an XML string.

**`yxmlFragment.createTreeWalker(filter: function(yxml: Y.XmlElement | Y.XmlText): boolean): Iterable`**  
    Create an Iterable that walks through all children of this type \(not only direct children\). The returned iterable returns every element that the filter accepts. I.e. `for (const paragraph of yxmlFragment.createTreeWalker(yxml => yxml.nodeName === 'p') { .. }` iterates through all `Y.XmlElements` that have the node-name `<p>`.

**`yxmlFragment.clone(): Y.XmlFragment`**  
    Clone all values into a fresh Y.XmlFragment instance. The returned type can be included into the Yjs document.

**`yxmlFragment.observe(function(Y.XmlEvent, Transaction))`**  
    Registers a change observer that will be called synchronously every time this shared type is modified. In the case this type is modified in the observer call, the event listener will be called again after the current event listener returns.

**`yxmlFragment.unobserve(function)`**  
    Unregisters a change observer that has been registered with `yxmlFragment.observe`.

**`yxmlFragment.observeDeep(function(Array<Y.Event>, Transaction))`**  
    Registers a change observer that will be called synchronously every time this type or any of its children is modified. In the case this type is modified in the event listener, the event listener will be called again after the current event listener returns. The event listener receives all Events created by itself and any of its children.

**`yxmlFragment.unobserveDeep(function)`**  
    Unregisters a change observer that has been registered with `yxmlFragment.observeDeep`.

## Observing changes: Y.XmlEvent

\[todo\]

The `yxmlFragment.observe` callback fires `Y.XmlEvent` events that you can use to calculate the changes that happened during a transaction. We use an adaption of the [Quill delta format](https://quilljs.com/docs/delta/) to calculate the differences. Instead of strings, our ArrayDelta works on Arrays. You can find more examples and information about the delta format in our [Y.Event API](../y.event.md#delta-format).

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

### Y.XmlEvent API

\[todo\]

See [Y.Event](../y.event.md) API. The API is inherited from Y.Event.I'm still in the process of moving the documentation to this place. For now, you can find the API docs in the README:

{% embed url="https://github.com/yjs/yjs\#API" caption="API DOCS" %}



