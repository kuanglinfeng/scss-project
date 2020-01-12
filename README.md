# SCSS搭建项目

目标：

1. 初始化

mkdir scss-demo

cd scss-demo

npm init -y

mkdir src

cd src

touch index.js

2. 引入webpack

`yarn add webpack webpack-cli --dev`

执行：`npx webpack`

提示加mode

新建webpck.config.js，配置：

```js
module.exports = {
  mode: 'production'
}
```

3. 引入webpack-dev-server

`yarn add webpack-dev-server --dev`

`yarn add html-webpack-plugin --dev`

修改webpack.config.js:
```js
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  mode: 'production',
  plugins: [
    new HtmlWebpackPlugin()
  ]
}
```

4. 多页面

配置多页面、配置多入口

在src下面新建a.js、b.js

```js
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  entry: {
    a: './src/a.js',
    b: './src/b.js',
  },
  mode: 'production',
  plugins: [
    new HtmlWebpackPlugin({
      filename: 'a.html',
      chunks: ['a']
    }),
    new HtmlWebpackPlugin({
      filename: 'b.html',
      chunks: ['b']
    })
  ]
}
```

现在就可以访问`localhost:8080/a.html` 和 `localhost:8080/b.html`了

5. 引入css loader(支持css)

`yarn add css-loader --dev`

`yarn add style-loader --dev`

根目录新建css目录，在css目录下新建a.css。并且在a.js里引入这个css。

配置webpack.config.js:

```js
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  entry: {
    a: './src/a.js',
    b: './src/b.js',
  },
  mode: 'production',
  plugins: [
    new HtmlWebpackPlugin({
      filename: 'a.html',
      chunks: ['a']
    }),
    new HtmlWebpackPlugin({
      filename: 'b.html',
      chunks: ['b']
    })
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader'],
      },
    ],
  },
}
```

执行：npx webpack-dev-server 就可以预览css样式了

原理：
简单来说，style-loader是将css-loader打包好的css代码以<style>标签的形式插入到html文件中。

6. 引入scss loader(支持scss)

将a.css改为a.scss，npx webpack-dev-server，报错

`yarn add sass-loader node-sass --dev`

修改webpack.config.js:

```js
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  entry: {
    a: './src/a.js',
    b: './src/b.js',
  },
  mode: 'production',
  plugins: [
    new HtmlWebpackPlugin({
      filename: 'a.html',
      chunks: ['a']
    }),
    new HtmlWebpackPlugin({
      filename: 'b.html',
      chunks: ['b']
    })
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader'],
      },
      {
        test: /\.s[ac]ss$/i,
        use: [
          // Creates `style` nodes from JS strings
          'style-loader',
          // Translates CSS into CommonJS
          'css-loader',
          // Compiles Sass to CSS
          'sass-loader',
        ],
      },
    ],
  },
}
```
npx webpack-dev-server，就可以预览scss了

7. 新增pages目录

目标：将a.js、b.js等页面入口文件放到pages目录下，并且以后在pages新增入口文件时，可以不必修改配置文件。

`yarn add globby`


修改webpack.config.js:

```js
const HtmlWebpackPlugin = require('html-webpack-plugin')
const globby = require('globby')
const path = require('path');

const paths = globby.sync(['./src/pages/*.js'])
const entry = {}
const plugins = [];
paths.map(p => {
  const name = path.basename(p).split('.').slice(0, -1)[0]
  entry[name] = p
  plugins.push(new HtmlWebpackPlugin({
    filename: `${name}.html`,
    chunks: [name]
  }))
})

module.exports = {
  entry: entry,
  mode: 'production',
  plugins: plugins,
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      },
      {
        test: /\.s[ac]ss$/i,
        use: [
          // Creates `style` nodes from JS strings
          'style-loader',
          // Translates CSS into CommonJS
          'css-loader',
          // Compiles Sass to CSS
          'sass-loader'
        ]
      }
    ]
  }
}
```
