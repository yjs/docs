# Monaco

[Monaco](https://microsoft.github.io/monaco-editor/) is the editor that powers VS Code. The [y-monaco](https://github.com/yjs/y-monaco/) extension makes it collaborative.

{% embed url="https://microsoft.github.io/monaco-editor/" %}

{% embed url="https://github.com/yjs/y-monaco/" caption="" %}

```text
const ydoc = new Y.Doc()
const provider = new WebsocketProvider('wss://demos.yjs.dev/ws', 'monaco', ydoc) // or any other provider
const type = ydoc.getText('monaco')

const editor = monaco.editor.create(
  document.getElementById('monaco-editor'),
  {
    value: '',
    language: 'javascript',
    theme: 'vs-dark'
  }
)
const monacoBinding = new MonacoBinding(type, editor.getModel(), new Set([editor]), provider.awareness)

```

{% tabs %}
{% tab title="JavaScript" %}
```javascript
import * as Y from 'yjs'
import { WebrtcProvider } from 'y-webrtc'
import { MonacoBinding } from 'y-monaco'

const ydoc = new Y.Doc()
const provider = new WebrtcProvider('monaco', ydoc)
const type = ydoc.getText('monaco')

// There are some steps missing to initialize the editor
// The editor requires a webpack build-step
// See the complete example:
//   https://github.com/yjs/yjs-demos/blob/master/monaco/monaco.js
const editor = monaco.editor.create(
  document.getElementById('monaco-editor'),
  {
    value: '',
    language: 'javascript',
    theme: 'vs-dark'
  }
)
const monacoBinding = new MonacoBinding(
  type,
  editor.getModel(),
  new Set([editor]),
  provider.awareness
)

```
{% endtab %}

{% tab title="install" %}
```
npm i monaco-editor yjs y-monaco
```
{% endtab %}
{% endtabs %}

#### Live Demo

Unfortunately, we can't show a live-code example on this website because the editor requires a build-step that no online-IDE supports. Maybe you can give it a try? Anyway, we still have a live demo available on a different website \(simply open it in two different tabs\). 

{% embed url="https://demos.yjs.dev/monaco/monaco.html" caption="Live Demo of the y-monaco editor binding" %}

#### Demo Code

The [yjs-demos](https://github.com/yjs/yjs-demos) repository contains several demos. Simply download the repository you are interested in \(e.g. the `monaco` folder\) and run \`npm install && npm start\` to run the demo.

{% embed url="https://github.com/yjs/yjs-demos/tree/master/monaco" %}





