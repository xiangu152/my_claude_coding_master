---
title: "Command-line Flags"
source: "https://docs.pytest.org/en/stable/reference/reference.html"
version: "stable"
---

# Command-line Flags

> 原始文档来源：https://docs.pytest.org/en/stable/reference/reference.html (API Reference 节选)

---

This section documents all command-line options provided by pytest’s core plugins.

Note

External plugins can add their own command-line options. This reference documents only the options from pytest’s core plugins. To see all available options including those from installed plugins, run pytest --help.

Test Selection
-k EXPRESSION

Only run tests which match the given substring expression. An expression is a Python evaluable expression where all names are substring-matched against test names and their parent classes.

Examples:

pytest -k "test_method or test_other"  # matches names containing 'test_method' OR 'test_other'
pytest -k "not test_method"            # matches names NOT containing 'test_method'
pytest -k "not test_method and not test_other"  # excludes both

The matching is case-insensitive. Keywords are also matched to classes and functions containing extra names in their extra_keyword_matches set.

See Specifying which tests to run for more information and examples.

-m MARKEXPR

Only run tests matching given mark expression. Supports and, or, and not operators.

Examples:

pytest -m slow                  # run tests marked with @pytest.mark.slow
pytest -m "not slow"            # run tests NOT marked slow
pytest -m "mark1 and not mark2" # run tests marked mark1 but not mark2

See How to mark test functions with attributes for more information on markers.

--markers

Show all available markers (builtin, plugin, and per-project markers defined in configuration).

Test Execution Control
-x, --exitfirst

Exit instantly on first error or failed test.

--maxfail=NUM

Exit after first num failures or errors. Useful for CI environments where you want to fail fast but see a few failures.

--last-failed, --lf

Rerun only the tests that failed at the last run. If no tests failed (or no cached data exists), all tests are run. See also cache_dir and How to re-run failed tests and maintain state between test runs.

--failed-first, --ff

Run all tests, but run the last failures first. This may re-order tests and thus lead to repeated fixture setup/teardown.

--new-first, --nf

Run tests from new files first, then the rest of the tests sorted by file modification time.

--stepwise, --sw

Exit on test failure and continue from last failing test next time. Useful for fixing multiple test failures one at a time.

See Stepwise for more information.

--stepwise-skip, --sw-skip

Ignore the first failing test but stop on the next failing test. Implicitly enables --stepwise.

--stepwise-reset, --sw-reset

Resets stepwise state, restarting the stepwise workflow. Implicitly enables --stepwise.

--last-failed-no-failures, --lfnf

With --last-failed, determines whether to execute tests when there are no previously known failures or when no cached lastfailed data was found.

all (default): runs the full test suite again

none: just emits a message about no known failures and exits successfully

--runxfail

Report the results of xfail tests as if they were not marked. Useful for debugging xfailed tests. See XFail: mark test functions as expected to fail.

Collection
--collect-only, --co

Only collect tests, don’t execute them. Shows which tests would be collected and run.

--pyargs

Try to interpret all arguments as Python packages. Useful for running tests of installed packages:

pytest --pyargs pkg.testing

--ignore=PATH

Ignore path during collection (multi-allowed). Can be specified multiple times.

--ignore-glob=PATTERN

Ignore path pattern during collection (multi-allowed). Supports glob patterns.

--deselect=NODEID_PREFIX

Deselect item (via node id prefix) during collection (multi-allowed).

--confcutdir=DIR

Only load conftest.py files relative to specified directory.

--noconftest

Don’t load any conftest.py files.

--keep-duplicates

Keep duplicate tests. By default, pytest removes duplicate test items.

--collect-in-virtualenv

Don’t ignore tests in a local virtualenv directory. By default, pytest skips tests in virtualenv directories.

--continue-on-collection-errors

Force test execution even if collection errors occur.

--import-mode

Prepend/append to sys.path when importing test modules and conftest files.

prepend (default): prepend to sys.path

append: append to sys.path

importlib: use importlib to import test modules

See pytest import mechanisms and sys.path/PYTHONPATH for more information.

Fixtures
--fixtures, --funcargs

Show available fixtures, sorted by plugin appearance. Fixtures with leading _ are only shown with --verbose.

--fixtures-per-test

Show fixtures per test.

--setup-only

Only setup fixtures, do not execute tests. See How to use fixtures.

--setup-show

Show setup of fixtures while executing tests.

--setup-plan

Show what fixtures and tests would be executed but don’t execute anything.

Debugging
--pdb

Start the interactive Python debugger on errors or KeyboardInterrupt. See Using python:library/pdb with pytest.

--pdbcls=MODULENAME:CLASSNAME

Specify a custom interactive Python debugger for use with --pdb.

Example:

pytest --pdbcls=IPython.terminal.debugger:TerminalPdb

--trace

Immediately break when running each test.

See Dropping to pdb at the start of a test for more information.

--full-trace

Don’t cut any tracebacks (default is to cut).

See Modifying Python traceback printing for more information.

--debug, --debug=DEBUG_FILE_NAME

Store internal tracing debug information in this log file. This file is opened with 'w' and truncated as a result, care advised. Default file name if not specified: pytestdebug.log.

--trace-config

Trace considerations of conftest.py files.

Output and Reporting
-v, --verbose

Increase verbosity. Can be specified multiple times (e.g., -vv) for even more verbose output.

See Fine-grained verbosity for fine-grained control over verbosity.

-q, --quiet

Decrease verbosity.

--verbosity=NUM

Set verbosity level explicitly. Default: 0.

-r CHARS, --report-chars=CHARS

Show extra test summary info as specified by chars:

f: failed

E: error

s: skipped

x: xfailed

X: xpassed

p: passed

P: passed with output

a: all except passed (p/P)

A: all

w: warnings (enabled by default)

N: resets the list

Default: 'fE'

Examples:

pytest -rA           # show all outcomes
pytest -rfE          # show only failed and errors (default)
pytest -rfs          # show failed and skipped

See Producing a detailed summary report for more information.

--no-header

Disable header.

--no-summary

Disable summary.

--no-fold-skipped

Do not fold skipped tests in short summary.

--force-short-summary

Force condensed summary output regardless of verbosity level.

-l, --showlocals

Show locals in tracebacks (disabled by default).

--no-showlocals

Hide locals in tracebacks (negate --showlocals passed through addopts).

--tb=STYLE

Traceback print mode:

auto: intelligent traceback formatting (default)

long: exhaustive, informative traceback formatting

short: shorter traceback format

line: only the failing line

native: Python’s standard traceback

no: no traceback

See Modifying Python traceback printing for examples.

--xfail-tb

Show tracebacks for xfail (as long as --tb != no).

--show-capture

Controls how captured stdout/stderr/log is shown on failed tests.

no: don’t show captured output

stdout: show captured stdout

stderr: show captured stderr

log: show captured logging

all (default): show all captured output

--color=WHEN

Color terminal output:

yes: always use color

no: never use color

auto (default): use color if terminal supports it

--code-highlight={yes,no}

Whether code should be highlighted (only if --color is also enabled). Default: yes.

--pastebin=MODE

Send failed|all info to bpaste.net pastebin service.

--durations=NUM

Show N slowest setup/test durations (N=0 for all). See Profiling test execution duration.

--durations-min=NUM

Minimal duration in seconds for inclusion in slowest list. Default: 0.005 (or 0.0 if -vv is given).

Output Capture
--capture=METHOD

Per-test capturing method:

fd: capture at file descriptor level (default)

sys: capture at sys level

no: don’t capture output

tee-sys: capture but also show output on terminal

See How to capture stdout/stderr output.

-s

Shortcut for --capture=no.

JUnit XML
--junit-xml=PATH, --junitxml=PATH

Create junit-xml style report file at given path.

--junit-prefix=STR, --junitprefix=STR

Prepend prefix to classnames in junit-xml output.

Cache
--cache-show[=PATTERN]

Show cache contents, don’t perform collection or tests. Default glob pattern: '*'.

--cache-clear

Remove all cache contents at start of test run. See How to re-run failed tests and maintain state between test runs.

Warnings
--disable-pytest-warnings, --disable-warnings

Disable warnings summary.

-W WARNING, --pythonwarnings=WARNING

Set which warnings to report, see -W option of Python itself. Can be specified multiple times.

--max-warnings=NUM

Exit with pytest.ExitCode MAX_WARNINGS_ERROR (code 6) if all the tests pass, but the number of warnings exceeds the given threshold. By default there is no limit. Can also be set via the max_warnings configuration option.

Doctest
--doctest-modules

Run doctests in all .py modules.

See How to run doctests for more information on using doctests with pytest.

--doctest-report

Choose another output format for diffs on doctest failure:

none

cdiff

ndiff

udiff

only_first_failure

--doctest-glob=PATTERN

Doctests file matching pattern. Default: test*.txt.

--doctest-ignore-import-errors

Ignore doctest collection errors.

--doctest-continue-on-failure

For a given doctest, continue to run after the first failure.

Configuration
-c FILE, --config-file=FILE

Load configuration from FILE instead of trying to locate one of the implicit configuration files.

--rootdir=ROOTDIR

Define root directory for tests. Can be relative path: 'root_dir', './root_dir', 'root_dir/another_dir/'; absolute path: '/home/user/root_dir'; path with variables: '$HOME/root_dir'.

--basetemp=DIR

Base temporary directory for this test run. Warning: this directory is removed if it exists.

See Temporary directory location and retention for more information.

-o OPTION=VALUE, --override-ini=OPTION=VALUE

Override configuration option with option=value style. Can be specified multiple times.

Example:

pytest -o strict_xfail=true -o cache_dir=cache

--strict-config

Enables the strict_config option.

--strict-markers

Enables the strict_markers option.

--strict

Enables the strict option (which enables all strictness options).

--assert=MODE

Control assertion debugging tools:

plain: performs no assertion debugging

rewrite (default): rewrites assert statements in test modules on import to provide assert expression information

Logging

See How to manage logging for a guide on using these flags.

--log-level=LEVEL

Level of messages to catch/display. Not set by default, so it depends on the root/parent log handler’s effective level, where it is WARNING by default.

--log-format=FORMAT

Log format used by the logging module.

--log-date-format=FORMAT

Log date format used by the logging module.

--log-cli-level=LEVEL

CLI logging level. See Live Logs.

--log-cli-format=FORMAT

Log format used by the logging module for CLI output.

--log-cli-date-format=FORMAT

Log date format used by the logging module for CLI output.

--log-file=PATH

Path to a file logging will be written to.

--log-file-mode

Log file open mode:

w (default): recreate the file

a: append to the file

--log-file-level=LEVEL

Log file logging level.

--log-file-format=FORMAT

Log format used by the logging module for the log file.

--log-file-date-format=FORMAT

Log date format used by the logging module for the log file.

--log-auto-indent=VALUE

Auto-indent multiline messages passed to the logging module. Accepts true|on, false|off or an integer.

--log-disable=LOGGER

Disable a logger by name. Can be passed multiple times.

Plugin and Extension Management
-p NAME

Early-load given plugin module name or entry point (multi-allowed). To avoid loading of plugins, use the no: prefix, e.g. no:doctest. See also --disable-plugin-autoload.

--disable-plugin-autoload

Disable plugin auto-loading through entry point packaging metadata. Only plugins explicitly specified in -p or env var PYTEST_PLUGINS will be loaded.

Version and Help
-V, --version

Display pytest version and information about plugins. When given twice, also display information about plugins.

-h, --help

Show help message and configuration info.

Complete Help Output

All the command-line flags can also be obtained by running pytest --help:

```bash
$ pytest --help
usage: pytest [options] [file_or_dir] [file_or_dir] [...]
```

positional arguments:
  file_or_dir

general:
  -k EXPRESSION         Only run tests which match the given substring
                        expression. An expression is a Python evaluable
                        expression where all names are substring-matched
                        against test names and their parent classes.
                        Example: -k 'test_method or test_other' matches all
                        test functions and classes whose name contains
                        'test_method' or 'test_other', while -k 'not
                        test_method' matches those that don't contain
                        'test_method' in their names. -k 'not test_method
                        and not test_other' will eliminate the matches.
                        Additionally keywords are matched to classes and
                        functions containing extra names in their
                        'extra_keyword_matches' set, as well as functions
                        which have names assigned directly to them. The
                        matching is case-insensitive.
  -m MARKEXPR           Only run tests matching given mark expression. For
                        example: -m 'mark1 and not mark2'.
  --markers             show markers (builtin, plugin and per-project ones).
  -x, --exitfirst       Exit instantly on first error or failed test
  --maxfail=num         Exit after first num failures or errors
  --strict-config       Enables the strict_config option
  --strict-markers      Enables the strict_markers option
  --strict              Enables the strict option
  --fixtures, --funcargs
```python
                        Show available fixtures, sorted by plugin appearance
                        (fixtures with leading '_' are only shown with '-v')
```
  --fixtures-per-test   Show fixtures per test
  --pdb                 Start the interactive Python debugger on errors or
                        KeyboardInterrupt
  --pdbcls=modulename:classname
                        Specify a custom interactive Python debugger for use
                        with --pdb.For example:
                        --pdbcls=IPython.terminal.debugger:TerminalPdb
  --trace               Immediately break when running each test
  --capture=method      Per-test capturing method: one of fd|sys|no|tee-sys
  -s                    Shortcut for --capture=no
  --runxfail            Report the results of xfail tests as if they were
                        not marked
  --lf, --last-failed   Rerun only the tests that failed at the last run (or
                        all if none failed)
  --ff, --failed-first  Run all tests, but run the last failures first. This
```python
                        may re-order tests and thus lead to repeated fixture
                        setup/teardown.
```
  --nf, --new-first     Run tests from new files first, then the rest of the
                        tests sorted by file mtime
  --cache-show=[CACHESHOW]
                        Show cache contents, don't perform collection or
                        tests. Optional argument: glob (default: '*').
  --cache-clear         Remove all cache contents at start of test run
  --lfnf, --last-failed-no-failures={all,none}
                        With ``--lf``, determines whether to execute tests
                        when there are no previously (known) failures or
                        when no cached ``lastfailed`` data was found.
                        ``all`` (the default) runs the full test suite
                        again. ``none`` just emits a message about no known
                        failures and exits successfully.
  --sw, --stepwise      Exit on test failure and continue from last failing
                        test next time
  --sw-skip, --stepwise-skip
                        Ignore the first failing test but stop on the next
                        failing test. Implicitly enables --stepwise.
  --sw-reset, --stepwise-reset
                        Resets stepwise state, restarting the stepwise
                        workflow. Implicitly enables --stepwise.
Reporting:
  --durations=N         Show N slowest setup/test durations (N=0 for all)
  --durations-min=N     Minimal duration in seconds for inclusion in slowest
                        list. Default: 0.005 (or 0.0 if -vv is given).
  -v, --verbose         Increase verbosity
  --no-header           Disable header
  --no-summary          Disable summary
  --no-fold-skipped     Do not fold skipped tests in short summary.
  --force-short-summary
                        Force condensed summary output regardless of
                        verbosity level.
  -q, --quiet           Decrease verbosity
  --verbosity=VERBOSE   Set verbosity. Default: 0.
  -r, --report-chars chars
                        Show extra test summary info as specified by chars:
                        (f)ailed, (E)rror, (s)kipped, (x)failed, (X)passed,
                        (p)assed, (P)assed with output, (a)ll except passed
                        (p/P), or (A)ll. (w)arnings are enabled by default
                        (see --disable-warnings), 'N' can be used to reset
                        the list. (default: 'fE').
  --disable-warnings, --disable-pytest-warnings
                        Disable warnings summary
  -l, --showlocals      Show locals in tracebacks (disabled by default)
  --no-showlocals       Hide locals in tracebacks (negate --showlocals
                        passed through addopts)
  --tb=style            Traceback print mode
                        (auto/long/short/line/native/no)
  --xfail-tb            Show tracebacks for xfail (as long as --tb != no)
  --show-capture={no,stdout,stderr,log,all}
                        Controls how captured stdout/stderr/log is shown on
                        failed tests. Default: all.
  --full-trace          Don't cut any tracebacks (default is to cut)
  --color=color         Color terminal output (yes/no/auto)
  --code-highlight={yes,no}
                        Whether code should be highlighted (only if --color
                        is also enabled). Default: yes.
  --pastebin=mode       Send failed|all info to bpaste.net pastebin service
  --junitxml, --junit-xml=path
                        Create junit-xml style report file at given path
  --junitprefix, --junit-prefix=str
                        Prepend prefix to classnames in junit-xml output
pytest-warnings:
  -W, --pythonwarnings PYTHONWARNINGS
                        Set which warnings to report, see -W option of
                        Python itself
  --max-warnings=num    Exit with error if all tests pass but the number of
                        warnings exceeds this threshold
collection:
  --collect-only, --co  Only collect tests, don't execute them
  --pyargs              Try to interpret all arguments as Python packages
  --ignore=path         Ignore path during collection (multi-allowed)
  --ignore-glob=path    Ignore path pattern during collection (multi-
                        allowed)
  --deselect=nodeid_prefix
                        Deselect item (via node id prefix) during collection
                        (multi-allowed)
  --confcutdir=dir      Only load conftest.py's relative to specified dir
  --noconftest          Don't load any conftest.py files
  --keep-duplicates     Keep duplicate tests
  --collect-in-virtualenv
                        Don't ignore tests in a local virtualenv directory
  --continue-on-collection-errors
                        Force test execution even if collection errors occur
  --import-mode={prepend,append,importlib}
                        Prepend/append to sys.path when importing test
                        modules and conftest files. Default: prepend.
  --doctest-modules     Run doctests in all .py modules
  --doctest-report={none,cdiff,ndiff,udiff,only_first_failure}
                        Choose another output format for diffs on doctest
                        failure
  --doctest-glob=pat    Doctests file matching pattern, default: test*.txt
  --doctest-ignore-import-errors
                        Ignore doctest collection errors
  --doctest-continue-on-failure
                        For a given doctest, continue to run after the first
                        failure
test session debugging and configuration:
  -c, --config-file FILE
                        Load configuration from `FILE` instead of trying to
                        locate one of the implicit configuration files.
  --rootdir=ROOTDIR     Define root directory for tests. Can be relative
                        path: 'root_dir', './root_dir',
                        'root_dir/another_dir/'; absolute path:
                        '/home/user/root_dir'; path with variables:
                        '$HOME/root_dir'.
  --basetemp=dir        Base temporary directory for this test run.
                        (Warning: this directory is removed if it exists.)
  -V, --version         Display pytest version and information about
                        plugins. When given twice, also display information
                        about plugins.
  -h, --help            Show help message and configuration info
  -p name               Early-load given plugin module name or entry point
                        (multi-allowed). To avoid loading of plugins, use
                        the `no:` prefix, e.g. `no:doctest`. See also
                        --disable-plugin-autoload.
  --disable-plugin-autoload
                        Disable plugin auto-loading through entry point
                        packaging metadata. Only plugins explicitly
                        specified in -p or env var PYTEST_PLUGINS will be
                        loaded.
  --trace-config        Trace considerations of conftest.py files
  --debug=[DEBUG_FILE_NAME]
```python
                        Store internal tracing debug information in this log
                        file. This file is opened with 'w' and truncated as
                        a result, care advised. Default: pytestdebug.log.
```
  -o, --override-ini OVERRIDE_INI
                        Override configuration option with "option=value"
                        style, e.g. `-o strict_xfail=True -o
                        cache_dir=cache`.
  --assert=MODE         Control assertion debugging tools.
```python
                        'plain' performs no assertion debugging.
                        'rewrite' (the default) rewrites assert statements
                        in test modules on import to provide assert
                        expression information.
```
  --setup-only          Only setup fixtures, do not execute tests
  --setup-show          Show setup of fixtures while executing tests
  --setup-plan          Show what fixtures and tests would be executed but
                        don't execute anything
logging:
  --log-level=LEVEL     Level of messages to catch/display. Not set by
                        default, so it depends on the root/parent log
                        handler's effective level, where it is "WARNING" by
                        default.
  --log-format=LOG_FORMAT
                        Log format used by the logging module
  --log-date-format=LOG_DATE_FORMAT
                        Log date format used by the logging module
  --log-cli-level=LOG_CLI_LEVEL
                        CLI logging level
  --log-cli-format=LOG_CLI_FORMAT
                        Log format used by the logging module
  --log-cli-date-format=LOG_CLI_DATE_FORMAT
                        Log date format used by the logging module
  --log-file=LOG_FILE   Path to a file when logging will be written to
  --log-file-mode={w,a}
                        Log file open mode
  --log-file-level=LOG_FILE_LEVEL
                        Log file logging level
  --log-file-format=LOG_FILE_FORMAT
                        Log format used by the logging module
  --log-file-date-format=LOG_FILE_DATE_FORMAT
                        Log date format used by the logging module
  --log-auto-indent=LOG_AUTO_INDENT
                        Auto-indent multiline messages passed to the logging
                        module. Accepts true|on, false|off or an integer.
  --log-disable=LOGGER_DISABLE
                        Disable a logger by name. Can be passed multiple
                        times.
[pytest] configuration options in the first pytest.toml|pytest.ini|tox.ini|setup.cfg|pyproject.toml file found:

  markers (linelist):   Register new markers for test functions
  empty_parameter_set_mark (string):
                        Default marker for empty parametersets
  strict_config (bool): Any warnings encountered while parsing the `pytest`
                        section of the configuration file raise errors
  strict_markers (bool):
                        Markers not registered in the `markers` section of
                        the configuration file raise errors
  strict (bool):        Enables all strictness options, currently:
                        strict_config, strict_markers, strict_xfail,
                        strict_parametrization_ids
  filterwarnings (linelist):
                        Each line specifies a pattern for
                        warnings.filterwarnings. Processed after
                        -W/--pythonwarnings.
  max_warnings (string):
                        Exit with error if all tests pass but the number of
                        warnings exceeds this threshold
  norecursedirs (args): Directory patterns to avoid for recursion
  testpaths (args):     Directories to search for tests when no files or
                        directories are given on the command line
  collect_imported_tests (bool):
                        Whether to collect tests in imported modules outside
                        `testpaths`
  consider_namespace_packages (bool):
                        Consider namespace packages when resolving module
                        names during import
  usefixtures (args):   List of default fixtures to be used with this
                        project
  python_files (args):  Glob-style file patterns for Python test module
                        discovery
  python_classes (args):
                        Prefixes or glob names for Python test class
                        discovery
  python_functions (args):
                        Prefixes or glob names for Python test function and
                        method discovery
  disable_test_id_escaping_and_forfeit_all_rights_to_community_support (bool):
                        Disable string escape non-ASCII characters, might
                        cause unwanted side effects(use at your own risk)
  strict_parametrization_ids (bool):
                        Emit an error if non-unique parameter set IDs are
                        detected
  console_output_style (string):
                        Console output: "classic", or with additional
                        progress information ("progress" (percentage) |
                        "count" | "progress-even-when-capture-no" (forces
                        progress even when capture=no)
  verbosity_test_cases (string):
                        Specify a verbosity level for test case execution,
                        overriding the main level. Higher levels will
                        provide more detailed information about each test
                        case executed.
  strict_xfail (bool):  Default for the strict parameter of xfail markers
                        when not given explicitly (default: False) (alias:
                        xfail_strict)
  tmp_path_retention_count (string):
                        How many sessions should we keep the `tmp_path`
                        directories, according to
                        `tmp_path_retention_policy`.
  tmp_path_retention_policy (string):
```python
                        Controls which directories created by the `tmp_path`
                        fixture are kept around, based on test outcome.
                        (all/failed/none)
```
  enable_assertion_pass_hook (bool):
```python
                        Enables the pytest_assertion_pass hook. Make sure to
                        delete any previously generated pyc cache files.
```
  truncation_limit_lines (string):
                        Set threshold of LINES after which truncation will
                        take effect
  truncation_limit_chars (string):
                        Set threshold of CHARS after which truncation will
                        take effect
  assertion_text_diff_style (string):
```python
                        Choose how pytest renders diffs for string equality
                        assertions: ndiff or block
```
  verbosity_assertions (string):
                        Specify a verbosity level for assertions, overriding
                        the main level. Higher levels will provide more
                        detailed explanation when an assertion fails.
  junit_suite_name (string):
                        Test suite name for JUnit report
  junit_logging (string):
                        Write captured log messages to JUnit report: one of
                        no|log|system-out|system-err|out-err|all
  junit_log_passing_tests (bool):
                        Capture log information for passing tests to JUnit
                        report:
  junit_duration_report (string):
                        Duration time to report: one of total|call
  junit_family (string):
                        Emit XML for schema: one of legacy|xunit1|xunit2
  doctest_optionflags (args):
                        Option flags for doctests
  doctest_encoding (string):
                        Encoding used for doctest files
  cache_dir (string):   Cache directory path
  log_level (string):   Default value for --log-level
  log_format (string):  Default value for --log-format
  log_date_format (string):
                        Default value for --log-date-format
  log_cli (bool):       Enable log display during test run (also known as
                        "live logging")
  log_cli_level (string):
                        Default value for --log-cli-level
  log_cli_format (string):
                        Default value for --log-cli-format
  log_cli_date_format (string):
                        Default value for --log-cli-date-format
  log_file (string):    Default value for --log-file
  log_file_mode (string):
                        Default value for --log-file-mode
  log_file_level (string):
                        Default value for --log-file-level
  log_file_format (string):
                        Default value for --log-file-format
  log_file_date_format (string):
                        Default value for --log-file-date-format
  log_auto_indent (string):
                        Default value for --log-auto-indent
  faulthandler_timeout (string):
                        Dump the traceback of all threads if a test takes
                        more than TIMEOUT seconds to finish
  faulthandler_exit_on_timeout (bool):
                        Exit the test process if a test takes more than
                        faulthandler_timeout seconds to finish
  verbosity_subtests (string):
                        Specify verbosity level for subtests. Higher levels
                        will generate output for passed subtests. Failed
                        subtests are always reported.
  addopts (args):       Extra command line options
  minversion (string):  Minimally required pytest version
  pythonpath (paths):   Add paths to sys.path
  required_plugins (args):
                        Plugins that must be present for pytest to run
Environment variables:
  CI                       When set to a non-empty value, pytest knows it is running in a CI process and does not truncate summary info
  BUILD_NUMBER             Equivalent to CI
  PYTEST_ADDOPTS           Extra command line options
  PYTEST_PLUGINS           Comma-separated plugins to load during startup
  PYTEST_DISABLE_PLUGIN_AUTOLOAD Set to disable plugin auto-loading
  PYTEST_DEBUG             Set to enable debug tracing of pytest's internals
  PYTEST_DEBUG_TEMPROOT    Override the system temporary directory
  PYTEST_THEME             The Pygments style to use for code output
  PYTEST_THEME_MODE        Set the PYTEST_THEME to be either 'dark' or 'light'

to see available markers type: pytest --markers
to see available fixtures type: pytest --fixtures
(shown according to specified file_or_dir or current dir if not specified; fixtures with leading '_' are only shown with the '-v' option
