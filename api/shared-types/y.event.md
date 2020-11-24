# Y.Event

### Y.Event API

`yevent.target: Y.AbstractType`  
    The shared type that this event was created on. This event describes the changes on `target`.

`yevent.currentTarget: Y.AbstractType`  
    The current target of the event as the event traverses through the \(deep\)observer callbacks. It refers to the type on which the event handler \(observe/observeDeep\) has been attached. Similar to [Event.currentTarget](https://developer.mozilla.org/en-US/docs/Web/API/Event/currentTarget).

`yevent.transaction: Y.Transaction`  
    The transaction in which this event was created on.

`yevent.path: Array<String|number>`   
    Computes the path from the Y.Doc to the changed type. You can traverse to the changed type by calling `ydoc.get(path[0]).get(path[1]).get(path[2]).get( ..`.

`yevent.changes.delta: Delta`  
    Computes the changes in the delta format. See more in the [Delta Format](y.event.md#delta-format) section.

`yevent.changes.keys: Map<string, { action: 'add' | 'update' | 'delete', oldValue: any }>`  
    Computes changes on the attributes / key-value map of a shared type. In Y.Map it is used to represent  changed keys. In Y.Xml it is used to describe changes on the xml-attributes.

### Delta Format

The [Delta Format](https://quilljs.com/docs/delta/) was originally described by the Quill rich text editor. The delta format is also used in Yjs to represent changes on sequence-like data \(e.g. Y.Text, Y.Array, Y.XmlFragment\).



\[..\]









