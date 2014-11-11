# Routr [![Build Status](https://travis-ci.org/yahoo/routr.svg?branch=master)](https://travis-ci.org/yahoo/routr) [![Dependency Status](https://david-dm.org/yahoo/routr.svg)](https://david-dm.org/yahoo/routr) [![Coverage Status](https://coveralls.io/repos/yahoo/routr/badge.png?branch=master)](https://coveralls.io/r/yahoo/routr?branch=master)


Routr library is an implementation of router-related functionalities that can be used for both server and client. It follows the same routing rules as [Express](http://expressjs.com/) by using the same library. This library does not use callbacks for routes, instead just mapping them to string names that can be used as application state and used within your application later. For instance in Flux, the current route would be held as state in a store.

## Usage

For more detailed examples, please check out [example applications](https://github.com/yahoo/routr/tree/master/examples);

```javascript
var Router = require('routr'),
    router,
    route,
    path;

router = new Router({
    view_user: {
        path: '/user/:id',
        method: 'get',
        foo: {
            bar: 'baz'
        }
    },
    view_user_post: {
        path: '/user/:id/post/:post',
        method: 'get'
    }
});

// match route
route = router.getRoute('/user/garfield');
if (route) {
    // this will output:
    //   - "view_user" for route.name
    //   - {id: "garfield"} for route.params
    //   - {path: "/user/:id", method: "get", foo: { bar: "baz"}} for route.config
    console.log('[Route found]: name=', route.name, 'params=', route.params, 'config=', route.config);
}

// generate url path from route
// "path" will be "/user/garfield/post/favoriteFood"
path = router.makePath('view_user_post', {id: 'garfield', post: 'favoriteFood'});

```

## Object.freeze
We use `Object.freeze` to freeze the router and route objects for non-production environments to ensure the immutability of these objects.

For production environments, it is recommended to use tools like [envify](https://github.com/hughsk/envify) along with [uglify](https://github.com/mishoo/UglifyJS) as part of your build process to strip out the production specific code for performance benefits.

We use `if (process.env.NODE_ENV !== 'production')` to wrap around `Object.freeze()`, so that you can use various tools to build the code for different environments:

### Build with Webpack
Two main utility plugins:
   * use [DefinePlugin](http://webpack.github.io/docs/list-of-plugins.html#defineplugin) to define the value for `process.env`
   * use [UglifyJsPlugin](http://webpack.github.io/docs/list-of-plugins.html#uglifyjsplugin) to remove dead code.

Example of the webpack configuration:
```js
    plugins: [
        new webpack.DefinePlugin({
            'process.env': {
                NODE_ENV: JSON.stringify('production')
            }
        }),
        new webpack.optimize.UglifyJsPlugin(),
        ...
    ]
```

### Build with Browserify
Similar to webpack, you can also use the following two utils with your favorite build system:
   * use [envify](https://github.com/hughsk/envify) to set `process.env.NODE_ENV` to the desired environment
   * use [uglifyjs](https://github.com/mishoo/UglifyJS2) to remove dead code.

Command-line example:
```bash
$ browserify index.js -t [ envify --NODE_ENV production  ] | uglifyjs -c > bundle.js
```


## License

This software is free to use under the Yahoo! Inc. BSD license.
See the [LICENSE file][] for license text and copyright information.

[LICENSE file]: https://github.com/yahoo/routr/blob/master/LICENSE.md

Third-pary open source code used are listed in our [package.json file]( https://github.com/yahoo/routr/blob/master/package.json).

