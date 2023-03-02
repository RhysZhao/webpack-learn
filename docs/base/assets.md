<!--
 * Author  rhys.zhao
 * Date  2023-03-02 14:14:59
 * LastEditors  rhys.zhao
 * LastEditTime  2023-03-02 19:22:57
 * Description
-->

# assets

日常开发的项目中，除了 js 和 css，还有一些静态资源文件。webpack4 之前这些静态资源文件也是交由 loader 处理的。在 webpack5 中内置了这些 loader。我们直接通过 assets 配置即可。

## 引入图片资源

```js
const HtmlWebpackPlugin = require('html-webpack-plugin');
const path = require('path');
module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, './dist'),
    clean: true
  },
  module: {
    rules: [
      {
        test: /\.js|jsx$/,
        use: ['babel-loader']
      },
      {
        test: /\.less$/,
        use: ['style-loader', 'css-loader', 'less-loader']
      },
      {
        test: /\.(png|svg|jpg|jpeg|gif)$/i,
        type: 'asset'
      }
    ]
  },
  plugins: [new HtmlWebpackPlugin()],
  mode: 'development'
};
```
