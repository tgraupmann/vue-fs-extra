# vue-fs-extra

> Vue using fs-extra

* Note browsers cannot access the FileSystem due to security reasons. Issue - [Unable to require 'fs' with Vue CLI 3](https://stackoverflow.com/questions/52420663/unable-to-require-fs-with-vue-cli-3)

* The workaround is to access `fs-extra` during the webpack bootstrap.

```js
var ws = require('ws');
const wss = new ws.WebSocketServer({ port: 8081 });

wss.on('connection', function connection(ws) {
  ws.on('message', function message(data, isBinary) {
    if (!isBinary) {
      let json = JSON.parse(data);
      console.log('websocket onmessage:', JSON.stringify(json, null, 2));
      switch (json.method) {
        case 'readdir':
          const fse = require("fs-extra");
          console.log('The fs-extra module is accessible from a websocket hosted in webpack.config.js');
          fse.readdir('.', (err, dir) => {
            console.log('readdir dir', dir);
            for (let filePath of dir) {
              console.log('readdir filePath', filePath);
            }
          });
          break;
      }
    }
  });
});

```

## Project CLI Setup

**Setup the Vue CLI**

```
npm i -g @vue/cli
```

**Get the Vue Loader**

```
npm install -D vue-loader vue-template-compiler
```

**Create a new webpack project: (Say no to SASS unless it’s setup properly)**

```
vue init webpack-simple vue-fs-extra
cd vue-fs-extra
```

**Install packages**

```
npm install
```

**Install [fs-extra](https://www.npmjs.com/package/fs-extra)**

```
npm install fs-extra
```

**Install [WebSocket](https://github.com/websockets/ws)**

```
npm install ws
```

**Launch dev site**

```
npm run dev
```

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build
```

For detailed explanation on how things work, consult the [docs for vue-loader](http://vuejs.github.io/vue-loader).
