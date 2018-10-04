# `bindings-loader`

A webpack loader for node addon modules that handles the recommended
`require("bindings")("myaddon.node")` pattern.

Very early, use at your own risk, but feedback welcome!

## Usage

No options at the moment, always emits all `.node` files directly in the output
directory.

```js
// webpack.config.js
module.exports = {
  externals: [
    // Don't try to pack referenced .node files
    function(context, request, callback) {
      if (/\.node$/.test(request)) {
        return callback(null, `commonjs ${request}`);
      }
      return callback();
    },
  ],
  module: {
    rules: [
      // ...
      // Rewrites and emits
      {
        test: /\.js$/,
        loader: "bindings-loader",
      },
    ],
  },
};
```

In theory this should also work well chained with 'node-loader' to bundle the
binary into the JS bundle, but I haven't tried.

## Behavior

Recognizes `require('bindings')('foo.node')`, uses (roughly)
`require('bindings')({ path: true, bindings: 'foo.node' })` to get the built
addon module, re-writes the require to `require('./foo.node')`, and emits the
referenced file at that relative path.

Should also handle `require('bindings')({ bindings: 'foo.node', ... })`,
including with `path: true` to just get the path, but that has even less testing
than the rest of this module.

No options are supported yet, in theory this should support most everything
`file-loader` does to control the `.node` file output.
