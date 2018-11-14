<!--
# license: Licensed to the Apache Software Foundation (ASF) under one
#         or more contributor license agreements.  See the NOTICE file
#         distributed with this work for additional information
#         regarding copyright ownership.  The ASF licenses this file
#         to you under the Apache License, Version 2.0 (the
#         "License"); you may not use this file except in compliance
#         with the License.  You may obtain a copy of the License at
#
#           http://www.apache.org/licenses/LICENSE-2.0
#
#         Unless required by applicable law or agreed to in writing,
#         software distributed under the License is distributed on an
#         "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
#         KIND, either express or implied.  See the License for the
#         specific language governing permissions and limitations
#         under the License.
-->

# AirGap Web View for Cordova

This is a fork of the [Ionic WKWebView cordova plugin](https://github.com/ionic-team/cordova-plugin-ionic-webview), removing the internal webserver and serving the files directly from the local filesystem in order to ensure proper sandboxing.

## Migration

**LocalStorage**

The localStorage and IndexedDB of the ionic-webview will not be accessible after the switch to this plugin. If you use @ionic/storage and the sqlite driver, then you will not be affected by this.

**WebWorkers**

WebWorkers cannot be loaded from the filesystem directly. As a workaround, they have to be inlined and loaded as a string/blob. So instead of this:

worker.js
```ts
self.onmessage = function (event) {
  // worker code
}
```

```ts
const worker = new Worker('./assets/workers/worker.js')
```

You will have to do this:

worker.js
```ts
const workerJS = `
self.onmessage = function (event) {
  // worker code
}
`

export default workerJS
```

```ts
import workerJS from '../../assets/workers/worker'
const blobURL = window.URL.createObjectURL(new Blob([workerJS]))
const entropyCalculatorWorker = new Worker(blobURL)
```

**Others**

There might be other issues that we are not aware of. Please test your app thoroughly after switching to this plugin and report any errors here.
