# FAQ

### I get a new ClientID for every session, is there a way to make it static for a peer accessing the document?

The ClientID is used for conflict resolution. So it is important that you understand all side-effects of retaining a ClientID across sessions. The simple answer is: Yjs is designed to create a new ClientID for every session to avoid sync conflicts. The recommended method to identify users is using the Awareness feature. If you still want to retain a ClientID, you can do so by simply overwriting the `ydoc.clientID` property. But you must ensure that no other `Y.Doc` instance is currently holding that ClientID. This is not always possible: A user might open several browser windows with the same user account. When two `Y.Doc` instances with the same ClientID exist, the document might get permanently corrupted without a way to recover. So do this with caution.

### Structuring data in smaller YDocs

One basically needs to decide on the following:

1. To use one or multiple YDocs for an entity or set of entities in your application.
2. How to structure the data within a YDoc.

When reasoning around how to structure data in Yjs I recommend to consider these aspects:

1. **The flow of data for common use cases:** It can be good to group data that is often used together. In contrast, it may not be practical to load hundreds of YDocs at once or load new YDocs very frequently.
2. **Read/write permissions:** Permissions cannot be practically enforced within a YDoc so you need to split data into multiple YDocs if you need different permissions for different parts of the data.
3. **Size is very rarely a practical problem** as long as you deal with human-entered text input. (See [benchmarks](https://github.com/dmonad/crdt-benchmarks).)
4. **Separate structure and data:** In some cases, it can be practical to have one YDoc that holds only the id references across entities (eg. pages) and one YDoc per entity data. This is particularly relevant if you need different permission levels for different entities. If you have no need for granular control, a split like this may be unnecessarily complex.
5. **History and undo:** At what level is it natural to track edit history and perform undo? It is much easier to perform history tracking within a single YDoc rather than spread across multiple YDocs.
6. **Consider using a single top-level YMap:** Top-level shared types cannot be deleted, so you may want to structure all your data in a single top-level YMap, eg. `yDoc.getMap('data').get('page-1')`.
7. **Subdocuments:** You may also consider using [subdocuments 2](https://docs.yjs.dev/api/subdocuments). However, it gets a bit more complex and your provider may not support it.





