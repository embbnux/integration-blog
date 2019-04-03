---
layout: post
title:  "How to build a JS library with Webpack -- RingCentral C2D library"
date:   2019-03-30 10:00:00 +0800
categories: blog
author: Embbnux Ji
---

![clicktodial](https://user-images.githubusercontent.com/7036536/51652788-d2627200-1fcb-11e9-8ba3-9e50baeaf8a6.png)

Rencently, we publish RingCentral C2D library to help developers integrate RingCentral Click To Dial in browser. This library is built with Webpack 4 and Typescript. This article will share how to build a JS library which support CDN and NPM ways by introducing how we build RingCentral C2D.

## Introduction about RingCentral C2D

RingCentral C2D aims to help developers to get phone numbers in web page and pass it to our RingCentral integration app.

#### Project Links:

* Github Repo: [https://github.com/ringcentral/ringcentral-c2d](https://github.com/ringcentral/ringcentral-c2d)
* Demo Page: [https://ringcentral.github.io/ringcentral-c2d/](https://ringcentral.github.io/ringcentral-c2d/)

#### Built with:

* Webpack 4
* Typescript

## Using webpack 4

### Output option

Webpack 4 provides very easy way to build JS library with several options:

```js
// webpack config object
config.ouput = {
  filename: 'index.js',
  path: path.resolve(__dirname, 'build'),
  library: 'RingCentralC2DInject',
  libraryTarget: 'umd',
  libraryExport: 'default'
}
```

In output config, `filename` and `path` define where to output your library. `config.library` and `config.libraryTarget` property tells webpack you are building a library not an app. And with `umd`, this library will support to used with CommonJS, AMD and as global variable. When this library is used with CDN script, it will export `RingCentralC2DInject` as global variable.

With `libraryExport: 'default'`, webpack will add `default` in module exports. So you can use as `import RingCentralC2D from 'ringcentral-c2d'`.

### TypeScript loader

Because our library source codes were written with TypeScript, we need to make webpack support it. Just add TS loader in `config.module.rules`:

```js
config.module = {
  rules: [
    {
      test: /\.ts?$/,
      use: 'ts-loader',
      exclude: /node_modules/
    }
  ]
}
```

After that, webpack support to compile and bundle TS codes.

### Externals option

Our library is based on `libphonenumber-js` library. But we should not bundle it into our library. Developers should allow to import it themselves. We can use `externals` option:

```
config.externals = {
  'libphonenumber-js': {
    commonjs: 'libphonenumber-js',
    commonjs2: 'libphonenumber-js',
    amd: 'libphonenumber-js',
    root: 'libphonenumber'
  }
}
```

We allow developers to import `libphonenumber-js` with CommonJS, AMD and global variable.

So developers can use our library with `libphonenumber-js` CDN version:

```
<script src="https://unpkg.com/libphonenumber-js@1.7.7/bundle/libphonenumber-min.js"></script>
<script src="https://unpkg.com/ringcentral-c2d@0.0.3/build/index.js"></script>
```

If developers use our npm package with webpack, `libphonenumber-js` will be installed automatically, because we declare `libphonenumber-js` in our dependencies inside `package.json`.

## Publish

#### NPM

Now we have our library ready, so we need to publish it to make developer easy to install it. [NPM](https://www.npmjs.com/) is package manager for javascript. We can use `npm publish` command to publish our package with `package.json`. Before we publish, we need to make sure our codes in `NPM` is compiled.

Add `prepublish` script in `package.json`:

```json
{
  "scripts": {
    "prepublish": "tsc && webpack",
    "tsc": "tsc",
    "build": "webpack"
  }
}
```

#### CDN

For CDN version, we can use [UNPKG](https://unpkg.com/). If you publish your package in `NPM`, you will get `UNPKG` URL like:

```
unpkg.com/:package@:version/:file
```

For example, our CDN version script URI: `https://unpkg.com/ringcentral-c2d@0.0.3/build/index.js`.

## Summary

In this article, we share about how to build and publish a JS library. For more, you can also get full codes in this [repo](https://github.com/ringcentral/ringcentral-c2d).