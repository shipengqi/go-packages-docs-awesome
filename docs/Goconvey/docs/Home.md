Welcome to GoConvey, a yummy testing tool for gophers.

**[[Documentation & tutorial|Documentation]]**


### Main Features

- Integrates with `go test`
- Readable, colorized console output
- Fully-automatic web UI
- Huge suite of regression tests
- Test code generator

View a **[[comprehensive table of all features|Features-Table]]** compared to other Go testing tools.



### Get going in 25 seconds

1. In your terminal:
```sh
# make sure your GOPATH is set

$ cd <project path>
$ go get github.com/smartystreets/goconvey
$ $GOPATH/bin/goconvey


```
2. In your browser:
```sh
http://localhost:8080
```
If you have existing Go tests, they will run automatically and the results will appear in your browser.




### Your first GoConvey test

Open any `_test.go` file and put this in it, customizing your package declaration:

```go
package package_name

import (
	"testing"
	. "github.com/smartystreets/goconvey/convey"
)

func TestIntegerStuff(t *testing.T) {
	Convey("Given some integer with a starting value", t, func() {
		x := 1

		Convey("When the integer is incremented", func() {
			x++

			Convey("The value should be greater by one", func() {
				So(x, ShouldEqual, 2)
			})
		})
	})
}
```
Save the file, then glance over at your browser window, and you'll see that the new tests have already been run.

Change the assertion (the line with `So()`) to make the test fail, then see the output change in your browser.

You can also run tests from the terminal as usual, with `go test`. If you want the tests to run automatically in the terminal, check out [[the auto-test script|Auto-test]].


### Required Reading

If I could ensure that every GoConvey user read only one bit of code from this repository it would be the [isolated execution tests](https://github.com/smartystreets/goconvey/blob/master/convey/isolated_execution_test.go). Those tests are the very best documentation for the GoConvey execution model.

### Full Documentation

See the [[documentation index|Documentation]] for details about assertions, writing tests, execution, etc.

