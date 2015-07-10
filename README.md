# cache-service-node-cache

* A [node-cache](https://github.com/tcs-de/nodecache) plugin for [cache-service](https://github.com/jpodwys/cache-service)
* AND a standalone node-cache wrapper

# Basic Usage

Require and instantiate
```javascript
var csNodeCache = require('cache-service-node-cache');

var cacheModuleConfig = {defaultExpiration: 60};
var nodeCache = new csNodeCache(cacheModuleConfig);
```

Cache!
```javascript
nodeCache.set('key', 'value');
```

# Benefits of Using `cache-service-node-cache`

If you're using `cache-service-node-cache` with `cache-service`, the benefits are obvious. However, there are also a couple of reasons you might prefer it to using vanilla [node-cache](https://www.npmjs.com/package/node-cache):

* It adds an excellent `.mset()` implementation which allow you to set expirations on a per key, per function call, and/or per `cache-service-node-cache` instance basis (Vanilla node-cache does not offer `.mset()` at all).
* Built-in logging with a `verbose` flag.

# Cache Module Configuration Options

## type

An arbitrary identifier you can assign so you know which cache is responsible for logs and errors.

* type: string
* default: 'node-cache'

## defaultExpiration

The expiration to include when executing cache set commands. Can be overridden via `.set()`'s optional expiraiton param.

* type: int
* default: 900
* measure: seconds

## verbose

> When used with `cache-service`, this property is overridden by `cache-service`'s `verbose` value.

When false, `cache-service-node-cache` will log only errors. When true, `cache-service-node-cache` will log all activity (useful for testing and debugging).

* type: boolean
* default: false

# API

Although this is a node-cache wrapper, its API differs in some small cases from node-cache's own API because all `cache-service`-compatible cache modules match [`cache-service`'s API](https://github.com/jpodwys/cache-service#api).

## .get(key, callback (err, response))

Retrieve a value by a given key.

* key: type: string
* callback: type: function
* err: type: object
* response: type: string or object

## .mget(keys, callback (err, response))

Retrieve the values belonging to a series of keys. If a key is not found, it will not be in `response`.

* keys: type: an array of strings
* callback: type: function
* err: type: object
* response: type: object, example: {key: 'value', key2: 'value2'...}

## .set(key, value [, expiraiton, callback])

Set a value by a given key.

* key: type: string
* callback: type: function
* expiration: type: int, measure: seconds
* callback: type: function

## .mset(obj [, expiration, callback])

Set multiple values to multiple keys

* obj: type: object, example: {'key': 'value', 'key2': 'value2', 'key3': {cacheValue: 'value3', expiration: 60}}
* callback: type: function

This function exposes a heirarchy of expiration values as follows:
* The `expiration` property of a key that also contains a `cacheValue` property will override all other expirations. (This means that, if you are caching an object, the string 'cacheValue' is a reserved property name within that object.)
* If an object with both `cacheValue` and `expiration` as properties is not present, the `expiration` provided to the `.mset()` argument list will be used.
* If neither of the above is provided, each cache's `defaultExpiration` will be applied.

## .del(keys [, callback (err, count)])

Delete a key or an array of keys and their associated values.

* keys: type: string || array of strings
* callback: type: function
* err: type: object
* count: type: int

## .flush([cb])

Flush all keys and values from.

* callback: type: function

# More Node-Cache Methods

If you need access to one of node-cache's other functions, you can get at the underlying [`node-cache` instance](https://github.com/tcs-de/nodecache) by tapping into the `.db` property like so:

```javascript
var underlyingNodeCacheInstance = nodeCacheModule.db;
underlyingNodeCacheInstance.SOME_OTHER_NODE_CACHE_FUNCTION();
```
