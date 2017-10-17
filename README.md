# dedup-resolve-webpack-plugin
Resolves copies of the same module in different locations to a single path.

This plugin allows to solve the issue descrived at https://github.com/webpack/webpack/issues/5593.
When you use nice libA in your project, and depA and depB also use this cool libA then you will have 3 copies of libA in your resulting bundle. No uglification, tree shaking and other cool features will help you to avoid code duplication.
```
projectA
- node_modules
-- libA
-- depA
---- node_modules/libA
-- depB
---- node_modules/libA 
```
This plugin will check the dependecies, their versions and will use only one (first) location of libA.

## Usage:
1. Install the plugin as a development dependency:

    **Yarn:**
    `yarn add @roboburned/dedup-resolve-webpack-plugin -D`

    **NPM:**
    `npm install @roboburned/dedup-resolve-webpack-plugin -D`

2. Require a plugin in webpack configuration file and register it as **resolve** plugin.
```js
var webpack = require('webpack');
var DedupPlugin = require('dedup-resolve-webpack-plugin');

module.exports = {
  entry: [
    './src/index.js'
  ],
  module: {
    loaders: [{
      test: /\.js?$/,
      exclude: /node_modules/,
      loader: 'babel-loader'
    }]
  },
  resolve: {
    extensions: ['.js'],
    plugins: [new DedupPlugin({verbose: true})]
  },
  plugins: [/*DO NOT ADD the plugin here. Add it to resolve/plugins section.*/]
}
```
3. If you enabled verbose flag then you will see the output simmilar to the following. It means that both a and b will use debug library from parent location.
```bash
[Dedup]: d:\Projects\webpack-dedupe\node_modules\b\node_modules\debug
      => d:\Projects\webpack-dedupe\node_modules\debug
[Dedup]: d:\Projects\webpack-dedupe\node_modules\a\node_modules\debug
      => d:\Projects\webpack-dedupe\node_modules\debug
```
