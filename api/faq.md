# FAQ

### I get a new ClientID for every session, is there a way to make it static for a peer accessing the document?

The ClientID is used for conflict resolution. So it is important that you understand all side-effects of retaining a ClientID across sessions. The simple answer is: Yjs is designed to create a new ClientID for every session to avoid sync conflicts. The recommended method to identify users is using the Awareness feature. If you still want to retain a ClientID, you can do so by simply overwriting the `ydoc.clientID` property. But you must ensure that no other `Y.Doc` instance is currently holding that ClientID. This is not always possible: A user might open several browser-windows with the same user account. When two `Y.Doc` instances with the same ClientID exist, the document might get permanently corrupted without a way to recover. So do this with caution.











