When your Conveys have some set-up involved, you may need to tear down after or between tests. Use `Reset()` to clean up in those cases. A Convey's Reset() runs at the end of each `Convey()` within that same scope.

For example:

```go
Convey("Top-level", t, func() {

    // setup (run before each `Convey` at this scope):
    db.Open()
    db.Initialize()

    Convey("Test a query", t, func() {
        db.Query()
        // TODO: assertions here
    })

    Convey("Test inserts", t, func() {
        db.Insert()
        // TODO: assertions here
    })

    Reset(func() {
        // This reset is run after each `Convey` at the same scope.
        db.Close()
    })

})
```