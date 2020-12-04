---
description: A shared type that represents an XML node
---

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

The `yxmlElement.observe` callback fires `Y.XmlEvent` events that you can use to calculate the changes that happened during a transaction. We use an adaption of the [Quill delta format](https://quilljs.com/docs/delta/) to calculate insertions & deletions of child-elements. Changes on the xml-attributes are expressed using the same API from [Y.Map](y.map.md#observing-changes-y-mapevent).

```javascript
yxmlFragment.observe(yxmlElent => {
  yxmlEvent.target === yarray // => true

  // Observe when child-elements are added or deleted. 
  // Log the Xml-Delta Format to calculate the difference to the last observe-event
  console.log(yxmlEvent.changes.delta)

  // Observe attribute changes.  
  // Option 1: A set of keys that changed
  yxmlEvent.keysChanged // => Set<strings>
  // Option 2: Compute the differences
  yxmlEvent.changes.keys // => Map<string, { action: 'add'|'update'|'delete', oldValue: any}>
  
  // The change format is equivalent to the Y.MapEvent change format.
  yxmlEvent.changes.keys.forEach((change, key) => {
    if (change.action === 'add') {
      console.log(`Attribute "${key}" was added. Initial value: "${ymap.get(key)}".`)
    } else if (change.action === 'update') {
      console.log(`Attribute "${key}" was updated. New value: "${ymap.get(key)}". Previous value: "${change.oldValue}".`)
    } else if (change.action === 'delete') {
      console.log(`Attribute "${key}" was deleted. New value: undefined. Previous value: "${change.oldValue}".`)
    }
  })
})

yxmlElement.insert(0, [new Y.XmlText()]) // => [{ insert: [yxmlText] }]
yxmlElement.delete(0, 1) // [{ delete: 1 }]

yxmlElement.setAttribute('key', 'value') // Attribute "key" was added. Initial value: "undefined".
yxmlElement.setAttribute('key', 'new value') // Attribute "key" was updated. New value: "new value". Previous value: "value"
yxmlElement.deleteAttribute('key') // Attribute "key" was deleted. New value: undefined. Previous value: "new value"
```

### Y.XmlEvent API

\[todo\]

describe childListChanged and attributesChanged

See [Y.Event](../y.event.md) API. The API is inherited from Y.Event.I'm still in the process of moving the documentation to this place. For now, you can find the API docs in the README:

{% embed url="https://github.com/yjs/yjs\#API" caption="API DOCS" %}





