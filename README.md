# To.ImageCache
A simple CommonJS module to cache images a little less temporarily, and configurable

## Setup

Add `To.ImageCache` CommonJS module to your project. Either download this project, or install using `gittio`

`gittio install To.ImageCache`

Follow regular steps to include it in your project

## Configuration

The configuration is simple: No configuration is needed, unless you want to change something. 

All current properties available shown below

```js
require('To.ImageCache').config({
	debug: true, //default "false"
	expireTime: 100000, // time in seconds, default 43200 = 12 hours
	folder: 'CustomFolder', // folder to store the cache in, default "ToCache"
	remoteBackup: true // iOS Only do you want the images to be backed up to iCloud?
});
```

The config can be changed at all times, between different files so support multiple folders, expiration times and file specfic backup properties. You only have to pass what you want to change from now on. No need to pass all config properties

If you want to make a copy of the config, so you can restore it later, use the getter

```js
var config = require('To.ImageCache').config();
```

All available images will be stored in Properties

```js
 Ti.App.Properties.getList('To.ImageCache.ImageList');
 ```
 
 There is, however, no need to call this list manually unless you really want to. Best to use build in functionality
 
## Usage
 
 There are 2 methods to use the module. 
 
#### Only call the module when the image is needed, it will then, return a blob of the image while you wait. 
  
Note: This might cause delays in your app and is only recommended for smaller images

```js
var blob = require('To.ImageCache').remoteImage('http://example.com/image.jpg');
```

This will cache the image the first time it is called, and the next time you request this same file it will return the same blob, but this time stored locally

#### Pre-cache images before they are needed

This method is preferred for bigger images, as this can happen in the background

```js
require('To.ImageCache').cache('http://example.com/image.jpg');
```

This function will NOT return a blob, but will cache the file using `XHR`.

Aditonally, you can add a timeout and callback function: 

```js
require('To.ImageCache').cache('http://example.com/image.jpg', 25000, function(blob){
	$.imageView.image = blob;
});
```

## Clearing Cache

You want, of course, to clear the cache when needed. This is *NOT* done automatically. 

The best function to call is `flushExpired`

```js
require('To.ImageCache').flushExpired();
```

This function will remove all files older than the expired time.

You can also clear all cache

```js
require('To.ImageCache').clearCache();
```

This will remove all cached files *regardless* of expired time.

You can also remove a single file by URL:

```js
require('To.ImageCache').removeRemote('http://example.com/image.jpg');
```
This will also *NOT* take expiry time in consideration

If you know the filename (which is internally generated, so you probably won't), you can remove by filename too

```js
require('To.ImageCache').removeFile('a128a10e623e08c9b5b704bf162d770e');
```

## Advanced

You can fetch the entire cache size, in bytes, from the module. This could, for example, be used to display users so they can be aware how much cache is present, and you could give the users the ability to remove cache manually

```js
require('To.ImageCache').cacheSize()
```

You can also, at a later point, update the config. Expire time is per-file, and stored per-file. So changing it later will not update the currently stored files. Might be usefull for different expiry times.

Also the folder can be changed multiple times. Folders are also stored per file, so changable all the time.
