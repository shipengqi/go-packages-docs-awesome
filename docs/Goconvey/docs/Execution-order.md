As an extension of the FAQ, this document answers some important questions
about the execution model of GoConvey, like:

1. How do I define a "Setup" method to be run before each test?
2. Why don't my nested tests run in sequential order?

These questions, surprisingly, are related. Here's an eloquent explanation 
from one of [GoConvey's users](https://github.com/smartystreets/goconvey/issues/111) 
that sheds light on these questions:

> Consider, for example, the pseudocode:

```
Convey A
    So 1
    Convey B
        So 2
    Convey C
        So 3
```

> I Initially thought would execute as `A1B2C3`, in other words, sequentially. 
> As you all know, this is actually executed first as `A1B2` and then `A1C3`. 
> Once I realized this, I actually realized the power of goconvey, because 
> it allows you two write n tests with log(n) statements. This "tree-based" 
> behavioral testing eliminates so much duplicated setup code and is so much 
> easier to read for completeness (versus pages of unit tests) while still 
> allowing for very well isolated tests (for each branch in the tree).

In the psuedo code above, `Convey A` serves as a "Setup" method for `Convey B`
and `Convey C` and is run separately for each. Here's a more complex example:
                                       
```
Convey A
    So 1
    Convey B
        So 2
        Convey Q
        	So 9
    Convey C
        So 3
```

Can you guess what the output would be?

`A1B2Q9A1C3` is the correct answer.

You're welcome to peruse the [tests in the GoConvey project itself](https://github.com/smartystreets/goconvey/blob/master/convey/isolated_execution_test.go) that document this behavior.

**Gotchas**

Remember that every Convey() call in Go creates a new scope. You should use Foo **=** &Bar{} in order to assign a new value to a previous declared variable. Using foo **:=** &Bar{} creates a new variable in the current scope. Example:

```Go
    Convey("Setup", func() {
        foo := &Bar{}
        Convey("This creates a new variable foo in this scope", func() {
            foo := &Bar{}
        }
        Convey("This assigns a new value to the previous declared foo", func() {
            foo = &Bar{}
        }
    }
```

If you're wondering about how to achieve "tear-down" functionality, see the page on the [[Reset|Reset]] function.