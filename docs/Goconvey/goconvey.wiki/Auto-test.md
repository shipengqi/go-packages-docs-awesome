There are two ways to run tests automatically with GoConvey: in the terminal or in the browser.


### 1. In the terminal: auto-run.py

For viewing test reports in the terminal, use something like [auto-run.py](https://gist.github.com/mdwhatcott/9107649), 
which was at one time bundled with GoConvey. First download the script and 
put it somewhere on your path.

```sh
cd <folder_with_tests_or_packages>
auto-run.py -v
```

Run this from your project's top-level folder, and any changes to `.go` files under your current directory will trigger tests to run. The `-v` option enables "verbose" mode, and is entirely optional.



### 2. In the browser: `goconvey` server

When you use the [[web UI|Web-UI]] to watch test results, tests are already run automatically.



### Performance

One of the advantages with method #2 (in the browser) is that tests are run across multiple goroutines to bring results back as fast as possible.