#### What is GoConvey?

Basically, GoConvey is an extension of the built-in Go test tool. It facilitates [Behavior-driven Development (BDD)](https://en.wikipedia.org/wiki/Behavior-driven_development) in Go, though this is not the only way to use it. Many people continue to write traditional Go tests but prefer [[GoConvey's web UI|Web-UI]] for reporting test results.

There are two main parts to GoConvey:

1. A comprehensive BDD framework
2. A server & web UI

Both parts are optional and can be used independently according to your workflow.


#### Can I use GoConvey with `go test`?

Yes, that's the point!


#### Why do my nested `Convey` blocks execute in a strange order?

Please read the [isolated execution tests](https://github.com/smartystreets/goconvey/blob/master/convey/isolated_execution_test.go). They are the best form of documentation for the execution model of GoConvey, which is very powerful but not apparent at first.


#### What is [[the web UI|Web-UI]]?

It's test results in your browser. It updates automatically as files in the watched directories are saved or changed. See the [[web UI|Web-UI]] wiki page for more information.



#### How can I get tests to run automatically?

If you're using the [[web UI|Web-UI]] with `goconvey` (the server) to watch tests in your browser, then tests already run automatically when `.go` files are changed.

For running tests in your terminal, check out how to use [[the auto-run.py auto-test script|Auto-test]]. (It's really easy!)



#### Does the [[web UI|Web-UI]] work with traditional Go tests?

Yep! If you haven't ported all your tests over to GoConvey, your traditional Go test cases will still be run and the results will be reported in the browser.


#### How can I get debug output to appear next to my assertions, rather than up at the function level?

Use `convey.Print` or `convey.Printf` or `convey.Println` just as you would from the `fmt` package. This will cause the output to show up by your assertions rather than up at a higher level.


#### How can I force tests to continue executing even after a failure?

By default, a test failure or panic causes future tests in that scope to halt. To have tests continue running, you can pass in a FailureMode in a `Convey()` call:

```go
Convey("A", t, FailureContinues, func() {
    // ...
})
```

All nested Conveys will inherit that setting. You can also set the default FailureMode in an `init()` function with `SetDefaultFailureMode()`, like so:

```go
func init() {
    SetDefaultFailureMode(FailureContinues)
}
```


#### Why can't I open a coverage report (404 Not Found) of a tested file?

You have to make sure that the package you are testing lives inside your $GOPATH.


#### Is GoConvey supported?

Not in the commercial sense of the word, no. Even though it is "sponsored" by SmartyStreets (in that a couple of devs were given company time to work on it), it comes as-is, so "use-at-your-own-risk" -- though honestly you'll probably quite enjoy it. :)

But yes, in the open-source sense of the word, it is supported, meaning: it's not a defunct project. It's very much alive and well. Feel free to submit a pull request with contributions!


#### Where did GoConvey start?

When SmartyStreets decided to scrap its .NET code (which was pretty much everything) in favor of Go, all the developers started learning and practicing with Go. While Go's built-in `go test` command and standard `testing` package excited us, in practice it left us wanting.

After our first project re-write in Go, using the standard library tests, we found that our tests didn't clearly document what our code was doing, and how it should behave. Assertions weren't clear because they were backward.

On top of that (which is solved by other, more lightweight libraries), we thought a browser tab with test results displayed visually would be really cool and it sounded fun. So originally, GoConvey was written for internal use at SmartyStreets. Then we decided to make it kind of our gift to the Golang community.


#### Will you add this or that feature?

Maybe, but GoConvey works well enough for us at SmartyStreets as-is. You're welcome to do it.


#### How do I contribute?

See our [[contributors|For-Contributors]] page.


#### If I'm new to testing, what do you recommend reading?

- [Unit Testing](http://en.wikipedia.org/wiki/Unit_testing)
- [Test-Driven Development (TDD)](http://en.wikipedia.org/wiki/Test-driven_development)
- [Behavior-Driven Development (BDD)](http://en.wikipedia.org/wiki/Behavior-driven_development)
- [BDD vs. TDD](http://stackoverflow.com/questions/2509/what-are-the-primary-differences-between-tdd-and-bdd)
- [Laws of TDD](http://butunclebob.com/ArticleS.UncleBob.TheThreeRulesOfTdd)
- [Integration Testing](http://en.wikipedia.org/wiki/Integration_testing)



#### Why did we create this? (Isn't `go test` good enough?)

We weren't satisifed with the built-in GoLang test tools. No, actually we were overjoyed that the language
came with something built-in. And we liked `go test` enough that rather than create something from the ground up 
we decided to integrate with `go test` directly. We were just used to something much more descriptive and 
that facilitated testing large systems withing a lot of boiler plate code.


#### Why is it called GoConvey?

We've used a few different BDD tools before, each having its own take on the language you should use
to specify the behavior of the system under test. "Given, When, Then" vs. "Establish, Because, It" from BDD
and "Arrange, Act, Assert" from TDD are a few examples. In the end, you use the testing tool to specify or
"convey" what the system should do. So, the main function you'll use to write specifications is named `Convey`.
The language you use to specify your system is up to you, although we usually use the "Given, When, Then" style.

#### Should I be careful to not produce certain output in my code?

Well, that's an obscure question if I've ever heard one, but I'm glad you asked. Don't ever output either of these patterns:

1. `>->->OPEN-JSON->->->`
2. `<-<-<-CLOSE-JSON<-<-<`

Those patterns are used to delimit blocks of JSON so that the web server can parse test output correctly.

