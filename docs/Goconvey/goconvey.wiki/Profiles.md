## GoConvey Test Package Profiles

When using the [[web UI|Web-UI]] it would be nice to be able to customize the flags used when running `go test` in a specific
package*. It would also be nice to somehow mark a package as persistently ignored or disabled so that when the UI starts sometime in the future, you don't have to click ignore on that package again. Well, there's a way to do both of those things by creating a text file in that package that satisfied the following regex:

	.+\.goconvey

Examples of good names:

- `example.goconvey`
- `hi.goconvey`

Examples of bad names:

- `.goconvey`
- `hi.txt`

The contents of that text file may include blank lines, commented lines (start the line with a `#` or `//`), test flags (`-short`, etc...) or the word `ignore` (casing is unimportant). The only hard and fast rule is that if you include the word `ignore` it must come before any test flags. 

The result of creating such a file is that if `ignore` is found as the first non-comment, non-blank line, the package will be ignored by the GoConvey server until that line is removed or commented. Otherwise, all non-blank, non-comment lines are treated as arguments to be used whenever calling `go test` on your package (like when you save a file). Most of these arguments will probably arguments defined by the golang testing command but you can include arbitrary arguments to your package. There are only a few caveats to be aware of:

1. `-v` is always used so there's no need to ever include this flag.
1. `-cover`, `-covermode` and `-coverprofile` are specified by the GoConvey server so including these flags will have no effect. (Let me know if you really want to be able to specify `-covermode` for values other than `set` which is what GoConvey uses by default.)
1. `-tags` will be passed appropriately through to `go test`, `go list`, etc. calls, so if you need a special tag to build your package or your tests, it should get picked up correctly.
1. Many of the profiling/benchmarking flags haven't really been used much by the core goconvey developers so we aren't sure if they will work as they normally do when applied to a goconvey package profile.

Far from being an exhaustive list, here are some intended use cases:

- Using the `-short` flag can allow you to toggle execution of long-running integration tests.
- Using the `-run` flag can allow you to focus on specific test functions in a package. Combined with `FocusConvey` you could limit test execution to a single test case within a single test function--handy if displaying lots of debug information.
- Using the `-timeout` flag may help prevent manual restarts of the goconvey server in the event of a mistaken infinite loop (come on, admit it--you know it's happened to you!).

Please see the `examples.goconvey` profile in the examples package ([`github.com/smartystreets/goconvey/examples`](https://github.com/smartystreets/goconvey/tree/master/examples)) for, well, an example. Happy testing!

* A profile only applies to the containing package (same folder), not any nested packages (sub-folders).