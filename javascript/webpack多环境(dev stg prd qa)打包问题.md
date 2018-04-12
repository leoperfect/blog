# webpack多环境(dev stg prd qa)打包问题

> 针对前后端分离的项目，在多环境部署的时候，不同环境的前端分别调用的后端接口不同，怎么实现呢？

## 1. 脚本入口地方传入环境信息

```json
  "scripts": {
    "build:dev": "cross-env NODE_ENV=dev node build/build.js",
    "build:stg": "cross-env NODE_ENV=stg node build/build.js",
    "build:prd": "cross-env NODE_ENV=prd node build/build.js",
    "build:qa": "cross-env NODE_ENV=qa node build/build.js"
  }
  "devDependencies": {
	"cross-env": "^5.0.1"
   }	
```

其中通过[cross-env](https://github.com/kentcdodds/cross-env)进行NODE环境的设置，其中`build/build.js`是webpack的配置

## 2. webpack配置

```javasc
plugins: [
    new webpack.DefinePlugin({
      __ENV__: JSON.stringify(process.env.NODE_ENV)
    })
   ]
```

`webpack`中通过webpack插件[DefinePlugin](https://doc.webpack-china.org/plugins/define-plugin/)可以在编译器创建一个全局的环境变量`__ENV__`，其中存储的就是对应的环境信息(dev stg prd qa)。

## 3.代码中的配置

api.config.js

```javascript
/* eslint-disable */
const env = __ENV__ || 'dev'; // 默认是dev
const pdfParse = {
    dev: '...',
    stg: '...',
    prd: '...',
    qa: '...'
}
const api = {
    pdfParse: pdfParse[env]
}
export default api;
```

## 4.代码中的调用

```javascript
import axios from 'axios';
import api from '../../api.config.js';
const BACK_END_URL = api.pdfParse + '/parse/';
axios.get(BACK_END_URL).then(...).catch(...);
```

 这样就能够通过执行不同的npm脚本进行不同环境的打包部署了。例如 `npm run build:dev` 打包后皆可以部署dev环境了，其调用的后端接口就是`api.config.js`中配置的dev的地址。