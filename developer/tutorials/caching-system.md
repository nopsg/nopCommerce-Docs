# Caching System

Caching is a temporary storage area that stores previously requested/fetched data so that further request for the same data can be served from the cache storage rather then from the original source for faster response time. The device or server that is used to store data temporarily is called cache server. Cache server always resides in between client requesting for resource and server responding with resource. Cache server can lies anywhere between client and server like near to the client side or near to the data source or server side, that depends according to our requirement and software architecture. Caching is specially used to reduce network request and bandwidth consumption, store computed data that needs longer time to compute to reduce resource utilization like cpu or memory usage, to store data that is frequently requested by client in order to reduce database call. So in overall caching is used to decrease resource usage and increase response time of system.

There are numbers of caching system that uses different caching algorithms, techniques and write policy to increase the performance of caching system depending on different scenario. nopCommerce also uses the caching system to store frequently requested data into the cache in order to increase the performance and decrease the response time of the system. Since nopCommerce is a web application it does not have control of the client side or client device so it uses caching server near to server or data source. In caching system there are two ways for storing temporary data that is InMemory cache which stores cache data in RAM and persisted cache which stores cache data in persisted physical storage like system hard drive. Both have there pros and cons. InMemory is faster while retrieving and storing data but due to its limited storage capacity we cannot store large amount of data where as persisted data storage device has large amount of data storage space but it is vary slow during write and read data. nopCommerce provides both type of caching. If data we want to store in cache is large we can use persisted cache storage and if the data we want to store in cache is of small size and needs to be loaded faster then we can use InMemory cache.

## How Caching works

Whenever a client request for some resource to the server the request goes through the cache system, a cache hit occurs when the requested data can be found in a cache and a cache miss occurs when it cannot. Cache hits are served by reading data from the cache, which is faster than recomputing a result or reading from a slower data store. But if cash miss occurs then the request goes through the cash server to the real data source and when new data is sent back from the data source it first stores that data in cash server and sends same data to the client so that if the clients requests same data again now this time a cache hit occurs and data is served form the cash. However cash server can store data indefinitely until application restarts or can be expired in certain time and data will be refreshed from original data storage depending on the application setting.

## Cache Write Policy

Specially there are two types of cash write policy.

* **Write-through:** Write is done synchronously both to the cache and to the backing store.
* **Write-back (also called write-behind):** Initially, writing is done only to the cache. The write to the backing store is postponed until the modified content is about to be replaced by another cache block.

## Implementation of ICacheManager and IStaticCacheManager

In order to implement, first we initialize the cache manager as shown below. IStaticCacheManager extends ICacheManager and is generally used for caching between http requests (long term caching).

```csharp
private readonly ICacheManager _cacheManager;
private readonly IStaticCacheManager _staticCacheManager;

public ConstructorName(ICacheManager cacheManager, IStaticCacheManager staticCacheManager)
{
    _cacheManager = cacheManager;
    _staticCacheManager = staticCacheManager;
}
```

*ICacheManager* has various methods like *Get*, *Set*, *IsSet* and their implementation is as following.

**Get** method gets a cached item and if there is no value then it can be loaded and cached at the same time. It takes three parameters - key (string), acquire (Func), cacheTime (nullable int) and returns data with data type of cached item. The cache time integer value equals to the number of minutes. The default value is 60 minutes. For eg, to cache a key value for a day we set to 1440 and set it 0 if the value is not to be cached.  The return type of cached item can be explicitly set as well.
An example to get integer value from cache.

```csharp
// here cache time is not passed so it's value will be 60 minutes
_cacheManager.Get<int>("key_name", () => "function_call_to_load_data");

// here cache time is set to one day (1440)
_cacheManager.Get<int>("key_name", () => "function_call_to_load_data", 1440);
```

**Set** method is used to set a cache key value pair. This also takes three parameters - key (string), data (object), cacheTime (int). It is similar to *Get* except it requires data instead of the function call, cache time is required and doesn't return anything.
An example to set a cache.

```csharp
// sets a value for the specified key for the duration assigned to cache time
_cacheManager.Set("key_name", your_data_object_here, cache_time_value);
```

**IsSet** method is used to check if the value associated with the specified key has been cached before or not. It returns *true* if has been set before otherwise *false*. It accepts single parameter key (string).

```csharp
// checks if key exist or not. if not exists set it's value
if(!_cacheManager.IsSet("key_name"))
{
    // set cache value here
    ...
}
```

**Remove** method is used to remove cache value with the specified key. It takes a single parameter - key (string).

```csharp
// removes the value with the specified key from the cache
_cacheManager.Remove("key_name")
```

**RemoveByPrefix** method is used to remove cache items by key prefix. It accepts single parameter prefix (string). For eg, if we want to remove items from cache whose key starts with a specific text let's say *Nop.customers* (customers cached values). For this particular example, *key_prefix* will be replaced by *Nop.customers*.

```csharp
// removes cache by key prefix
_cacheManager.RemoveByPrefix("key_prefix")
```

**Clear** method will clear all cache data and doesn't require any parameters. There may occur a case where we may have to clear whole cache data and this is used for such purpose.

```csharp
// removes all cache data
_cacheManager.Clear()
```

*IStaticCacheManager* implements *ICacheManger* and it has only method *GetAsync* which is similar to *Get*. This also takes three parameters but the only difference is function should of of type **Task** (awaitable). It returns **Task<T>** (awaitable) where **T** is type of cached item.

```csharp
// without cache time
await _cacheManager.GetAsync("key_name", async () => "function_call_to_load_data");

// with cache time for a day (1440)
await _cacheManager.GetAsync("key_name", async () => "function_call_to_load_data", 1440);
```
