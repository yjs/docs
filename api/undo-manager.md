---
description: A selective Undo/Redo manager for Yjs.
---

# Y.UndoManager

Yjs ships with a selective Undo/Redo manager. The changes can be optionally scoped to transaction origins.

```javascript
import * as Y from 'yjs'

const ytext = doc.getText('text')
const undoManager = new Y.UndoManager(ytext)

ytext.insert(0, 'abc')
undoManager.undo()
ytext.toString() // => ''
undoManager.redo()
ytext.toString() // => 'abc'
```

**`const undoManager = new Y.UndoManager(scope: Y.AbstractType | Array<Y.AbstractType> [, {captureTimeout: number, trackedOrigins: Set<any>, deleteFilter: function(item):boolean}])`**  
    Creates a new Y.UndoManager on a scope of shared types. If any of the specified types, or any of its children is modified, the UndoManager adds a reverse-operation on its stack. Optionally, you may specify `trackedOrigins` to filter specific changes. By default, all local changes will be tracked. The UndoManager merges edits that are created within a certain `captureTimeout` \(defaults to 500ms\). Set it to 0 to capture each change individually.

**`undoManager.undo()`**  
    Undo the last operation on the UndoManager stack. The reverse operation will be put on the redo-stack.

**`undoManager.redo()`**  
    Redo the last operation on the redo-stack. I.e. the previous redo is reversed.

**`undoManager.stopCapturing()`**  
    Call `stopCapturing()` to ensure that the next operation that is put on the UndoManager is not merged with the previous operation.

**`undoManager.clear()`**  
    Delete all captured operations from the undo & redo stack.

**`undoManager.on('stack-item-added', {stackItem: { meta: Map<any,any>, type: 'undo'|'redo'}}`**  
    Register an event that is called when a `StackItem` is added to the undo- or the redo-stack.

**`on('stack-item-popped', { stackItem: { meta: Map<any,any> }, type: 'undo' | 'redo' })`**  
    Register an event that is called when a `StackItem` is popped from the undo- or the redo-stack.

### **Example: Stop Capturing**

UndoManager merges Undo-StackItems if they are created within time-gap smaller than `options.captureTimeout`. Call `um.stopCapturing()` so that the next StackItem won't be merged.

```javascript
// without stopCapturing
ytext.insert(0, 'a')
ytext.insert(1, 'b')
undoManager.undo()
ytext.toString() // => '' (note that 'ab' was removed)

// with stopCapturing
ytext.insert(0, 'a')
undoManager.stopCapturing()
ytext.insert(0, 'b')
undoManager.undo()
ytext.toString() // => 'a' (note that only 'b' was removed)
```

### **Example: Specify tracked origins**

Every change on the shared document has an origin. If no origin was specified, it defaults to `null`. By specifying `trackedOrigins` you can selectively specify which changes should be tracked by `UndoManager`. The UndoManager instance is always added to `trackedOrigins`.

```javascript
class CustomBinding {}

const ytext = doc.getText('text')
const undoManager = new Y.UndoManager(ytext, {
  trackedOrigins: new Set([42, CustomBinding])
})

ytext.insert(0, 'abc')
undoManager.undo()
ytext.toString() // => 'abc' (does not track because origin `null` and not part
                 //           of `trackedTransactionOrigins`)
ytext.delete(0, 3) // revert change

doc.transact(() => {
  ytext.insert(0, 'abc')
}, 42)
undoManager.undo()
ytext.toString() // => '' (tracked because origin is an instance of `trackedTransactionorigins`)

doc.transact(() => {
  ytext.insert(0, 'abc')
}, 41)
undoManager.undo()
ytext.toString() // => '' (not tracked because 41 is not an instance of
                 //        `trackedTransactionorigins`)
ytext.delete(0, 3) // revert change

doc.transact(() => {
  ytext.insert(0, 'abc')
}, new CustomBinding())
undoManager.undo()
ytext.toString() // => '' (tracked because origin is a `CustomBinding` and
                 //        `CustomBinding` is in `trackedTransactionorigins`)
```

### **Example: Add additional information to the StackItems**

When undoing or redoing a previous action, it is often expected to restore additional meta information like the cursor location or the view on the document. You can assign meta-information to Undo-/Redo-StackItems.

```javascript
const ytext = doc.getText('text')
const undoManager = new Y.UndoManager(ytext, {
  trackedOrigins: new Set([42, CustomBinding])
})

undoManager.on('stack-item-added', event => {
  // save the current cursor location on the stack-item
  event.stackItem.meta.set('cursor-location', getRelativeCursorLocation())
})

undoManager.on('stack-item-popped', event => {
  // restore the current cursor location on the stack-item
  restoreCursorLocation(event.stackItem.meta.get('cursor-location'))
})
```

