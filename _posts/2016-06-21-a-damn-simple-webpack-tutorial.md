---
layout: post
title:  ' A damn simple webpack tutorial '
categories: [webpack, babel, es2015]
---

I need perform operations (uglify, bundle, etc. )on my Javascript files. So I learned Browserify. During the learning of Browserify, I realized Gulp would be a better choice because it is newer. So I switched to Gulp immediately. Then webpack popped up... That is why I am discussing webpack now.

OK. Let us start. If you are eager to use webpack right now, scroll down the bottom.

**Step 1: Install Webpack.**

```
$npm install webpack -g
```

`-g` means install webpack globally. You can choose not to do so by skipping `-g`. By the way, we have seen `node install package -g` and `node install package --save` which are both for web package installing. An interesting question arises here: how to install a package from you local directory? Here you go:

```
$npm install path/to/local-folder/my-module
```
After that you will see the difference in your 'package.json':

```
"dependencies": {
    ...
    "my-module-name": "file:path/to/local-folder/my-module",
    ...
  }
```

**Step 2: Using webpack on the command line.**

Create files:

main.js:

```
document.write("I love webpack!");
```

index.html:

```
<html>
    <body>
        <script src="bundle.js"></script>
    </body>
</html>
```

You may ask where is "bundle.js" from? It is transformed from "main.js". Why don't we simply use '<script src="main.js"></script>'? Because in a non-trivial project, we will have quite a few javascipt or other files (such as css files, etc.) that we want to put into a single file (called "bundle" file) such that users only need a single load of the "bundle" file instead of multiple loads of different JS or other files. Another reason is we can utilize the back end NodeJS packages for the front end development. These back end NodeJS packages should be translated to JS that can be understood/executed by browsers, which is implemented by tools like `webpack`.

Here is the command of using webpack on the command line:

```
$webpack ./main.js bundle.js
```

It is always a good idea to perform all the transformation operations in a configuration file.

**Step 3: Using webpack in a configuration file.**

You just need `webpack` which will automatically execute a default configuration file `webpack.config.js`.

```
$webpack
```

webpack.config.js:

```
var webpack = require('webpack');

module.exports = {
  entry: [
    './main.js'
  ],
  output: {
    filename: 'bundle.js'
  },
  module: {
    loaders: [{
      test: /\.js$/,
      loader: 'babel',
      exclude: /node_modules/,
      query: {
          presets: ['es2015']
        }
    }]
  }
};
```

The above webpack config file will transform JS files using tool `babel` to a bundled file `bundle.js`. JS files under "/node_modules/" will not be transformed. If you have not heard of babel, see the [link](https://babeljs.io/blog/2015/10/31/setting-up-babel-6).

**A few things we need pay attentions:**

* 'webpack.config.js': default configuration file executed by command `webpack`.
* `entry`: root file that webpack will be starting parsing.
* `output`: the bundle file name.
* `loaders`: hook up the engine with to-transform files.

**More on `loader`**

As the real engine of webpack, `loader` usually has 4 parts:

* `test`: specify the files that requires transformation.
* `loader`: the engine that will parse your file
* `exclude`/`include`: which files will be included/exclude in the transformation.
* `query`: optional. options passed to `babel`. Here I used "es2015" which means the to-transform file contains "es2015" features so I need babel to transform it with "babel" plugin "es2015".

To use the engine specified in `loader`, you need install the engine and its options first.

```
npm install babel-loader babel-core babel-preset-es2015 babel-preset-react --save-dev
```

Now you can simply issue `webpack` on the terminal and a 'bundle.js' will be generated.

**webpack for the impatient**

Go to your root directory to create a configuration file "webpack.config.js".
Then:

```
$npm install webpack -g
$npm install babel-loader babel-core babel-preset-es2015 --save-dev
$webpack
```
