When a customer emailed Steve Jobs about bad reception on the iPhone 4, [Jobs replied and basically told him](http://www.engadget.com/2010/06/24/apple-responds-over-iphone-4-reception-issues-youre-holding-th/), you're holding it wrong.

Similarly, here are some of the wrong ways to "hold" GoConvey, some of which are not very obvious (and understandably so).

### &bull; Go files not in `src` folder in a Go workspace

When using the web UI, Go files being tested must be in the GOPATH under the `src` directory. The server expects the `src` directory to be present after the GOPATH so it can determine the full package name.


### &bull; Calling `panic(nil)`

Never, never, never call `panic(nil)` because GoConvey can't recover the error (it thinks the `Convey` passed) see [issue 98](https://github.com/smartystreets/goconvey/issues/98) and especially [this Stack Overflow question](http://stackoverflow.com/questions/19662527/how-to-detect-panicnil-and-normal-execution-in-deferred-function-go).