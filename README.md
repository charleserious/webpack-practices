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

`css-loader?module` (the query parameter modules) enables the [CSS Module](https://github.com/css-modules/css-modules) which gives a local scoped CSS to your JS module's CSS. You can switch it off with `global(selector)` ([more info](https://css-modules.github.io/webpack-demo/)).

index.html

```html

<html>
    <body>
        <h1 class="h1">Hello World</h1>
        <h2 class="h2">Hello Webpack</h2>
        <div id="example"></div>
    </body>
    <script src="./bundle.js"></script>
</html>
```

app.css

```css

/* local scope */
.h1 {
    color: red;
}

/* global scope */
:global(.h2) {
    color: blue;
}
```

main.jsx

```JavaScript
var React = require( 'react' );
var ReactDOM = require( 'react-dom' );
var style = require( './app.css' );

ReactDOM.render(
    <div>
        <h1 className={style.h1}>Hello World</h1>
        <h1 className="h2">Hello Webpack</h1>
    </div>,
    document.getElementById( 'example' )
);
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

                test: /\.js[x]?$/,
                exclude: /node-modules/,
                use: {

                    loader: 'babel-loader',
                    options: {

                        presets: [ 'es2015', 'react' ]

                    }

                }

            },
            {

                test: /\.css$/,
                use: [
                    {

                        loader: 'style-loader'

                    },
                    {

                        loader: 'css-loader',
                        options: {

                            modules: true

                        }

                    }

                ]

            }

        ]

    }

};
```

Launch the server.

```bash
$ cd css.module
$ npm run dev
```

Visiting [http://127.0.0.1:9020](http://127.0.0.1:9020), you'll find that only second `h1` is red, because its' CSS is local scoped, and both `h2` is blud, because its' CSS is global scoped.

## UglifyJs Plugin ([source](https://github.com/charleserious/webpack-practices/tree/master/uglifyjs.plugin))

Webpack has a plugin system to expand it's functions. For example, [UglyfyJs Plugin](https://webpack.js.org/plugins/uglifyjs-webpack-plugin/) will minify output (`bundle.js`) JS codes.

main.js

```JavaScript
var longVariableName = 'Hello';
longVariableName += 'World';
document.write('<h1>' + longVariableName + '</h1>');
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
var webpack = require( 'webpack' );
var UglifyJsPlugin = require( 'uglyfijs-webpack-plugin' );

module.exports = {

    entry: './main.js',
    output: {

        filename: 'bundle.js'

    },

    plugins: [

        new UglifyJsPlugin()

    ]
};
```

After launching the server `main.js` will minified into following.

```JavaScript
var o="Hello";o+="World",document.write('<h1>'+o+'</h1>');
```

## HTML Webpack Plugin and Open Browser Webpack Plugin ([source](https://github.com/charleserious/webpack-practices/tree/master/html.webpack.plugin.and.open.browser.webpack.plugin))

This demo shows you how to load 3rd-party plugins.

[html-webpack-plugin](https://github.com/ampedandwired/html-webpack-plugin) could create `index.html` for you, and [open-browser-webpack-plugin](https://github.com/baldore/open-browser-webpack-plugin) could open a new browser tab when Webpack loads.

main.js

```JavaScript
document.write('<h1>Hello World</h1>');
```

webpack.config.js

```JavaScript
var HtmlwebpackPlugin = require( 'html-webpack-plugin' );
var OpenBrowserPlugin = require( 'open-browser-webpack-plugin' );

module.exports = {

    entry: './main.js',

    output: {

        filename: 'bundle.js'

    },

    plugins: [
        
        new HtmlwebpackPlugin( {
            
            title: 'Webpack-demos',
            filename: 'index.html',

        } ),
        new OpenBrowserPlugin( {
            
            url: 'http://localhost:9020'
        } )
    ]
};
```

Launch the server.

```bash
$ cd html.webpack.plugin.and.open.browser.webpack.plugin
$ npm run dev
```

Now you don't need to write `index.html` by hand and don't have to open browser by yourself. Webpack did all these things for you.

## Enviroment flags ([source](https://github.com/charleserious/webpack-practices/tree/master/enviroment.flags))

You can enable some codes only in development with environment flags.

main.js

```JavaScript
document.write('<h1>Hello World</h1>');

if(__DEV__) {
    document.write(new Date());
}
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
var webpack = require( 'webpack' );

var devFlagPlugin = new webpack.DefinePlugin( {
    __DEV__: JSON.stringify( JSON.parse( process.env.DEBUG || 'false' ) )
} );

module.exports = {
    
    entry: './main.js',
    
    output: {

        filename: 'bundle.js'

    },

    plugins: [ devFlagPlugin ]

};
```

Now pass environment variable into webpack. Opening `enviroment.flags\package.json`, you should `scripts` field as following.

```JavaScript
// package.json

{
    // ...
    "scripts: {
        "dev": "npx cross-env DEBUG=true webpack-dev-server --open --port 9020"
    }
    // ...
}
```

Launch the server

```bash
$ cd enviroment.flags
$ npm run dev
```

## Code splitting ([source](https://github.com/charleserious/webpack-practices/tree/master/code.splitting))

For big web apps, it's not efficent to put all code into a single file. Webpack allows you to split a large JS file into serveral chunks. Especially, if some blocks of code are only required under some circumstances, these chunks could be loaded on demand.

Webpack uses `require.ensure` to define a split point ([official document](http://webpack.github.io/docs/code-splitting.html)).

```JavaScript
// main.js
require.ensure( ['./a'], function ( require ) {
    
    var content = require( './a' );
    document.open();
    document.write('<h1>' + content + '</h1>');
    document.close();

} );
```

`require.ensure` tells Webpack that `./a.js` should be separated from `bundle.js` and build into a single chunk file.

```JavaScript
// a.js
module.exports = 'Hello World';
```

Now Webpack takes care of the dependencies, output files and runtime stuff. You don't have to put any redundancy into your `index.html` and `webpack.config.js`.

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

    }

};
```

Launch the server

```bash
$ cd code.splitting
$ npm run dev
```

On the surface, you won't feel any diffeneces. However, Webpack actually builds `main.js` and `a.js` into diffenent chunks(`bundle.js` and `0.bundle.js`), and loads `0.bundle.js` from `bundle.js` when on demand.

## Code splitting with bundle-loader ([source](https://github.com/charleserious/webpack-practices/tree/master/code.splitting.with.bundle.loader))

Another way of coding splitting is using [bundle-loader](https://www.npmjs.com/package/bundle-loader).

```JavaScript
// main.js

// Now a.js is requested, it will be bundled into another file
var load = require( 'bundle-loader!./a.js' );

// To wait until a.js is available (and get the exports)
// you need to async wait for it.
load( function( file ) {
    document.open();
    document.write('<h1>' + file + '</h1>');
    document.close();
} );
```

`require( 'bundle-loader!./a.js' )` tells Webpack to load `a.js` from another chunk.

Now Webpack will build `main.js` into `bundle.js`, and `a.js` into `0.bundle.js`.


## Common chunk ([source](https://github.com/charleserious/webpack-practices/tree/master/common.chunk))

When untile scritps have common chunks, you can extract the common part into a separate file with [CommonsChunkPlugin](https://webpack.js.org/plugins/commons-chunk-plugin/), which is useful for browser caching and saving bandwidth.

```JavaScript
// main1.jsx
var React = require( 'react' );
var ReactDOM = require( 'react-dom' );

ReactDOM = render(
    <h1>Hello World</h1>,
    document.getElementById( 'a' );
);

// main2.jsx
var React = require( 'react' );
var ReactDOM = require( 'react-dom' );

ReactDOM = render(
    <h1>Hello Webpack</h1>,
    document.getElementById( 'b' );
);
```

index.html

```html
<html>
    <body>
        <div id="a"></div>
        <div id="b"></div>
    </body>
    <script src="commons.js"></script>
    <script src="bundle1.js"></script>
    <script src="bundle2.js"></script>
</html>
```

The above `common.js` is the common chunk of `main1.jsx` and `main2.jsx`. As you can imagine, `common.js` includes `react` and `react-dom`

webpack.config.js
```JavaScript
var webpack = require('webpack');

module.exports = {

    entry: {

        bundle1: './main1.jsx',
        bundle2: './main2.jsx'
        
    },
    output: {

        filename: '[name].js'

    },

    module: {

        rules: [
            {

                test: /\.js[x]?$/,
                exclude: /node_modules/,
                use: {

                    loader: 'babel-loader',

                    options: {

                        presets: ['es2015', 'react']

                    }

                }

            },

        ]

    },
    plugins: [

        new webpack.optimize.CommonsChunkPlugin({

            name: "commons",
            // (the commons chunk name)

            filename: "commons.js",
            // (the filename of the commons chunk)
        })

    ]
}
```

## Vendor chunk ([source](https://github.com/charleserious/webpack-practices/tree/master/vendor.chunk))

You can also extract the vendor libraries from a script into a separated file with CommonsChunkPlugin.

main.js

```JavaScript
var $ = require('jquery');
$('h1').text('Hello World');
```

index.html

```html
<html>
    <body>
        <h1></h1>
    </body>
    <script src="vendor.js"></script>
    <script src="bundle.js"></script>
</html>
```

webpack.config.js

```JavaScript
var webpack = require('webpack');

module.exports = {
    entry: {

        app: './main.js',
        vendor: ['jquery'],

    },
    output: {

        filename: 'bundle.js'

    },
    plugins: [
        new webpack.optimize.CommonsChunkPlugin( {
            name: 'vendor',
            filename: 'vendor.js'
        } )
    ]
};
```

In above codes, `entry.vender: [ 'jquery' ]` tells Webpack that `jquery` should be include in the common chunk `vender.js`.

If you want a module available as a global variable in every module, such as making `$` and `jQuery` available in every module without writing `require( 'jquery' )`. You should use `ProvidePlugin`([Official doc](https://webpack.js.org/plugins/provide-plugin/)) which automatically loads modules instead of having to import or require them everywhere.

```JavaScript
// main.js
$('h1').text('Hello World');

// webpack.config.js
var webpack = require('webpack');

module.exports = {
    entry: {

        app: './main.js'
    
    },

    output: {

        filename: 'bundle.js'

    },
    plugins: [
        
        new webpack.ProvidePlugin( {
            $: 'jquery',
            jQuery: 'jquery'
        } )

    ]
};
```

Of course, in this case, you should load `jquery.js` globally by yourself.

## Exposing Global Variables ([source](https://github.com/charleserious/webpack-practices/tree/master/exposing.global.variables))

If you want to use some global variables, and don't want to include them in the Webpack bundle, you can enable externals field in webpack.config.js ([official document](https://webpack.js.org/configuration/externals/)).

For example, we have a data.js.

```JavaScript
// data.js
var data = 'Hello World';
```

index.html
```html
<html>
    <body></body>
    <script src="data.js"></script>
    <script src="bundle.js"></script>
</html>
```

Attention, Webpack will only build bundle.js, but not data.js.

We can expose data as a global variable.

```JavaScript
// webpack.config.js
module.exports = {
    entry: './main.jsx',

    output: {

        filename: 'bundle.js'

    },
    module: {

        rules:[

            {

                test: /\.js[x]?$/,
                exclude: /node_modules/,
                use: {

                    loader: 'babel-loader',
                    options: {

                        presets: ['es2015', 'react']

                    }

                }

            }

        ]

    },
    
    externals: {
        // require('data') is external and available
        //  on the global var data
        'data': 'data'
    }

};
```

Now, you require data as a module variable in your script. but it actually is a global variable.

```JavaScript
// main.jsx
var data = require('data');
var React = require('react');
var ReactDOM = require('react-dom');

ReactDOM.render(
    <h1>{data}</h1>,
    document.body
);
```
You could also put react and react-dom into externals, which will greatly decreace the building time and building size of bundle.js.

## React router ([source](https://github.com/charleserious/webpack-practices/tree/master/react.router))

This demo uses webpack to build [React-router](https://github.com/rackt/react-router/blob/0.13.x/docs/guides/overview.md)'s official example.

Let's imagine a little app with a dashboard, inbox, and calendar.

```
+---------------------------------------------------------+
| +---------+ +-------+ +--------+                        |
| |Dashboard| | Inbox | |Calendar|      Logged in as Jane |
| +---------+ +-------+ +--------+                        |
+---------------------------------------------------------+
|                                                         |
|                        Dashboard                        |
|                                                         |
|                                                         |
|   +---------------------+    +----------------------+   |
|   |                     |    |                      |   |
|   | +              +    |    +--------->            |   |
|   | |              |    |    |                      |   |
|   | |   +          |    |    +------------->        |   |
|   | |   |    +     |    |    |                      |   |
|   | |   |    |     |    |    |                      |   |
|   +-+---+----+-----+----+    +----------------------+   |
|                                                         |
+---------------------------------------------------------+
```

webpack.config.js

```javascript
module.exports = {
  entry: './index.js',
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [ 'style-loader', 'css-loader' ]
      },
      {
        test: /\.jsx?$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['es2015', 'react']
          }
        }
      },
    ]
  }
};
```

index.js

```javascript
import React from 'react';
import { render } from 'react-dom';
import { BrowserRouter, Switch, Route, Link } from 'react-router-dom';

import './app.css';

class App extends React.Component {
  render() {
    return (
      <div>
        <header>
          <ul>
            <li><Link to="/app">Dashboard</Link></li>
            <li><Link to="/inbox">Inbox</Link></li>
            <li><Link to="/calendar">Calendar</Link></li>
          </ul>
          Logged in as Jane
        </header>
        <main>
          <Switch>
            <Route exact path="/" component={Dashboard}/>
            <Route path="/app" component={Dashboard}/>
            <Route path="/inbox" component={Inbox}/>
            <Route path="/calendar" component={Calendar}/>
            <Route path="*" component={Dashboard}/>
          </Switch>
        </main>
      </div>
    );
  }
};

class Dashboard extends React.Component {
  render() {
    return (
      <div>
        <p>Dashboard</p>
      </div>
    );
  }
};

class Inbox extends React.Component {
  render() {
    return (
      <div>
        <p>Inbox</p>
      </div>
    );
  }
};

class Calendar extends React.Component {
  render() {
    return (
      <div>
        <p>Calendar</p>
      </div>
    );
  }
};

render((
  <BrowserRouter>
    <Route path="/" component={App} />
  </BrowserRouter>
), document.querySelector('#app'));
```

index.html

```html
<html>
  <body>
    <div id="app"></div>
    <script src="/bundle.js"></script>
  </body>
</htmL>
```

Launch the server.

```bash
$ cd demo15
$ npm run dev
```

## Useful links

- [Webpack docs](https://webpack.js.org/concepts/)
- [webpack-howto](https://github.com/petehunt/webpack-howto), by Pete Hunt
- [SurviveJS Webpack book](https://survivejs.com/webpack/introduction/), by Juho Vepsäläinen
- [Diving into Webpack](https://web-design-weekly.com/2014/09/24/diving-webpack/), by Web Design Weekly
- [Webpack and React is awesome](http://www.christianalfoni.com/articles/2014_12_13_Webpack-and-react-is-awesome), by Christian Alfoni
- [Browserify vs Webpack](https://medium.com/@housecor/browserify-vs-webpack-b3d7ca08a0a9), by Cory House

## License

MIT

