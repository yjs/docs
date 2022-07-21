---
description: A shared type similar with a similar API to global.Map
---

# Y.Map

```javascript
import * as Y from 'yjs'

const ydoc = new Y.Doc()

// You can define a Y.Map as a top-level type or a nested type

// Method 1: Define a top-level type
const ymap = ydoc.getMap('my map type') 
// Method 2: Define Y.Map that can be included into the Yjs document
const ymapNested = new Y.Map()

// Nested types can be included as content into any other shared type
ymap.set('my nested map', ymapNested)

// Common methods
ymap.set('prop-name', 'value') // value can be anything json-encodable
ymap.get('prop-name') // => 'value'
ymap.delete('prop-name')

// Powerful for simple stuff as toggles as well
ymap.set('your-toggle', true)
ymap.get('your-toggle') // => true
```

## API

**`ymap.doc: Y.Doc | null`** (readonly)\
The Yjs document that this type is bound to. Is `null` when it is not bound yet.

**`ymap.parent: Y.AbstractType | null`**\
The parent that holds this type. Is `null` if this `ymap` is a top-level type.

**`ymap.set(key: string, value: object|boolean|string|number|Uint8Array|Y.AbstractType)`**\
Add or update an entry with a specified key. This method works similarly to the [Map.set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Map/set) method. The value can be a shared type, an Uint8Array, or anything JSON-encodable.

**`ymap.get(key: string): object|boolean|Array|string|number|Uint8Array|Y.AbstractType`**\
Returns an entry with the specified key. This method works similarly to the [Map.get](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Map/get) method.

**`ymap.delete(key: string)`**\
Deletes an entry with the specified key. This method works similarly to the [Map.delete](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Map/delete) method.

**`ymap.has(key: string): boolean`**\
Returns true if an entry with the specified key exists. This method works similarly to the[ Map.has](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Map/has) method.

**`ymap.toJSON(): Object<string,object|boolean|Array|string|number|Uint8Array>`**\
Copies the `[key,value]` pairs of this Y.Map to a new Object. It transforms all shared types to JSON using their `toJSON` method.

**`ymap.size`**\
Returns the number of key/value pairs.

**`ymap.forEach(value: any, key: string, map: Y.Map)`**\
Execute the provided function once on every key-value pair.

**`ymap[Symbol.Iterator]: Iterator`**\
Returns an [Iterator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration\_protocols) of `[key, value]` pairs. This allows you to iterate over the `ymap` using a for..of loop: `for (const [key, value] of ymap) { .. }`

**`ymap.entries(): Iterator`**\
Returns an [Iterator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration\_protocols) of `[key, value]` pairs.

**`ymap.values(): Iterator`**\
Returns an [Iterator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration\_protocols) of values only. This allows you to iterate through the values only `for (const value of ymap.values()) { ... }` or insert all values into an array `Array.from(ymap.values())`.

**`ymap.keys(): Iterator`**\
Returns an [Iterator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration\_protocols) of keys only. This allows you to iterate through the keys only `for (const key of ymap.keys()) { ... }` or insert all keys into an array `Array.from(ymap.keys())`.

**`ymap.clone(): Y.Map`**\
Clone all values into a fresh Y.Map instance. The returned type can be included into the Yjs document.

**`ymap.observe(function(YMapEvent, Transaction))`**\
Registers a change observer that will be called synchronously every time this shared type is modified. In the case this type is modified in the observer call, the event listener will be called again after the current event listener returns.

**`ymap.unobserve(function)`**\
Unregisters a change observer that has been registered with `ymap.observe`.

**`ymap.observeDeep(function(Array<Y.Event>, Transaction))`**\
Registers a change observer that will be called synchronously every time this type or any of its children is modified. In the case this type is modified in the event listener, the event listener will be called again after the current event listener returns. The event listener receives all Events created by itself or any of its children.

**`ymap.unobserveDeep(function)`**\
Unregisters a change observer that has been registered with `ymap.observeDeep`.

## Observing changes: Y.MapEvent

```javascript
ymap.observe(ymapEvent => {
  ymapEvent.target === ymap // => true

  // Find out what changed: 
  // Option 1: A set of keys that changed
  ymapEvent.keysChanged // => Set<strings>
  // Option 2: Compute the differences
  ymapEvent.changes.keys // => Map<string, { action: 'add'|'update'|'delete', oldValue: any}>

  // sample code.
  ymapEvent.changes.keys.forEach((change, key) => {
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

### Y.MapEvent API

`ymapEvent.keysChanged: Set<string>`\
A [Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Set) containing all keys that were modified during a transaction.

See [Y.Event](../y.event.md) API. The rest of the API is inherited from Y.Event.
