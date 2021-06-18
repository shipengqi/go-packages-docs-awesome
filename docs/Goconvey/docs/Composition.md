Writing self-documenting tests is remarkably easy with GoConvey.

### Examples

First, take a look through the [examples folder](https://github.com/smartystreets/goconvey/tree/master/examples) to get the basic idea. We'd recommend reviewing [isolated_execution_test.go](https://github.com/smartystreets/goconvey/blob/master/convey/isolated_execution_test.go) for a more thorough understanding of how you can compose test cases.


### Functions

See [GoDoc](http://godoc.org/github.com/smartystreets/goconvey) for exported functions and assertions. You'd be most interested in the [convey](http://godoc.org/github.com/smartystreets/goconvey/convey) package.


### Quick tutorial

In your test file, import needed packages:

```go
import(
	"testing"
	. "github.com/smartystreets/goconvey/convey"
)
```

(Notice the dot-notation for the `convey` package, for convenience.)

Since GoConvey uses `go test`, set up a Test function:

```go
func TestSomething(t *testing.T) {
	
}
```

To set up test cases, we use `Convey()` to define scope/context/behavior/ideas, and `So()` to make assertions. For example:

```go
Convey("1 should equal 1", t, func() {
	So(1, ShouldEqual, 1)
})
```

There's a working GoConvey test. Notice that we pass in the `*testing.T` object. Only the top-level calls to `Convey()` require that. For nested calls, you must omit it. For instance:

```go
Convey("Comparing two variables", t, func() {
	myVar := "Hello, world!"

	Convey(`"Asdf" should NOT equal "qwerty"`, func() {
		So("Asdf", ShouldNotEqual, "qwerty")
	})

	Convey("myVar should not be nil", func() {
		So(myVar, ShouldNotBeNil)
	})
})
```

If you haven't yet implemented a test or scope, just set its function to `nil` to [[skip|Skip]] it:

```go
Convey("This isn't yet implemented", nil)
```


### [[Next|Assertions]]

Next, you should learn about the [[standard assertions|Assertions]]. You may also skip ahead to [[executing tests|Execution]] or to [[Skip|Skip]] to make testing more convenient.
