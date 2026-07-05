---
title: "Exceptions and Warnings"
source: "https://docs.pytest.org/en/stable/reference/reference.html"
version: "stable"
---

# Exceptions and Warnings

> 原始文档来源：https://docs.pytest.org/en/stable/reference/reference.html (API Reference 节选)

---

exception UsageError

Bases: Exception

Error in pytest usage or invocation.

final exception FixtureLookupError
[source]

Bases: LookupError

Could not return a requested fixture (missing or invalid).

Warnings

Custom warnings generated in some situations such as improper usage or deprecated features.

class PytestWarning

Bases: UserWarning

Base class for all warnings emitted by pytest.

class PytestAssertRewriteWarning

Bases: PytestWarning

Warning emitted by the pytest assert rewrite module.

class PytestCacheWarning

Bases: PytestWarning

Warning emitted by the cache plugin in various situations.

class PytestCollectionWarning

Bases: PytestWarning

Warning emitted when pytest is not able to collect a file or symbol in a module.

class PytestConfigWarning

Bases: PytestWarning

Warning emitted for configuration issues.

class PytestDeprecationWarning

Bases: PytestWarning, DeprecationWarning

Warning class for features that will be removed in a future version.

class PytestExperimentalApiWarning

Bases: PytestWarning, FutureWarning

Warning category used to denote experiments in pytest.

Use sparingly as the API might change or even be removed completely in a future version.

class PytestReturnNotNoneWarning

Bases: PytestWarning

Warning emitted when a test function returns a value other than None.

See Returning non-None value in test functions for details.

class PytestRemovedIn10Warning

Bases: PytestDeprecationWarning

Warning class for features that will be removed in pytest 10.

class PytestUnknownMarkWarning

Bases: PytestWarning

Warning emitted on use of unknown markers.

See How to mark test functions with attributes for details.

class PytestUnraisableExceptionWarning

Bases: PytestWarning

An unraisable exception was reported.

Unraisable exceptions are exceptions raised in __del__ implementations and similar situations when the exception cannot be raised as normal.

class PytestUnhandledThreadExceptionWarning

Bases: PytestWarning

An unhandled exception occurred in a Thread.

Such exceptions don’t propagate normally.

Consult the Internal pytest warnings section in the documentation for more information.

Configuration Options

Here is a list of builtin configuration options that may be written in a pytest.ini (or .pytest.ini), pyproject.toml, tox.ini, or setup.cfg file, usually located at the root of your repository.

To see each file format in detail, see Configuration file formats.

Warning

Usage of setup.cfg is not recommended except for very simple use cases. .cfg files use a different parser than pytest.ini and tox.ini which might cause hard to track down problems. When possible, it is recommended to use the latter files, or pytest.toml or pyproject.toml, to hold your pytest configuration.

Configuration options may be overwritten in the command-line by using -o/--override-ini, which can also be passed multiple times. The expected format is name=value. For example:

pytest -o console_output_style=classic -o cache_dir=/tmp/mycache

addopts
TYPE:
list[str]

Add the specified OPTS to the set of command line arguments as if they had been specified by the user. Example: if you have this configuration file content:

# content of pytest.toml
[pytest]
addopts = ["--maxfail=2", "-rf"]  # exit after 2 failures, report fail info

issuing pytest test_hello.py actually means:

pytest --maxfail=2 -rf test_hello.py

cache_dir
TYPE:
str
DEFAULT:
".pytest_cache"

Sets the directory where the cache plugin’s content is stored. Directory may be relative or absolute path. If setting relative path, then directory is created relative to rootdir. Additionally, a path may contain environment variables, that will be expanded. For more information about cache plugin please refer to How to re-run failed tests and maintain state between test runs.

collect_imported_tests
TYPE:
bool
DEFAULT:
true

Added in version 8.4.

Setting this to false will make pytest collect classes/functions from test files only if they are defined in that file (as opposed to imported there).

toml
[pytest]
collect_imported_tests = false

ini

pytest traditionally collects classes/functions in the test module namespace even if they are imported from another file.

For example:

# contents of src/domain.py
```python
class Testament: ...

# contents of tests/test_testament.py
```
```python
from domain import Testament

def test_testament(): ...
```
In this scenario, with the default options, pytest will collect the class Testament from tests/test_testament.py because it starts with Test, even though in this case it is a production class being imported in the test module namespace.

Set collected_imported_tests to false in the configuration file prevents that.

consider_namespace_packages
TYPE:
bool
DEFAULT:
false

Controls if pytest should attempt to identify namespace packages when collecting Python modules.

Set to True if the package you are testing is part of a namespace package. Namespace packages are also supported as --pyargs target.

Only native namespace packages are supported, with no plans to support legacy namespace packages.

For best results when using consider_namespace_packages, pytest needs to be able to import your namespace packages. This is best achieved by installing the packages in your environment, most commonly in “editable” mode. If you can’t install the packages, consider adding the namespace root paths to pythonpath.

Added in version 8.1.

console_output_style
TYPE:
str
DEFAULT:
"progress"

Sets the console output style while running tests:

classic: classic pytest output.

progress: like classic pytest output, but with a progress indicator.

progress-even-when-capture-no: allows the use of the progress indicator even when capture=no.

count: like progress, but shows progress as the number of tests completed instead of a percent.

times: show tests duration.

You can fallback to classic if you prefer or the new mode is causing unexpected problems:

toml
[pytest]
console_output_style = "classic"

ini
disable_test_id_escaping_and_forfeit_all_rights_to_community_support
TYPE:
bool
DEFAULT:
false

Added in version 4.4.

pytest by default escapes any non-ascii characters used in unicode strings for the parametrization because it has several downsides. If however you would like to use unicode strings in parametrization and see them in the terminal as is (non-escaped), use this option in your configuration file:

toml
[pytest]
disable_test_id_escaping_and_forfeit_all_rights_to_community_support = true

ini

Keep in mind however that this might cause unwanted side effects and even bugs depending on the OS used and plugins currently installed, so use it at your own risk.

See @pytest.mark.parametrize: parametrizing test functions.

doctest_encoding
TYPE:
str
DEFAULT:
"utf-8"

Default encoding to use to decode text files with docstrings. See how pytest handles doctests.

doctest_optionflags
TYPE:
list[str]

One or more doctest flag names from the standard doctest module. See how pytest handles doctests.

empty_parameter_set_mark
TYPE:
str
DEFAULT:
"skip"

Allows to pick the action for empty parametersets in parameterization

skip skips tests with an empty parameterset

xfail marks tests with an empty parameterset as xfail(run=False)

fail_at_collect raises an exception if parametrize collects an empty parameter set

toml
[pytest]
empty_parameter_set_mark = "xfail"

ini

Note

The default value of this option is planned to change to xfail in future releases as this is considered less error prone, see #3155 for more details.

enable_assertion_pass_hook
TYPE:
bool
DEFAULT:
false

Enables the pytest_assertion_pass hook. Make sure to delete any previously generated .pyc cache files.

toml
[pytest]
enable_assertion_pass_hook = true

ini
faulthandler_exit_on_timeout
TYPE:
bool
DEFAULT:
false

Exit the pytest process after the per-test timeout is reached by passing exit=True to the faulthandler.dump_traceback_later() function. This is particularly useful to avoid wasting CI resources for test suites that are prone to putting the main Python interpreter into a deadlock state.

toml
[pytest]
faulthandler_timeout = 5
faulthandler_exit_on_timeout = true

ini
faulthandler_timeout
TYPE:
float
DEFAULT:
0 (disabled)

Dumps the tracebacks of all threads if a test takes longer than X seconds to run (including fixture setup and teardown). Implemented using the faulthandler.dump_traceback_later() function, so all caveats there apply.

toml
[pytest]
faulthandler_timeout = 5

ini

For more information please refer to Fault Handler.

filterwarnings
TYPE:
list[str]

Sets a list of filters and actions that should be taken for matched warnings. By default all warnings emitted during the test session will be displayed in a summary at the end of the test session.

toml
[pytest]
filterwarnings = [
    'error',
    'ignore::DeprecationWarning',
    # Note the use of single quote below to denote "raw" strings in TOML.
    'ignore:function ham\(\) should not be used:UserWarning',
]

ini

This tells pytest to ignore deprecation warnings and turn all other warnings into errors. For more information please refer to How to capture warnings.

max_warnings
TYPE:
int

Added in version 9.1.

Maximum number of warnings allowed before the test run is considered a failure. When all tests pass, but the total number of warnings exceeds this value, pytest exits with pytest.ExitCode MAX_WARNINGS_ERROR (code 6).

toml
[pytest]
max_warnings = 10

ini

Note that filtered warnings do not count toward this maximum total.

Can also be set via the --max-warnings command-line option.

junit_duration_report
TYPE:
str
DEFAULT:
"total"

Added in version 4.1.

Configures how durations are recorded into the JUnit XML report:

total: duration times reported include setup, call, and teardown times.

call: duration times reported include only call times, excluding setup and teardown.

toml
[pytest]
junit_duration_report = "call"

ini
junit_family
TYPE:
str
DEFAULT:
"xunit2"

Added in version 4.2.

Changed in version 6.1: Default changed to xunit2.

Configures the format of the generated JUnit XML file. The possible options are:

xunit1 (or legacy): produces old style output, compatible with the xunit 1.0 format.

xunit2: produces xunit 2.0 style output, which should be more compatible with latest Jenkins versions.

toml
[pytest]
junit_family = "xunit2"

ini
junit_log_passing_tests
TYPE:
bool
DEFAULT:
true

Added in version 4.6.

If junit_logging != "no", configures if the captured output should be written to the JUnit XML file for passing tests.

toml
[pytest]
junit_log_passing_tests = false

ini
junit_logging
TYPE:
str
DEFAULT:
"no"

Added in version 3.5.

Changed in version 5.4: log, all, out-err options added.

Configures if captured output should be written to the JUnit XML file. Valid values are:

log: write only logging captured output.

system-out: write captured stdout contents.

system-err: write captured stderr contents.

out-err: write both captured stdout and stderr contents.

all: write captured logging, stdout and stderr contents.

no: no captured output is written.

toml
[pytest]
junit_logging = "system-out"

ini
junit_suite_name
TYPE:
str
DEFAULT:
"pytest"

To set the name of the root test suite xml item, you can configure the junit_suite_name option in your config file:

toml
[pytest]
junit_suite_name = "my_suite"

ini
log_auto_indent
TYPE:
str
DEFAULT:
"false"

Allow selective auto-indentation of multiline log messages.

Supports command line option --log-auto-indent=[value] and config option log_auto_indent = [value] to set the auto-indentation behavior for all logging.

[value] can be:

“True” or “On” - Dynamically auto-indent multiline log messages

“False” or “Off” or “0” - Do not auto-indent multiline log messages

“[positive integer]” - auto-indent multiline log messages by [value] spaces

toml
[pytest]
log_auto_indent = "false"

ini

Supports passing kwarg extra={"auto_indent": [value]} to calls to logging.log() to specify auto-indentation behavior for a specific entry in the log. extra kwarg overrides the value specified on the command line or in the config.

log_cli
TYPE:
bool
DEFAULT:
false

Enable log display during test run (also known as “live logging”).

toml
[pytest]
log_cli = true

ini
log_cli_date_format
TYPE:
str
DEFAULT:
Fallback to log_date_format

Sets a time.strftime()-compatible string that will be used when formatting dates for live logging.

toml
[pytest]
log_cli_date_format = "%Y-%m-%d %H:%M:%S"

ini

For more information, see Live Logs.

log_cli_format
TYPE:
str
DEFAULT:
Fallback to log_format

Sets a logging-compatible string used to format live logging messages.

toml
[pytest]
log_cli_format = "%(asctime)s %(levelname)s %(message)s"

ini

For more information, see Live Logs.

log_cli_level
TYPE:
str
DEFAULT:
Fallback to log_level

Sets the minimum log message level that should be captured for live logging. The integer value or the names of the levels can be used. Note in TOML the integer must be quoted, as there is no support for config parameters of mixed type.

toml
[pytest]
log_cli_level = "INFO"
log_cli_level = "10"

ini

For more information, see Live Logs.

log_date_format
TYPE:
str
DEFAULT:
"%H:%M:%S"

Sets a time.strftime()-compatible string that will be used when formatting dates for logging capture.

toml
[pytest]
log_date_format = "%Y-%m-%d %H:%M:%S"

ini

For more information, see How to manage logging.

log_file
TYPE:
str

Sets a file name relative to the current working directory where log messages should be written to, in addition to the other logging facilities that are active.

toml
[pytest]
log_file = "logs/pytest-logs.txt"

ini

For more information, see How to manage logging.

log_file_date_format
TYPE:
str
DEFAULT:
Fallback to log_date_format

Sets a time.strftime()-compatible string that will be used when formatting dates for the logging file.

toml
[pytest]
log_file_date_format = "%Y-%m-%d %H:%M:%S"

ini

For more information, see How to manage logging.

log_file_format
TYPE:
str
DEFAULT:
Fallback to log_format

Sets a logging-compatible string used to format logging messages redirected to the logging file.

toml
[pytest]
log_file_format = "%(asctime)s %(levelname)s %(message)s"

ini

For more information, see How to manage logging.

log_file_level
TYPE:
str
DEFAULT:
Fallback to log_level

Sets the minimum log message level that should be captured for the logging file. The integer value (in TOML, as a string) or the names of the levels can be used.

toml
[pytest]
log_file_level = "INFO"
log_cli_level = "10"

ini

For more information, see How to manage logging.

log_file_mode
TYPE:
str
DEFAULT:
"w"

Sets the mode that the logging file is opened with. The options are "w" to recreate the file or "a" to append to the file.

toml
[pytest]
log_file_mode = "a"

ini

For more information, see How to manage logging.

log_format
TYPE:
str
DEFAULT:
%(levelname)-8s %(name)s:%(filename)s:%(lineno)d %(message)s

Sets a logging-compatible string used to format captured logging messages.

toml
[pytest]
log_format = "%(asctime)s %(levelname)s %(message)s"

ini

For more information, see How to manage logging.

log_level
TYPE:
str

Sets the minimum log message level that should be captured for logging capture. Not set by default, so it depends on the root/parent log handler’s effective level, where it is "WARNING" by default. The integer value (in TOML, as a string) or the names of the levels can be used.

toml
[pytest]
log_level = "INFO"
log_cli_level = "10"

ini

For more information, see How to manage logging.

markers
TYPE:
list[str]

When the strict_markers configuration option is set, only known markers - defined in code by core pytest or some plugin - are allowed.

You can list additional markers in this setting to add them to the whitelist, in which case you probably want to set strict_markers to true to avoid future regressions:

toml
[pytest]
addopts = ["--strict-markers"]
markers = ["slow", "serial"]

ini
minversion
TYPE:
str

Specifies a minimal pytest version required for running tests.

toml
[pytest]
minversion = 3.0  # will fail if we run with pytest-2.8

ini
norecursedirs
TYPE:
list[str]
DEFAULT:
["*.egg", ".*", "_darcs", "build", "CVS", "dist", "node_modules", "venv", "{arch}"]

Set the directory basename patterns to avoid when recursing for test discovery. The individual (fnmatch-style) patterns are applied to the basename of a directory to decide if to recurse into it. Pattern matching characters:

*       matches everything
?       matches any single character
[seq]   matches any character in seq
[!seq]  matches any char not in seq

Setting a norecursedirs replaces the default. Here is an example of how to avoid certain directories:

toml
[pytest]
norecursedirs = [".svn", "_build", "tmp*"]

ini

This would tell pytest to not look into typical subversion or sphinx-build directories or into any tmp prefixed directory.

Additionally, pytest will attempt to intelligently identify and ignore a virtualenv. Any directory deemed to be the root of a virtual environment will not be considered during test collection unless --collect-in-virtualenv is given. Note also that norecursedirs takes precedence over --collect-in-virtualenv; e.g. if you intend to run tests in a virtualenv with a base directory that matches '.*' you must override norecursedirs in addition to using the --collect-in-virtualenv flag.

python_classes
TYPE:
list[str]
DEFAULT:
["Test"]

One or more name prefixes or glob-style patterns determining which classes are considered for test collection. Search for multiple glob patterns by adding a space between patterns. By default, pytest will consider any class prefixed with Test as a test collection. Here is an example of how to collect tests from classes that end in Suite:

toml
[pytest]
python_classes = ["*Suite"]

ini

Note that unittest.TestCase derived classes are always collected regardless of this option, as unittest’s own collection framework is used to collect those tests.

python_files
TYPE:
list[str]
DEFAULT:
["test_*.py", "*_test.py"]

One or more Glob-style file patterns determining which python files are considered as test modules. Search for multiple glob patterns by adding a space between patterns:

toml
[pytest]
python_files = ["test_*.py", "check_*.py", "example_*.py"]

ini
python_functions
TYPE:
list[str]
DEFAULT:
["test"]

One or more name prefixes or glob-patterns determining which test functions and methods are considered tests. Search for multiple glob patterns by adding a space between patterns. By default, pytest will consider any function prefixed with test as a test. Here is an example of how to collect test functions and methods that end in _test:

toml
[pytest]
python_functions = ["*_test"]

ini

Note that this has no effect on methods that live on a unittest.TestCase derived class, as unittest’s own collection framework is used to collect those tests.

See Changing naming conventions for more detailed examples.

pythonpath
TYPE:
list[str]

Sets list of directories that should be added to the python search path. Directories will be added to the head of sys.path. Similar to the PYTHONPATH environment variable, the directories will be included in where Python will look for imported modules. Paths are relative to the rootdir directory. Directories remain in path for the duration of the test session.

toml
[pytest]
pythonpath = ["src1", "src2"]

ini
required_plugins
TYPE:
list[str]

A space separated list of plugins that must be present for pytest to run. Plugins can be listed with or without version specifiers directly following their name. Whitespace between different version specifiers is not allowed. If any one of the plugins is not found, emit an error.

toml
[pytest]
required_plugins = ["pytest-django>=3.0.0,<4.0.0", "pytest-html", "pytest-xdist>=1.0.0"]

ini
strict
TYPE:
bool
DEFAULT:
false

If set to true, enable “strict mode”, which enables the following options:

strict_config

strict_markers

strict_parametrization_ids

strict_xfail

Plugins may also enable their own strictness options.

If you explicitly set an individual strictness option, it takes precedence over strict.

Note

If pytest adds new strictness options in the future, they will also be enabled in strict mode. Therefore, you should only enable strict mode if you use a pinned/locked version of pytest, or if you want to proactively adopt new strictness options as they are added.

toml
[pytest]
strict = true

ini

Added in version 9.0.

strict_config
TYPE:
bool
DEFAULT:
false

If set to true, any warnings encountered while parsing the pytest section of the configuration file will raise errors.

toml
[pytest]
strict_config = true

ini

You can also enable this option via the strict option.

strict_markers
TYPE:
bool
DEFAULT:
false

If set to true, markers not registered in the markers section of the configuration file will raise errors.

toml
[pytest]
strict_markers = true

ini

You can also enable this option via the strict option.

strict_parametrization_ids
TYPE:
bool
DEFAULT:
false

If set to true, pytest emits an error if it detects non-unique parameter set IDs.

If not set, pytest automatically handles this by adding 0, 1, … to duplicate IDs, making them unique.

toml
[pytest]
strict_parametrization_ids = true

ini

You can also enable this option via the strict option.

For example,

```python
import pytest

@pytest.mark.parametrize("letter", ["a", "a"])
def test_letter_is_ascii(letter):
    assert letter.isascii()
```
will emit an error because both cases (parameter sets) have the same auto-generated ID “a”.

To fix the error, if you decide to keep the duplicates, explicitly assign unique IDs:

```python
import pytest

@pytest.mark.parametrize("letter", ["a", "a"], ids=["a0", "a1"])
def test_letter_is_ascii(letter):
    assert letter.isascii()
```
See parametrize and pytest.param() for other ways to set IDs.

strict_xfail
TYPE:
bool
DEFAULT:
false

If set to true, tests marked with @pytest.mark.xfail that actually succeed will by default fail the test suite. For more information, see strict parameter.

toml
[pytest]
strict_xfail = true

ini

You can also enable this option via the strict option.

Changed in version 9.0: Renamed from xfail_strict to strict_xfail. xfail_strict is accepted as an alias for strict_xfail.

testpaths
TYPE:
list[str]

Sets list of directories that should be searched for tests when no specific directories, files or test ids are given in the command line when executing pytest from the rootdir directory. File system paths may use shell-style wildcards, including the recursive ** pattern.

Useful when all project tests are in a known location to speed up test collection and to avoid picking up undesired tests by accident.

toml
[pytest]
testpaths = ["testing", "doc"]

ini

This configuration means that executing:

pytest

has the same practical effects as executing:

pytest testing doc

tmp_path_retention_count
TYPE:
str
DEFAULT:
"3"

How many sessions should pytest keep the tmp_path directories, according to tmp_path_retention_policy.

toml
[pytest]
tmp_path_retention_count = "3"

ini
tmp_path_retention_policy
TYPE:
str
DEFAULT:
"all"

Controls which directories created by the tmp_path fixture are kept around, based on test outcome.

all: retains directories for all tests, regardless of the outcome.

failed: retains directories only for tests with outcome error or failed.

none: directories are always removed after each test ends, regardless of the outcome.

toml
[pytest]
tmp_path_retention_policy = "all"

ini
truncation_limit_chars
TYPE:
int
DEFAULT:
640

Controls maximum number of characters to truncate assertion message contents.

Setting value to 0 disables the character limit for truncation.

toml
[pytest]
truncation_limit_chars = 640

ini

pytest truncates the assert messages to a certain limit by default to prevent comparison with large data to overload the console output.

Note

If pytest detects it is running on CI, truncation is disabled automatically.

truncation_limit_lines
TYPE:
int
DEFAULT:
8

Controls maximum number of lines to truncate assertion message contents.

Setting value to 0 disables the lines limit for truncation.

toml
[pytest]
truncation_limit_lines = 8

ini

pytest truncates the assert messages to a certain limit by default to prevent comparison with large data to overload the console output.

Note

If pytest detects it is running on CI, truncation is disabled automatically.

usefixtures
TYPE:
list[str]

List of fixtures that will be applied to all test functions; this is semantically the same to apply the @pytest.mark.usefixtures marker to all test functions.

toml
[pytest]
usefixtures = ["clean_db"]

ini
verbosity_assertions
TYPE:
str
DEFAULT:
"auto"

Set a verbosity level specifically for assertion related output, overriding the application wide level.

toml
[pytest]
verbosity_assertions = "2"

ini

A special value of "auto" can be used to explicitly use the global verbosity level.

assertion_text_diff_style
TYPE:
str
DEFAULT:
"ndiff"

Set how pytest renders diffs for string equality assertions.

Supported values are:

ndiff: use the inline diff rendering markers.

block: render each string in separate Left: and Right: blocks.

toml
[pytest]
assertion_text_diff_style = "block"

ini
verbosity_subtests
TYPE:
str
DEFAULT:
"auto"

Set the verbosity level specifically for passed subtests.

toml
[pytest]
verbosity_subtests = "1"

ini

A value of 1 or higher will show output for passed subtests (failed subtests are always reported). Passed subtests output can be suppressed with the value 0, which overwrites the -v command-line option.

A special value of "auto" can be used to explicitly use the global verbosity level.

See also: How to use subtests.

verbosity_test_cases
TYPE:
str
DEFAULT:
"auto"

Set a verbosity level specifically for test case execution related output, overriding the application wide level.

toml
[pytest]
verbosity_test_cases = "2"

ini

A special value of "auto" can be used to explicitly use the global verbosity level.

Command-line Flags
