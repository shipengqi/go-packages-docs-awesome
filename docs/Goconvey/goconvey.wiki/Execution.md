To run your tests, do what you do best in your terminal:

```sh
go test
```

**Example output:** (real output is colorized)

	.....

	5 assertions and counting

	....

	9 assertions and counting

	PASS
	ok  	github.com/smartystreets/goconvey/examples	0.022s


You can also use -v for verbose:

```sh
go test -v
```

**Example output:** (real output is colorized)

	=== RUN TestScoring

	  Subject: Bowling Game Scoring 
	    Given a fresh score card 
	      When all gutter balls are thrown 
	        The score should be zero ✔
	      When all throws knock down only one pin 
	        The score should be 20 ✔
	      When a spare is thrown 
	        The score should include a spare bonus. ✔
	      When a strike is thrown 
	        The score should include a strike bonus. ✔
	      When all strikes are thrown 
	        The score should be 300. ✔

	5 assertions and counting

	--- PASS: TestScoring (0.00 seconds)
	=== RUN TestSpec

	  Subject: Integer incrementation and decrementation 
	    Given a starting integer value 
	      When incremented 
	        The value should be greater by one ✔
	        The value should NOT be what it used to be ✔
	      When decremented 
	        The value should be lesser by one ✔
	        The value should NOT be what it used to be ✔

	9 assertions and counting

	--- PASS: TestSpec (0.00 seconds)
	PASS
	ok  	github.com/smartystreets/goconvey/examples	0.023s




### Auto-test and web UI

If you're tired of hitting <kbd>↑</kbd>, <kbd>Enter</kbd> all the time, try [[running tests automatically|Auto-test]].

If you're tired of the terminal altogether, check out the [[web UI|Web-UI]] which displays test output elegantly in the browser.


### [[Next|Skip]]

Finally, [[learn about Skip|Skip]] to skip/ignore scopes and assertions.
