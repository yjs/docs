---
description: Extends Y.Text to represent a Y.Xml node.
---

# Y.XmlText

```javascript
import * as Y from 'yjs'

const ydoc = new Y.Doc()

// You can define a Y.XmlText as a top-level type or a nested type

// Method 1: Define a top-level type
const yxmlText = ydoc.get('my xmltext type', Y.XmlText) 
// Method 2: Define Y.XmlText that can be included into the Yjs document
const yxmltextNested = new Y.XmlText()

// Nested types can be included as content into any other shared type
yxmlText.set('my nested text', ytextNested)

// Common methods (also available in Y.Text)
yxmlText.insert(0, 'abc') // insert three elements
yxmlText.format(1, 2, { bold: true }) // delete second element 
yxmlText.toString() // => 'abc'
yxmlText.toDelta() // => [{ insert: 'a' }, { insert: 'bc', attributes: { bold: true }}]

// methods only available in Y.Text
yxmlText.toString() // => "a<bold>bc</bold>"
yxmlText.nextSibling
```

## API

> Inherits from [Y.Text](y.text.md).

**`const yxmlText = Y.XmlText()`**

**`yxmlText.prevSibling: Y.XmlElement | Y.XmlText | null`**\
\*\*\*\* The previous sibling of this type. Is null if this is the first child of its parent.

**`yxmlText.nextSibling: Y.XmlElement | Y.XmlText | null`**\
\*\*\*\* The next sibling of this type. Is null if this is the last child of its parent.

**`yxmlText.toString(): string`**\
\*\*\*\* Returns the XML-String representation of this element. Formatting attributes are transformed to XML-tags. If the formatting attribute contains an object, the key-value pairs will be used as attributes. E.g.

```javascript
ymxlText.insert(0, "my link", { a: { href: 'https://..' } })
ymxlText.toString() // => <a href="https://..">my link</a>
```
