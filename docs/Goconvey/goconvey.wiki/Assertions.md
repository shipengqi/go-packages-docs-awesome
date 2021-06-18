GoConvey comes with a lot of standard assertions you can use with `So()`.

### General Equality

	So(thing1, ShouldEqual, thing2)
	So(thing1, ShouldNotEqual, thing2)
	So(thing1, ShouldResemble, thing2)		// a deep equals for arrays, slices, maps, and structs
	So(thing1, ShouldNotResemble, thing2)
	So(thing1, ShouldPointTo, thing2)
	So(thing1, ShouldNotPointTo, thing2)
	So(thing1, ShouldBeNil)
	So(thing1, ShouldNotBeNil)
	So(thing1, ShouldBeTrue)
	So(thing1, ShouldBeFalse)
	So(thing1, ShouldBeZeroValue)

### Numeric Quantity comparison

	So(1, ShouldBeGreaterThan, 0)
	So(1, ShouldBeGreaterThanOrEqualTo, 0)
	So(1, ShouldBeLessThan, 2)
	So(1, ShouldBeLessThanOrEqualTo, 2)
	So(1.1, ShouldBeBetween, .8, 1.2)
	So(1.1, ShouldNotBeBetween, 2, 3)
	So(1.1, ShouldBeBetweenOrEqual, .9, 1.1)
	So(1.1, ShouldNotBeBetweenOrEqual, 1000, 2000)
	So(1.0, ShouldAlmostEqual, 0.99999999, .0001)   // tolerance is optional; default 0.0000000001
	So(1.0, ShouldNotAlmostEqual, 0.9, .0001)

### Collections

	So([]int{2, 4, 6}, ShouldContain, 4)
	So([]int{2, 4, 6}, ShouldNotContain, 5)
	So(4, ShouldBeIn, ...[]int{2, 4, 6})
	So(4, ShouldNotBeIn, ...[]int{1, 3, 5})
	So([]int{}, ShouldBeEmpty)
	So([]int{1}, ShouldNotBeEmpty)
	So(map[string]string{"a": "b"}, ShouldContainKey, "a")
	So(map[string]string{"a": "b"}, ShouldNotContainKey, "b")
	So(map[string]string{"a": "b"}, ShouldNotBeEmpty)
	So(map[string]string{}, ShouldBeEmpty)
	So(map[string]string{"a": "b"}, ShouldHaveLength, 1) // supports map, slice, chan, and string

### Strings

	So("asdf", ShouldStartWith, "as")
	So("asdf", ShouldNotStartWith, "df")
	So("asdf", ShouldEndWith, "df")
	So("asdf", ShouldNotEndWith, "df")
	So("asdf", ShouldContainSubstring, "sd")		// optional 'expected occurences' arguments?
	So("asdf", ShouldNotContainSubstring, "er")
	So("adsf", ShouldBeBlank)
	So("asdf", ShouldNotBeBlank)

### panic
        
	So(func(), ShouldPanic)
	So(func(), ShouldNotPanic)
	So(func(), ShouldPanicWith, "")		// or errors.New("something")
	So(func(), ShouldNotPanicWith, "")	// or errors.New("something")

### Type checking

	So(1, ShouldHaveSameTypeAs, 0)
	So(1, ShouldNotHaveSameTypeAs, "asdf")

### time.Time (and time.Duration)

	So(time.Now(), ShouldHappenBefore, time.Now())
	So(time.Now(), ShouldHappenOnOrBefore, time.Now())
	So(time.Now(), ShouldHappenAfter, time.Now())
	So(time.Now(), ShouldHappenOnOrAfter, time.Now())
	So(time.Now(), ShouldHappenBetween, time.Now(), time.Now())
	So(time.Now(), ShouldHappenOnOrBetween, time.Now(), time.Now())
	So(time.Now(), ShouldNotHappenOnOrBetween, time.Now(), time.Now())
	So(time.Now(), ShouldHappenWithin, duration, time.Now())
	So(time.Now(), ShouldNotHappenWithin, duration, time.Now())

Thanks to [github.com/jacobsa](https://github.com/jacobsa) for his excellent 
[oglematchers](https://github.com/jacobsa/oglematchers) library, which
is what many of these methods make use of to do their jobs.


### [[Next|Custom-Assertions]]

Next up, learn about [[building your own assertions|Custom-Assertions]].