# YTKResourceCache-Android

A cache library that works with [YTKWebView](https://github.com/yuantiku/YTKWebView-Android). You can also use it without YTKWebView to load the cache resources separately.

[中文说明](https://github.com/yuantiku/YTKResourceCache-Android/blob/master/README-CHINESE.md)

## Usage

### Resource cache ability

```kotlin
val cacheStorage = FileCacheStorage(
    context,
    mappingRule = MappingRule.Default,
    cacheDir = context.getCacheDir().getAbsolutePath(),
)

YTKWebView(context)
    .setCacheReader(cachesStorage.cacheReader)
    .attach(webView)
```

You can also create your own cache storage like `MemoryCacheStorage` by implementing `CacheStorage` interface and then create your own `CacheResourceReader` and `CacheResourceWriter`.

```kotlin
interface CacheStorage{
    val cacheReader: CacheResourceReader

    val cacheWriter: CacheResourceWriter
}

interface CacheResourceReader {
    fun getStream(url: String?): InputStream?
}

interface CacheResourceWriter{
    fun getStream(url: String?): OutputStream?
}
```

`FileCacheStorage`  use `DefaultCacheResourceReader`  for reading cache and `FileResourceWriter`  for writing cache. The `DefaultCacheResourceReader` first looks up in the assets directory, then in the local cache directory, by a specific mapping rule that maps remote urls to local file paths.  The `FileResourceWriter` use disk file to store cache so make sure your application has `WRITE_EXTERNAL_STORAGE` permission.

### Resource download ability

YTKResourceCache offers you the ability to download resource via internet, you can download files by url like this:

```kotlin
val urlList = listOf("http://...", "http://...")
val downloadTask = DownloadTask(urlList, cacheStorage,
    onSuccess = {

    },
    onFailed = { it:Throwable -> 

    },
    onProgress = { progressList: List<Progress> ->

    }
)
downloadTask.start()
```

To cancel  download task, simply use:

```kotlin
downloadTask.cancel()
```

Once you use `DownloadTask` to download resources from internet, the resource is cached by `CacheStorage`. You can later use `CacheResourceReader`  to quickly get a copy of the resource from cache.
