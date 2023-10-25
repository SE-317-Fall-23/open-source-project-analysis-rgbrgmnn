# Assignment Submission

## Project Selected: JSHint

## I. Introduction
- Below are two detailed descriptions of tests cases in the JSHint project that test the ability of JSHint to correctly lint embedded JavaScript within HTML files.
## II. Test Case 1: withIndentReportLocationMultipleFragments
### A. Description
- This test case is designed to verify that JSHint can correctly extract JavaScript code from an HTML document and report indentation errors within the JavaScript code, especially with multiple \<script\> fragments.
### B. Gherkin Syntax (if applicable)
- **Feature**: Indentation error reporting in embedded JavaScript within HTML
- As a developer using JSHint:
- I want to ensure that misindented JavaScript code within multiple HTML \<script\> tags are correctly identified
- So taht I can maintain consistent and clean code in my HTML document.
- **Scenario**: Detect and report misindented JavaScript lines across multiple \<script\> tags in HTML
- Given I have an HTML document with multiple \<script\> tags containing intentionally misindented JavaScript
- Then JSHint should report exactly 2 errors
- And the first error should be on line 5
- And the second error should be on line 10
- And JSHint should exit with a code indicating linting errors were found
- If you choose to use Gherkin syntax, write the Gherkin scenario for this test case.
### C. Test Steps
1. Initialize Environment:
    - Load the custom reporter from '../example/reporter.js'
    - Create an empty 'errors' array to accumulate linting errors.
    - Stub the reporter's 'reporter' function to append reported erros to the 'errors' array.
2. Setup Mock Environment:
    - Define the path of the mock directory
    - Stub the 'process.cwd()' to return the mock directory path
    - Define the mock HTML content contain two \<script\> tags with misindented JavaScript code
    - Stub the 'fsUtils.readFile' function to return the mock HTML content when queried for the file 'indent.html'
    - Stub the 'fsUtils.exists' function to return 'true' indicating the existence of the file 'indent.html'
3. Run JSHint:
    - Call 'cli.interpret' function to simulate running JSHint on the 'indent.html' file with the following comand-line options:
      - '--extract always'
      - '--reporter=reporter.js'
4. Assertions:
    - Verify that JSHint exist with error code '2'
    - Assert that exactly 2 linting errors were reported
    - Verify that errors are reported in the right location
### D. Code Segments Under Test
- Identify specific code segments or functions that are being tested in this case.

```JavaScript
interpret: function(args) {
    cli.setArgv(args);
    cli.options = {};

    cli.enable("version", "glob", "help");
    cli.setApp(path.resolve(__dirname + "/../package.json"));

    var options = cli.parse(OPTIONS);
    // Use config file if specified
    var config;
    if (options.config) {
      config = exports.loadConfig(options.config);
    }

    switch (true) {
    // JSLint reporter
    case options.reporter === "jslint":
    case options["jslint-reporter"]:
      options.reporter = "./reporters/jslint_xml.js";
      break;

    // CheckStyle (XML) reporter
    case options.reporter === "checkstyle":
    case options["checkstyle-reporter"]:
      options.reporter = "./reporters/checkstyle.js";
      break;

    // Unix reporter
    case options.reporter === "unix":
      options.reporter = "./reporters/unix.js";
      break;

    // Reporter that displays additional JSHint data
    case options["show-non-errors"]:
      options.reporter = "./reporters/non_error.js";
      break;

    // Custom reporter
    case options.reporter !== undefined:
      options.reporter = path.resolve(process.cwd(), options.reporter);
    }

    var reporter;
    if (options.reporter) {
      reporter = loadReporter(options.reporter);

      if (reporter === null) {
        cli.error("Can't load reporter file: " + options.reporter);
        exports.exit(1);
      }
    }

    // This is a hack. exports.run is both sync and async function
    // because I needed stdin support (and cli.withStdin is async)
    // and was too lazy to change tests.

    function done(passed) {
      /*jshint eqnull:true */

      if (passed == null)
        return;

      exports.exit(passed ? 0 : 2);
    }

    done(exports.run({
      args:       cli.args,
      config:     config,
      reporter:   reporter,
      ignores:    loadIgnores({ exclude: options.exclude, excludePath: options["exclude-path"] }),
      extensions: options["extra-ext"],
      verbose:    options.verbose,
      extract:    options.extract,
      filename:   options.filename,
      prereq:     options.prereq,
      useStdin:   { "-": true, "/dev/stdin": true }[args[args.length - 1]]
    }, done));
  }
  ```
### E. Initial State
- Describe the initial state or conditions of the system or component before executing the test.
### F. Transition
- Explain the actions or events that trigger a change in the system's state.
### G. Expected State
- Clearly outline the expected state or outcome after the test steps have been completed.

## III. Test Case 2: usingMultipleFiles
### A. Description
- This test is designed to verify the behavior of JSHint when processing multiple HTML files containing embedded JavaScript with errors.
### B. Gherkin Syntax (if applicable)
- If you choose to use Gherkin syntax, write the Gherkin scenario for this test case.
- **Feature**: Linting multiple HTML files with embedded JavaScript
- **Scenario**: Detecting errors in multiple HTML files
  - *Given* I have two HTML files "indent.html" and "another.html"
    - And "indent.html" contains the JavaScript code "a()"
    - And "another.html" contains the JavaScript code "a && a()"
  - *When* I lint the files with the options "--extract auto" and "--reporter=reporter.js"
  - *Then* I should get an exit status of "2"
    - And there should be "2" linting errors detected
    - And the first error from "indent.html" should indicate a "missing semicolon" at "line 2, column 6"
    - And the second error from "another.html" should indicate an "expression statement" error at "line 3, column 8"
### C. Test Steps
1. Environment Initialization:
    - 'reporter.js' is loaded
    - 'errors' array is initialized for collecting linting errors
    - 'reporter' function is stubbed to append reported linting errors to the 'errors' array
2. Mock Environment Setup:
    - The path to the mock directory ("../examples/") is defined
    - The current working directory is stubbed to return the mock directory path
    - Two mock HTML contents are defined:
      - 'indent.html': contains 'a()' with a semicolon
      - 'another.html': contains 'a && a()'
    - 'fsUtils.readFile' is stubbed to return mock content
    - 'fsUtils.exists' confirms the existence of 'indent.html' and 'another.html'
3. Run JSHint:
    - 'cli.interpret' is called to simulate JSHint's linting of 'indent.html' and 'another.html' with options '--extract auto' and '--reporter=reporter.js'
4. Assertions:
   - JSHint should exit with status code '2'
   - There should be two total linting errors
     - In 'indent.html':
       - checks presence of error object
       - checks for return code 'W033'
       - checks that correct line and position of error is returned
     - In 'another.html':
       - checks presence of error object
       - checks for return code 'W030'
       - checks that correct line and position of error is returned
### D. Code Segments Under Test
- Identify specific code segments or functions that are being tested in this case.
```JavaScript
/**
   * Main entrance function. Parses arguments and calls 'run' when
   * its done. This function is called from bin/jshint file.
   *
   * @param {object} args, arguments in the process.argv format.
   */
  interpret: function(args) {
    cli.setArgv(args);
    cli.options = {};

    cli.enable("version", "glob", "help");
    cli.setApp(path.resolve(__dirname + "/../package.json"));

    var options = cli.parse(OPTIONS);
    // Use config file if specified
    var config;
    if (options.config) {
      config = exports.loadConfig(options.config);
    }

    switch (true) {
    // JSLint reporter
    case options.reporter === "jslint":
    case options["jslint-reporter"]:
      options.reporter = "./reporters/jslint_xml.js";
      break;

    // CheckStyle (XML) reporter
    case options.reporter === "checkstyle":
    case options["checkstyle-reporter"]:
      options.reporter = "./reporters/checkstyle.js";
      break;

    // Unix reporter
    case options.reporter === "unix":
      options.reporter = "./reporters/unix.js";
      break;

    // Reporter that displays additional JSHint data
    case options["show-non-errors"]:
      options.reporter = "./reporters/non_error.js";
      break;

    // Custom reporter
    case options.reporter !== undefined:
      options.reporter = path.resolve(process.cwd(), options.reporter);
    }

    var reporter;
    if (options.reporter) {
      reporter = loadReporter(options.reporter);

      if (reporter === null) {
        cli.error("Can't load reporter file: " + options.reporter);
        exports.exit(1);
      }
    }

    // This is a hack. exports.run is both sync and async function
    // because I needed stdin support (and cli.withStdin is async)
    // and was too lazy to change tests.

    function done(passed) {
      /*jshint eqnull:true */

      if (passed == null)
        return;

      exports.exit(passed ? 0 : 2);
    }

    done(exports.run({
      args:       cli.args,
      config:     config,
      reporter:   reporter,
      ignores:    loadIgnores({ exclude: options.exclude, excludePath: options["exclude-path"] }),
      extensions: options["extra-ext"],
      verbose:    options.verbose,
      extract:    options.extract,
      filename:   options.filename,
      prereq:     options.prereq,
      useStdin:   { "-": true, "/dev/stdin": true }[args[args.length - 1]]
    }, done));
  }
```
### E. Initial State
- Describe the initial state or conditions of the system or component before executing the test.
### F. Transition
- Explain the actions or events that trigger a change in the system's state.
### G. Expected State
- Clearly outline the expected state or outcome after the test steps have been completed

