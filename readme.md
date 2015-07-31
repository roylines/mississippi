# mississippi

a collection of useful stream utility modules. learn how the modules work using this and then pick the ones you want and use them individually

## usage

```js
var miss = require('mississippi')
```

## methods

### `pipe`

#### `miss.pipe(stream1, stream2, stream3, ..., cb)`

Pipes streams together and destroys all of them if one of them closes. Calls `cb` with `(error)` if there was an error in any of the streams.

When using standard `source.pipe(destination)` the source will _not_ be destroyed if the destination emits close or error. You are also not able to provide a callback to tell when then pipe has finished.

`miss.pipe` does these two things for you, ensuring you handle stream errors 100% of the time (unhandled errors are probably the most common bug in most node streams code)

#### original module

`miss.pipe` is provided by [`require('pump')`](https://npmjs.org/pump)

#### example

```
// lets do a simple file copy
var fs = require('fs')

var read = fs.createReadStream('./original.zip')
var write = fs.createWriteStream('./copy.zip')

// use miss.pipe instead of read.pipe(write)
miss.pipe(read, write, function (err) {
  if (err) return console.error('Copy error!', err)
  console.log('Copied successfully')
})
```

### `pipeline`

#### `var pipeline = miss.pipeline(stream1, stream2, stream3, ...)`

builds a pipeline from all the transform streams passed in as arguments by piping them together and returning a single stream object that lets you write to the first stream and read from the last stream

if any of the streams in the pipeline emits an error or gets destroyed, or you destroy the stream it returns, all of the streams will be destroyed and cleaned up for you

#### original module

`miss.pipeline` is provided by [`require('pumpify')`](https://npmjs.org/pumpify)

#### example

```
// first create some individual transform streams (note: these two modules are fictional)
var imageResize = require('image-resizer-stream')({width: 400})
var pngOptimizer = require('png-optimizer-stream')({quality: 60})

// instead of doing a.pipe(b), use pipelin
var resizeAndOptimize = miss.pipeline(imageResize, pngOptimizer)
// `resizeAndOptimize` is a transform stream. when you write to it, it writes to `imageResize`. when you
// read from it, it reads from `pngOptimizer`. it handles piping all the streams together for you

// use it like any other transform stream
var fs = require('fs')

var read = fs.createReadStream('./image.png')
var write = fs.createWriteStream('./resized-and-optimized.png')

miss.pipe(read, resizeAndOptimize, write, function (err) {
  if (err) return console.error('Image processing error!', err)
  console.log('Image processed successfully')
})
```

### `duplex`

todo

### `through`

todo

### `concat`

todo
