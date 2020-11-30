# Y.XmlElement



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

> Inherits from [Y.XmlFragment](y.xmlfragment.md).

**`const yxmlElement = Y.XmlElement(nodeName: string)`**

**`yxmlElement.nodeName: string`**  
    The name of this Y.XmlElement as a String.

**`yxmlElement.prevSibling: Y.XmlElement | Y.XmlText | null`**  
    The previous sibling of this type. Is null if this is the first child of its parent.

**`yxmlElement.nextSibling: Y.XmlElement | Y.XmlText | null`**  
    The next sibling of this type. Is null if this is the last child of its parent.

**`yxmlElement.toString(): string`**  
    Returns the XML-String representation of this element. E.g. `"<div height="30px"></div>"`

**`yxmlElement.setAttribute(name: string, value: string | Y.AbstractType)`**  
    Set an XML attribute. Technically, the value can only be a string. But we also allow shared types. In this case, the XML type can't be properly converted to a string.

**`yxmlElement.removeAttribute(name: string)`**  
    Remove an XML attribute.

**`yxmlElement.getAttribute(name: string): string | Y.AbstractType`**  
    Retrieve an XML attribute.

**`yxmlElement.getAttributes(): Object<string, string | Y.AbstractType>`**  
    Retrieve all XML attributes.

## Observing changes: Y.XmlEvent

\[todo\]

The `yxmlFragment.observe` callback fires `Y.XmlEvent` events that you can use to calculate the changes that happened during a transaction. We use an adaption of the [Quill delta format](https://quilljs.com/docs/delta/) to calculate insertions & deletions of child-elements. You can find more examples and information about the delta format in our [Y.Event API](../y.event.md#delta-format).

```javascript
yxmlFragment.observe(yxmlElent => {
  yarrayEvent.target === yarray // => true

  // Observe when child-elements are added or deleted. 
  // Log the Xml-Delta Format to calculate the difference to the last observe-event
  console.log(yxmlEvent.changes.delta)
})

yxmlFragment.insert(0, [new Y.XmlText()]) // => [{ insert: [yxmlText] }]
yxmlFragment.delete(0, 1) // [{ delete: 1 }]
```

### Y.XmlEvent API

\[todo\]

describe childListChanged and attributesChanged

See [Y.Event](../y.event.md) API. The API is inherited from Y.Event.I'm still in the process of moving the documentation to this place. For now, you can find the API docs in the README:

{% embed url="https://github.com/yjs/yjs\#API" caption="API DOCS" %}





