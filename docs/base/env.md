<!--
 * Author  rhys.zhao
 * Date  2023-03-06 11:28:14
 * LastEditors  rhys.zhao
 * LastEditTime  2023-03-06 18:04:14
 * Description
-->

# 区分开发与生产环境

webpack 的 mode 属性用来标识打包环境。

- `mode: 'development'`: 开发环境，会启动一个本地服务器。
- `mode: 'production'`: 生产环境，会把项目代码打包，以便部署上线。

## 增加 webpack 配置目录

项目根目录下增加 webpack 目录，用来存放 webpack 配置。

在 webpack 目录增加四个文件:

- `webpack.config.common.js`: 用来存放开发与生产环境公共的配置
- `webpack.config.dev.js`: 用来存放开发环境的配置
- `webpack.config.prod.js`: 用来存放生产环境的配置
- `webpack.config.js`: 通过环境变量来合并配置。

此时，项目目录结构如下：

```
webpack-demo
├─ src
│  ├─ assets
│  │  ├─ bg.jpeg
│  │  └─ hello.txt
│  ├─ index.js
│  ├─ index.less
│  └─ math.js
├─ webpack
│  ├─ webpack.config.common.js
│  ├─ webpack.config.dev.js
│  ├─ webpack.config.js
│  └─ webpack.config.prod.js
├─ babel.config.js
├─ package-lock.json
└─ package.json
```

## 公共配置 `webpack.config.common.js`

```js
const HtmlWebpackPlugin = require('html-webpack-plugin');
const path = require('path');

module.exports = {
  entry: './src/index.js',
  module: {
    rules: [
      {
        test: /\.js$/,
        use: ['babel-loader']
      },
      {
        test: /\.less$/,
        use: ['style-loader', 'css-loader', 'less-loader']
      },
      {
        test: /\.(png|svg|jpg|jpeg|gif)$/i,
        type: 'asset/inline'
      },
      {
        test: /\.txt$/i,
        type: 'asset/source'
      }
    ]
  },
  plugins: [new HtmlWebpackPlugin()]
};
```

## 开发环境配置 `webpack.config.dev.js`

```js
module.exports = {
  devServer: {
    host: 'localhost', // 启动服务器域名
    port: '8081', // 端口
    open: true // 自动打开浏览器
  },
  mode: 'development'
};
```

## 生产环境配置 `webpack.config.prod.js`

```js
const HtmlWebpackPlugin = require('html-webpack-plugin');
const path = require('path');
module.exports = {
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, './dist'),
    clean: true
  },
  mode: 'production'
};
```

## 合并配置

`webpack.config.js` 用来通过环境变量来合并配置。

- 开发环境合并 `webpack.config.dev.js` 与 `webpack.config.common.js`
- 生产环境合并 `webpack.config.prod.js` 和 `webpack.config.common.js`

### 1. 配置环境变量

修改 `package.json`里的 scripts, 如下：

```json{2-5}
// ...
 "scripts": {
    "start": "cross-env NODE_ENV=development webpack server --config ./webpack/webpack.config.js",
    "build": "cross-env NODE_ENV=production webpack --config ./webpack/webpack.config.js"
  },
// ...
```

这里我们用到了一个插件 cross-env。安装：

```shell
npm i cross-env -D
```

cross-env 是一款运行跨平台设置和使用环境变量的脚本。在 `npm start` 与 `npm run build` 的时候，我们定义了环境变量 `NODE_ENV`分别为 development 和 production。

在 webpack 配置中，我们可以拿到这个环境变量，从而判断是开发还是生产环境。

### 2. 配置 `webpack.config.js`

合并 webpack 配置需要用到一个插件 `webpack-merge`，安装：

```shell
npm i webpack-merge -D
```

然后我们配置 `webpack.config.js`:

```js
const { merge } = require('webpack-merge');
const commonConfig = require('./webpack.config.common');
const devConfig = require('./webpack.config.dev');
const prodConfig = require('./webpack.config.prod');

module.exports = () => {
  if (process.env.NODE_ENV === 'development') {
    // 通过环境变量 NODE_ENV 来判断当前环境
    return merge(commonConfig, devConfig);
  }
  return merge(commonConfig, prodConfig);
};
```

webpack 的配置可以是一个对象，也可以是一个返回对象的函数。

这里，我们通过环境变量`NODE_ENV`来判断当前环境(开发还是生产)，然后返回对应的配置。

## 总结

1. webpack-merge 用来合并 webpack 配置
2. cross-env 可以定义环境变量，用来区分当前环境
