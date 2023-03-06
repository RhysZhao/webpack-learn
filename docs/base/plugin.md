<!--
 * Author  rhys.zhao
 * Date  2023-03-01 16:51:17
 * LastEditors  rhys.zhao
 * LastEditTime  2023-03-03 15:07:49
 * Description
-->

# plugin

插件能够完成 webpack 本身不具有的功能。它的使用一般是在 webpack 的配置信息 `plugins` 选项中指定。

在上一章中，打包后需要我们手动把打包的 js 文件引入到 html 中去。其实我们可以通过`html-webpack-plugin`插件实现自动生成 html 文件和引入 js 的功能。

## html-webpack-plugin

### 1. 安装

```
npm i html-webpack-plugin -D
```

### 2. 配置

webpack 插件的使用需要先引入，然后在`plugins`选项中实例化调用(插件本身是个类)，代码如下：

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/index.js', // 注意这里把入口文件index.js修改到了src目录下
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, './dist')
  },
  plugins: [new HtmlWebpackPlugin()],
  mode: 'development'
};
```

清空`dist`目录，然后使用`npx webpack`命令打包，我们会发现`dist`目录里自动生成了一个`index.html`。

有时我们想要自定义 html, 我们可以通过`template`参数传入我们自定义的 html 路径作为模板。代码如下：

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, './dist')
  },
  plugins: [new HtmlWebpackPlugin({ template: './index.html', filename: 'index.html', inject: 'body' })],
  mode: 'development'
};
```

其中，`plugins`选项是一个数组，里面是使用的各种插件。我们实例化了`html-webpack-plugin`插件，并且传入了三个参数`template`, `filename`,`inject`。

- `template`: 我们的 html 模板，可以根据自己需要创建、修改
- `filename`: `html-webpack-plugin`生成 html 文件的名称
- `inject`: 打包生成的 js 文件，在 html 文件哪个位置引入

更多配置，可以查询 `html-webpack-plugin`的 [API](https://www.npmjs.com/package/html-webpack-plugin)

## 清除上一次打包内容

前面的打包中，我们发现，每次打包都要手动清除上一次的`dist`文件。事实上，这个清除功能我们也可以通过插件解决。

在 webpack4 中，一般使用`clean-webpack-plugin`插件来清除上一次打包的内容。

webpack5 集成了 clean 插件，我们只需要在输出中配置`clean: true`即可。
代码如下：

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, './dist'),
    clean: true
  },
  plugins: [new HtmlWebpackPlugin({ template: './index.html', filename: 'index.html', inject: 'body' })],
  mode: 'development'
};
```

## 总结

1. webpack 插件本身是一个类，使用时需要先引入，然后在`plugins`选项中实例化调用
2. `html-webpack-plugin`能够以某个 html 作为模板，且能指定 js 引入位置，以及输出 html 文件名
3. webpack5 集成了 clean 插件，直接在输出中加入 `clean: true` 即可在每次打包的时候清除上一次打包的内容。

**参考资料：**

[webpack 官方文档](https://webpack.docschina.org/)
