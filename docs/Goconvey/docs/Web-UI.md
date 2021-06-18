The web UI is a powerful, elegant tool for viewing Go test results in your browser.


### Quick start

(Assuming you already set your GOPATH and did `go get github.com/smartystreets/goconvey`...)

**In your terminal:**

- `cd` to your project's path
- `go install github.com/smartystreets/goconvey`

From now on, you need only run the `goconvey` server:

```sh
$GOPATH/bin/goconvey
```

Then open your browser to:

```sh
http://localhost:8080
```



### Features

- Customize the watched directory
- Automatically updates when `.go` files are changed
- Test code generator
- Browser notifications (with enable/disable and the option to notify on any status, only success, or only panic/failure)
- Colored diff on most failing tests' outputs
- Nested display of GoConvey tests for easy reading
- Supports traditional Go tests
- Responsive layout so you can squish the browser next to the code if you have to
- Panics, failed builds, and failed tests are highlighted
- Silky-smooth appearance and transitions
- Direct link to the problem lines (opens your favorite editor--[some assembly required](Opening-files-in-your-editor-from-the-Web-UI))

[Scroll to the bottom for a graphical feature tour.](Web-UI#graphical-feature-tour)




### Code generator

Click the "Code Gen" link in the top-right corner to open the code generator. Type your test descriptors in the textbox, and use Tab to indent. Test stubs will automatically be created for you which you can then copy+paste into your Go test file.

The idea is to describe your program's behavior in a natural, flowing way.

For example (make sure to convert spaces to tabs, as GitHub transformed them to spaces):

	Test Integer Stuff
		Subject: Integer incrementation and decrementation
			Given a starting integer value
				When incremented
					The value should be greater by one
					The value should NOT be what it used to be
				When decremented
					The value should be lesser by one
					The value should NOT be what it used to be

There are a few to notice:

 - Lines starting with "Test" (case-sensitive), without indentation, are treated as the name of the test function
 - Tab indentation defines scope
 - Assertions are not made here; you'll do that later after pasting the generated code into your `_test.go` file.



### Graphical feature tour

![GoConvey web UI](http://i.imgur.com/O7uVvoq.png)


### Server command line flags

```sh
$GOPATH/bin/goconvey -help

Usage of goconvey:
  -cover=true: Enable package-level coverage statistics. Warning: this will obfuscate line number reporting on panics and build failures! Requires Go 1.2+ and the go cover tool. (default: true)
  -gobin="go": The path to the 'go' binary (default: search on the PATH).
  -host="127.0.0.1": The host at which to serve http.
  -packages=10: The number of packages to test in parallel. Higher == faster but more costly in terms of computing. (default: 10)
  -poll=250ms: The interval to wait between polling the file system for changes (default: 250ms).
  -port=8080: The port at which to serve http.

```