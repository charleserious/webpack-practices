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
1. [Multiple entry files](#multiple-entry-file-source)
1. [Babel-loader](#babel-loader-source)
1. [CSS-loader](#css-loader-source)
1. [Image loader](#image-loader-source)
1. [CSS Module](#css-module-source)
1. [UglifyJs Plugin](#uglifyjs-plugin-source)
1. [HTML Webpack Plugin and Open Browser Webpack Plugin](#html-webpack-plugin-and-open-browser-webpack-plugin-source)
1. [Enviroment flags](#enviroment-flags-source)
1. [Code splitting](#code-splitting-source)
1. [Code splitting with bundle-loader](#code-splitting-with-bundle-loader-source)
1. [Common chunk](#common-chunk-source)
1. [Vendor chunk](#vendor-chunk-source)
1. [Exposing Global Variables](#exposing-global-variables-source)
1. [React router](#react-router-source)

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
    <body></body>
    <script src="bundle.js"></script>
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

 ## Multiple entry file ([source](https://github.com/charleserious/webpack-practices/tree/master/multiple.entry))

 Multiple entry files are allowed. It is useful for a multi-page app which has different entry file for each page.

 ```JavaScript
// main1.js
document.write("<h1>Hello World</h1>");

// main2.js
document.write("<h1>Hello Webpack</h1>");
 ```

 index.html

 ```html
<html>
    <body></body>
    <script src="bundle1.js"></script>
    <script src="bundle2.js"></script>
</html>
 ```

 webpack.config.js

 ```JavaScript
module.exports = {

    entry: {

        bundle1: './main1.js',
        bundle2: './main2.js',

    },

    output: {

        filename: [name].js

    }
    
};
 ```

## Babel-loader ([source](https://github.com/charleserious/webpack-practices/tree/master/babel.loader))

Loaders are preprocessors which transform a resource file of your app ([more info](http://webpack.github.io/docs/using-loaders.html)) before Webpack's building process.

For example, [Babel-loader](https://www.npmjs.com/package/babel-loader) can transform JSX/ES6 file into normal JS files, after which Webpack will begin to build these JS files. Webpack's official docs has a complete list of [loaders](http://webpack.github.io/docs/list-of-loaders.html)

`main.jsx` is a JSX file

```JavaScript
// main.jsx
const React = require( 'react' );
const ReactDOM = require( 'react-dom' );

ReactDOM.render(

    <h1>Hello, World!</h1>,

    document.querySelector('#wrapper')

);
```

index.html

```html
<html>
    <body>
        <div id="wrapper"></div>
    </body>
</html>
```

webpack.config.js

```JavaScript
module.exports = {

    entry: './main.jsx',

    output: {

        filename: 'bundle.js'

    },

    module: {

        rules: [

            {

                test: /\.jsx?$/,
                exclude: /node-modules/,

                use: {

                    loader: 'babel-loader',

                    options: {

                        presets: [ 'es2015', 'react' ]

                    }

                }

            }

        ]

    }

};
```

The above snippet uses `babel-loader` which needs Babel's preset plugins [babel-preset-es2015](https://www.npmjs.com/package/babel-preset-es2015) and [babel-preset-react](https://www.npmjs.com/package/babel-preset-react) to transpile ES6 and React.

## CSS-loader ([source](https://github.com/charleserious/webpack-practices/tree/master/css.loader))

Webpack allows you to include CSS in JS file, then preprocessed CSS file with [CSS-loader](https://github.com/webpack-contrib/css-loader).

main.js

```JavaScript
require('./app.css');
```

app.css

```CSS
body {
    background-color: blue;
}
```

index.html

```html
<html>
    <body>
        <h1>Hello World</h1>
    </body>
    <script src='bundle.js'></script>
</html>
```

webpack.config.js
```JavaScript
module.exports = {

    entry: './main.js',
    output: {

        filename: 'bundle.js'

    },

    module: {

        rules: {

            test: /\.css$/,
            use: [ 'style-loader', 'css-loader' ]

        }

    }

};
```

Attention, you have to use two loaders to transform CSS files. First is [CSS-loader](https://www.npmjs.com/package/css-loader) to read CSS file, and another one is [Style-laoder](https://www.npmjs.com/package/style-loader) to insert `<style>` tag into HTML page.

Then, launch the server.

```bash
$ cd css.loader
$ npm run dev
```

Actually, Webpack inserts an internal style sheet into index.html

```html
<html>
    <head>
        <style type="text/css">
            body {
                background-color: blue;
            }
        </style>
    </head>
    <body>
        <h1>Hello World</h1>
        <script src='bundle.js'></script>
    </body>
</html>
```

## Image loader ([source](https://github.com/charleserious/webpack-practices/tree/master/image.loader))

Webpack could also include images in JS files.

main.js

```JavaScript
var img1 = document.createElement( 'img' );
img1.src = require( './small.png' );
document.body.appendChild( img1 );

var img2 = document.createElement( 'img' );
img2.src = require( './big.png' );
document.body.appendChild( img2 );
```

index.html

```html
<html>
    <body></body>
    <script src="bundle.js"></script>
</html>
```

webpack.config.js

```JavaScript
module.exports = {

    entry: './main.js',
    output: {

        filename: 'bundle.js'

    },

    module: {

        rules: [
            {

                test: /\.(png|jpg)$/,
                use: {

                    loader: 'url-loader',
                    options: [

                        limit: 8192

                    ]

                }
                
            }

        ]

    }
    
};
```

[url-loader](https://www.npmjs.com/package/url-loader) transforms image files into <img> tag. If the image size is smaller than 8192 bytes, it will be tansform into DataURL; otherwise, it will be transformed into normal URL.

After launching the server, small.png and big.png hava following URLs.

```html
<img src="data:image/png;base64,iVBOR...uQmCC">
<img src="4853ca667a2b8b8844eb2693ac1b2578.png">
```


## CSS Module ([source](https://github.com/charleserious/webpack-practices/tree/master/css.module))



## UglifyJs Plugin ([source](https://github.com/charleserious/webpack-practices/tree/master/uglifyjs.plugin))



## HTML Webpack Plugin and Open Browser Webpack Plugin ([source](https://github.com/charleserious/webpack-practices/tree/master/html.webpack.plugin.and.open.browser.webpack.plugin))



## Enviroment flags ([source](https://github.com/charleserious/webpack-practices/tree/master/enviroment.flags))



## Code splitting ([source](https://github.com/charleserious/webpack-practices/tree/master/code.splitting))



## Code splitting with bundle-loader ([source](https://github.com/charleserious/webpack-practices/tree/master/code.splitting.with.bundle.loader))



## Common chunk ([source](https://github.com/charleserious/webpack-practices/tree/master/common.chunk))



## Vendor chunk ([source](https://github.com/charleserious/webpack-practices/tree/master/vendor.chunk))



## Exposing Global Variables ([source](https://github.com/charleserious/webpack-practices/tree/master/exposing.global.variables))



## React router ([source](https://github.com/charleserious/webpack-practices/tree/master/react.router))


