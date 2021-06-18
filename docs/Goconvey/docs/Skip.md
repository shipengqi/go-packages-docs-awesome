Sometimes it's nice to ignore or skip an entire scope or some assertions here and there. This is easy with GoConvey.



### Skipping `Convey` registrations

Changing a `Convey()` to `SkipConvey()` prevents the `func()` passed into that call from running. This also has the consequence of preventing any nested `Convey` registrations from running. The reporter will indicate that the registration was skipped.

```go
SkipConvey("Important stuff", func() {			// This func() will not be executed!
    Convey("More important stuff", func() {
        So("asdf", ShouldEqual, "asdf")
    })
})
```

Using `SkipConvey()` has nearly the same effect as commenting out the test
entirely. However, this is preferred over commenting out tests to avoid the
usual "declared/imported but not used" errors. Usage of `SkipConvey()` is
intended for temporary code alterations.




### Unimplemented `Convey` registrations

When composing `Convey` registrations, sometimes it's convenient to use `nil` instead of an actual `func()`. Not only does this skip the scope, but it provides an indication in the report that the registration is not complete, and that it's likely your code is missing some test coverage.

```go
Convey("Some stuff", func() {

    // This will show up as 'skipped' in the report
    Convey("Should go boink", nil)

}
```



### Skipping `So` assertions

Similar to `SkipConvey()`, changing a `So()` to `SkipSo()` prevents the execution of
that assertion. The report will show that the assertion was skipped.

```go
Convey("1 Should Equal 2", func() {
    
    // This assertion will not be executed and will show up as 'skipped' in the report
    SkipSo(1, ShouldEqual, 2)

})
```

And like `SkipConvey`, this function is only intended for use during
temporary code alterations.


### Running Only Certain `Convey` Registrations

You can use `FocusConvey` to only run certain `Convey` registrations. 

You must mark at least one leaf `Convey` registration (where the actual assertions are) and all of its parent `Convey`s in order for it to work. 

Let's see an example:

```go
FocusConvey("A", func() {
    // B will not be run
    Convey("B", nil)
    FocusConvey("C", func() {
        // Only D will be run. 
        FocusConvey("D", func() {
        })
    })
}
```

You might want to run all subtests of a certain `Convey` registration. In that case every leaf test must be marked with `Convey`, along with all of its parents. 

Here's an example of a common mistake:

```go
Convey("A", func() {
    // test B will still run because test D is not marked with Focus
    Convey("B", nil)
    FocusConvey("C", func() {
        // Mark test D with Focus to run only test D
        Convey("D", func() {
        })
    })
}
```

Read more in [the documentation on `FocusConvey`.](https://godoc.org/github.com/smartystreets/goconvey/convey#FocusConvey)

### End of tutorial

Congrats, you made it! Now `go test`!