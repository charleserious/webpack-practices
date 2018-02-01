这个repo是参考阮一峰老师的[webpack-demos](https://github.com/ruanyf/webpack-demos)。
这些小样是一个学习[Webpack](https://webpack.js.org/)的练习，正如阮一峰老师所说，通过这些小样我确实对Webpack这个强大的工具有了一个相对高层的清晰的认识。

## 怎么做

首先，全局安装[Webpack](https://www.npmjs.com/package/webpack) 和 [webpack-dev-server](https://www.npmjs.com/package/webpack-dev-server)。

```bash
$ npm i -g webpack webpack-dev-server
```

然后，clone this repo
```bash
$ git clone https://github.com/charleserious/webpack-practices.git
```

安装依赖
```bash
$ cd webpack-practices
$ npm install
```

最后，快活了。
```bash
$ cd entry
$ npm run dev
```

如果上面的命令没有自动打开你的浏览器，你只得自己访问[http://localhost:9020/](http://localhost:9020/)

## 什么是Webpack

Webpack是一个为浏览器构建JavaScript模块脚本的前端工具。

Webpack的配置集中心一个叫`webpack.config.js`的文件中，说到底是一个CommonJS模块。

```JavaScript
// webpack.config.js
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  }
};
```

建立`webpack.config.js`之后，我们就可以在不配置附加任何参数的情况下调用Webpack了。

```bash
$ webpack
```

当然，我们也可以自定义`package.json`中的`scripts`部分。

```JavaScript
// package.json
{
  // ...
  "scripts": {
    "dev": "webpack-dev-server --devtool eval --progress --colors",
    "deploy": "NODE_ENV=production webpack -p"
  },
  // ...
}
```

## Index
1. [Entry file](#entry-file-source)
1. [Multiple entry files]()
1. [Babel-loader]()
1. [CSS-loader]()
1. [Image loader]()
1. [CSS Module]()
1. [UglifyJs Plugin]()
1. [HTML Webpack Plugin and Open Browser Webpack Plugin]()
1. [Enviroment flags]()
1. [Code splitting]()
1. [Code splitting with bundle-loader]()
1. [Common chunk]()
1. [Vendor chunk]()
1. [Exposing Global Variables]()
1. [React router]()

## Entry file ([source](https://github.com/charleserious/webpack-practices/tree/master/entry))
 Entry file is the file which `Webpack` reads be build `bundle.js`.

 For example, `main.js` is an entry file.

 ```JavaScript
 // main.js
 document.write('<h1>Hello World</h1>');
 ```

 index.html

 ```html
<html>
    <body>
        <script src="bundle.js"></script>
    </body>
</html>
 ```

 Webpack follows `webpack.config.js` to build `bundle.js`.

 ```JavaScript
webpack.config.js
module.exports = {
    entry: './main.js',
    output: {
        filename: 'bundle.js'
    }
};
 ```

 Launch the werver, visit [http://localhost:9020](http://localhost:9020)

 ```bash
$ cd entry.file
$ npm run dev
 ```