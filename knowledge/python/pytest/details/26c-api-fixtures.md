---
title: "Fixtures Reference"
source: "https://docs.pytest.org/en/stable/reference/reference.html"
version: "stable"
---

# Fixtures Reference

> 原始文档来源：https://docs.pytest.org/en/stable/reference/reference.html (API Reference 节选)

---

Tutorial: Fixtures reference

Fixtures are requested by test functions or other fixtures by declaring them as argument names.

Example of a test requiring a fixture:

```python
def test_output(capsys):
    print("hello")
    out, err = capsys.readouterr()
    assert out == "hello\n"
```
Example of a fixture requiring another fixture:

```python
@pytest.fixture
def db_session(tmp_path):
    fn = tmp_path / "db.file"
    return connect(fn)
```
For more details, consult the full fixtures docs.

```python
@pytest.fixture
@fixture(fixture_function: Callable[[...], object], *, scope: Literal['session', 'package', 'module', 'class', 'function'] | Callable[[str, Config], Literal['session', 'package', 'module', 'class', 'function']] = 'function', params: Iterable[object] | None = None, autouse: bool = False, ids: Sequence[object | None] | Callable[[Any], object | None] | None = None, name: str | None = None) → FixtureFunctionDefinition
```
[source]
@fixture(fixture_function: None = None, *, scope: Literal['session', 'package', 'module', 'class', 'function'] | Callable[[str, Config], Literal['session', 'package', 'module', 'class', 'function']] = 'function', params: Iterable[object] | None = None, autouse: bool = False, ids: Sequence[object | None] | Callable[[Any], object | None] | None = None, name: str | None = None) → FixtureFunctionMarker

Decorator to mark a fixture factory function.

This decorator can be used, with or without parameters, to define a fixture function.

The name of the fixture function can later be referenced to cause its invocation ahead of running tests: test modules or classes can use the pytest.mark.usefixtures(fixturename) marker.

Test functions can directly use fixture names as input arguments in which case the fixture instance returned from the fixture function will be injected.

Fixtures can provide their values to test functions using return or yield statements. When using yield the code block after the yield statement is executed as teardown code regardless of the test outcome, and must yield exactly once.

PARAMETERS:

scope –

The scope for which this fixture is shared; one of "function" (default), "class", "module", "package" or "session".

This parameter may also be a callable which receives (fixture_name, config) as parameters, and must return a str with one of the values mentioned above.

See Dynamic scope in the docs for more information.

params – An optional list of parameters which will cause multiple invocations of the fixture function and all of the tests using it. The current parameter is available in request.param.

autouse – If True, the fixture func is activated for all tests that can see it. If False (the default), an explicit reference is needed to activate the fixture.

ids – Sequence of ids each corresponding to the params so that they are part of the test id. If no ids are provided they will be generated automatically from the params.

name – The name of the fixture. This defaults to the name of the decorated function. If a fixture is used in the same module in which it is defined, the function name of the fixture will be shadowed by the function arg that requests the fixture; one way to resolve this is to name the decorated function fixture_<fixturename> and then use @pytest.fixture(name='<fixturename>').

capfd

Tutorial: How to capture stdout/stderr output

capfd()
[source]

Enable text capturing of writes to file descriptors 1 and 2.

The captured output is made available via capfd.readouterr() method calls, which return a (out, err) namedtuple. out and err will be text objects.

Returns an instance of CaptureFixture[str].

Example:

```python
def test_system_echo(capfd):
    os.system('echo "hello"')
    captured = capfd.readouterr()
    assert captured.out == "hello\n"
```
capfdbinary

Tutorial: How to capture stdout/stderr output

capfdbinary()
[source]

Enable bytes capturing of writes to file descriptors 1 and 2.

The captured output is made available via capfd.readouterr() method calls, which return a (out, err) namedtuple. out and err will be byte objects.

Returns an instance of CaptureFixture[bytes].

Example:

```python
def test_system_echo(capfdbinary):
    os.system('echo "hello"')
    captured = capfdbinary.readouterr()
    assert captured.out == b"hello\n"
```
caplog

Tutorial: How to manage logging

caplog()
[source]

Access and control log capturing.

Captured logs are available through the following properties/methods:

* caplog.messages        -> list of format-interpolated log messages
* caplog.text            -> string containing formatted log output
* caplog.records         -> list of logging.LogRecord instances
* caplog.record_tuples   -> list of (logger_name, level, message) tuples
* caplog.clear()         -> clear captured records and formatted log output string

Returns a pytest.LogCaptureFixture instance.

final class LogCaptureFixture
[source]

Provides access and control of log capturing.

property handler: LogCaptureHandler

Get the logging handler used by the fixture.

get_records(when)
[source]

Get the logging records for one of the possible test phases.

PARAMETERS:

when (Literal['setup', 'call', 'teardown']) – Which test phase to obtain the records from. Valid values are: “setup”, “call” and “teardown”.

RETURNS:

The list of captured records at the given stage.

RETURN TYPE:

list[LogRecord]

Added in version 3.4.

property text: str

The formatted log text.

property records: list[LogRecord]

The list of log records.

property record_tuples: list[tuple[str, int, str]]

A list of a stripped down version of log records intended for use in assertion comparison.

The format of the tuple is:

(logger_name, log_level, message)

property messages: list[str]

A list of format-interpolated log messages.

Unlike ‘records’, which contains the format string and parameters for interpolation, log messages in this list are all interpolated.

Unlike ‘text’, which contains the output from the handler, log messages in this list are unadorned with levels, timestamps, etc, making exact comparisons more reliable.

Note that traceback or stack info (from logging.exception() or the exc_info or stack_info arguments to the logging functions) is not included, as this is added by the formatter in the handler.

Added in version 3.7.

clear()
[source]

Reset the list of log records and the captured log text.

set_level(level, logger=None)
[source]

Set the threshold level of a logger for the duration of a test.

Logging messages which are less severe than this level will not be captured.

Changed in version 3.4: The levels of the loggers changed by this function will be restored to their initial values at the end of the test.

Will enable the requested logging level if it was disabled via logging.disable().

PARAMETERS:

level (int | str) – The level.

logger (str | None) – The logger to update. If not given, the root logger.

at_level(level, logger=None)
[source]

Context manager that sets the level for capturing of logs. After the end of the ‘with’ statement the level is restored to its original value.

Will enable the requested logging level if it was disabled via logging.disable().

PARAMETERS:

level (int | str) – The level.

logger (str | None) – The logger to update. If not given, the root logger.

filtering(filter_)
[source]

Context manager that temporarily adds the given filter to the caplog’s handler() for the ‘with’ statement block, and removes that filter at the end of the block.

PARAMETERS:

filter – A custom logging.Filter object.

Added in version 7.5.

capsys

Tutorial: How to capture stdout/stderr output

capsys()
[source]

Enable text capturing of writes to sys.stdout and sys.stderr.

The captured output is made available via capsys.readouterr() method calls, which return a (out, err) namedtuple. out and err will be text objects.

Returns an instance of CaptureFixture[str].

Example:

```python
def test_output(capsys):
    print("hello")
    captured = capsys.readouterr()
    assert captured.out == "hello\n"

class CaptureFixture
```
[source]

Object returned by the capsys, capsysbinary, capfd and capfdbinary fixtures.

readouterr()
[source]

Read and return the captured output so far, resetting the internal buffer.

RETURNS:

The captured content as a namedtuple with out and err string attributes.

RETURN TYPE:

CaptureResult

disabled()
[source]

Temporarily disable capturing while inside the with block.

capteesys

Tutorial: How to capture stdout/stderr output

capteesys()
[source]

Enable simultaneous text capturing and pass-through of writes to sys.stdout and sys.stderr as defined by --capture=.

The captured output is made available via capteesys.readouterr() method calls, which return a (out, err) namedtuple. out and err will be text objects.

The output is also passed-through, allowing it to be “live-printed”, reported, or both as defined by --capture=.

Returns an instance of CaptureFixture[str].

Example:

```python
def test_output(capteesys):
    print("hello")
    captured = capteesys.readouterr()
    assert captured.out == "hello\n"
```
capsysbinary

Tutorial: How to capture stdout/stderr output

capsysbinary()
[source]

Enable bytes capturing of writes to sys.stdout and sys.stderr.

The captured output is made available via capsysbinary.readouterr() method calls, which return a (out, err) namedtuple. out and err will be bytes objects.

Returns an instance of CaptureFixture[bytes].

Example:

```python
def test_output(capsysbinary):
    print("hello")
    captured = capsysbinary.readouterr()
    assert captured.out == b"hello\n"
```
config.cache

Tutorial: How to re-run failed tests and maintain state between test runs

The config.cache object allows other plugins and fixtures to store and retrieve values across test runs. To access it from fixtures request pytestconfig into your fixture and get it with pytestconfig.cache.

Under the hood, the cache plugin uses the simple dumps/loads API of the json stdlib module.

config.cache is an instance of pytest.Cache:

final class Cache
[source]

Instance of the cache fixture.

mkdir(name)
[source]

Return a directory path object with the given name.

If the directory does not yet exist, it will be created. You can use it to manage files to e.g. store/retrieve database dumps across test sessions.

Added in version 7.0.

PARAMETERS:

name (str) – Must be a string not containing a / separator. Make sure the name contains your plugin or application identifiers to prevent clashes with other cache users.

get(key, default)
[source]

Return the cached value for the given key.

If no value was yet cached or the value cannot be read, the specified default is returned.

PARAMETERS:

key (str) – Must be a / separated value. Usually the first name is the name of your plugin or your application.

default – The value to return in case of a cache-miss or invalid cache value.

set(key, value)
[source]

Save value for the given key.

PARAMETERS:

key (str) – Must be a / separated value. Usually the first name is the name of your plugin or your application.

value (object) – Must be of any combination of basic python types, including nested types like lists of dictionaries.

doctest_namespace

Tutorial: How to run doctests

doctest_namespace()
[source]

Fixture that returns a dict that will be injected into the namespace of doctests.

Usually this fixture is used in conjunction with another autouse fixture:

```python
@pytest.fixture(autouse=True)
def add_np(doctest_namespace):
    doctest_namespace["np"] = numpy
```
For more details: ‘doctest_namespace’ fixture.

monkeypatch

Tutorial: How to monkeypatch/mock modules and environments

monkeypatch()
[source]

A convenient fixture for monkey-patching.

The fixture provides these methods to modify objects, dictionaries, or os.environ:

monkeypatch.setattr(obj, name, value, raising=True)

monkeypatch.delattr(obj, name, raising=True)

monkeypatch.setitem(mapping, name, value)

monkeypatch.delitem(obj, name, raising=True)

monkeypatch.setenv(name, value, prepend=None)

monkeypatch.delenv(name, raising=True)

monkeypatch.syspath_prepend(path)

monkeypatch.chdir(path)

monkeypatch.context()

All modifications will be undone after the requesting test function or fixture has finished. The raising parameter determines if a KeyError or AttributeError will be raised if the set/deletion operation does not have the specified target.

To undo modifications done by the fixture in a contained scope, use context().

Returns a MonkeyPatch instance.

final class MonkeyPatch
[source]

Helper to conveniently monkeypatch attributes/items/environment variables/syspath.

Returned by the monkeypatch fixture.

Changed in version 6.2: Can now also be used directly as pytest.MonkeyPatch(), for when the fixture is not available. In this case, use with MonkeyPatch.context() as mp: or remember to call undo() explicitly.

classmethod context()
[source]

Context manager that returns a new MonkeyPatch object which undoes any patching done inside the with block upon exit.

Example:

```python
import functools

def test_partial(monkeypatch):
    with monkeypatch.context() as m:
        m.setattr(functools, "partial", 3)
```
Useful in situations where it is desired to undo some patches before the test ends, such as mocking stdlib functions that might break pytest itself if mocked (for examples of this see #3290).

setattr(target: str, name: object, value: NotSetType = NotSetType.token, raising: bool = True) → None
[source]
setattr(target: object, name: str, value: object, raising: bool = True) → None

Set attribute value on target, memorizing the old value.

For example:

import os
monkeypatch.setattr(os, "getcwd", lambda: "/")

The code above replaces the os.getcwd() function by a lambda which always returns "/".

For convenience, you can specify a string as target which will be interpreted as a dotted import path, with the last part being the attribute name:

monkeypatch.setattr("os.getcwd", lambda: "/")

Raises AttributeError if the attribute does not exist, unless raising is set to False.

Where to patch

monkeypatch.setattr works by (temporarily) changing the object that a name points to with another one. There can be many names pointing to any individual object, so for patching to work you must ensure that you patch the name used by the system under test.

See the section Where to patch in the unittest.mock docs for a complete explanation, which is meant for unittest.mock.patch() but applies to monkeypatch.setattr as well.

delattr(target, name=NotSetType.token, raising=True)
[source]

Delete attribute name from target.

If no name is specified and target is a string it will be interpreted as a dotted import path with the last part being the attribute name.

Raises AttributeError it the attribute does not exist, unless raising is set to False.

setitem(dic, name, value)
[source]

Set dictionary entry name to value.

delitem(dic, name, raising=True)
[source]

Delete name from dict.

Raises KeyError if it doesn’t exist, unless raising is set to False.

setenv(name, value, prepend=None)
[source]

Set environment variable name to value.

If prepend is a character, read the current environment variable value and prepend the value adjoined with the prepend character.

delenv(name, raising=True)
[source]

Delete name from the environment.

Raises KeyError if it does not exist, unless raising is set to False.

syspath_prepend(path)
[source]

Prepend path to sys.path list of import locations.

chdir(path)
[source]

Change the current working directory to the specified path.

PARAMETERS:

path (str | PathLike[str]) – The path to change into.

undo()
[source]

Undo previous changes.

This call consumes the undo stack. Calling it a second time has no effect unless you do more monkeypatching after the undo call.

There is generally no need to call undo(), since it is called automatically during tear-down.

Note

The same monkeypatch fixture is used across a single test function invocation. If monkeypatch is used both by the test function itself and one of the test fixtures, calling undo() will undo all of the changes made in both functions.

Prefer to use context() instead.

pytestconfig
pytestconfig()
[source]

Session-scoped fixture that returns the session’s pytest.Config object.

Example:

```python
def test_foo(pytestconfig):
    if pytestconfig.get_verbosity() > 0:
        ...
```
pytester

Added in version 6.2.

Provides a Pytester instance that can be used to run and test pytest itself.

It provides an empty directory where pytest can be executed in isolation, and contains facilities to write tests, configuration files, and match against expected output.

To use it, include in your topmost conftest.py file:

pytest_plugins = "pytester"

final class Pytester
[source]

Facilities to write tests/configuration files, execute pytest in isolation, and match against expected output, perfect for black-box testing of pytest plugins.

It attempts to isolate the test run from external factors as much as possible, modifying the current working directory to path and environment variables during initialization.

exception TimeoutExpired
[source]
plugins: list[str | object]

A list of plugins to use with parseconfig() and runpytest(). Initially this is an empty list but plugins can be added to the list.

When running in subprocess mode, specify plugins by name (str) - adding plugin objects directly is not supported.

property path: Path

Temporary directory path used to create files/run tests from, etc.

make_hook_recorder(pluginmanager)
[source]

Create a new HookRecorder for a PytestPluginManager.

chdir()
[source]

Cd into the temporary directory.

This is done automatically upon instantiation.

makefile(ext, *args, **kwargs)
[source]

Create new text file(s) in the test directory.

PARAMETERS:

ext (str) – The extension the file(s) should use, including the dot, e.g. .py.

args (str) – All args are treated as strings and joined using newlines. The result is written as contents to the file. The name of the file is based on the test function requesting this fixture.

kwargs (str) – Each keyword is the name of a file, while the value of it will be written as contents of the file.

RETURNS:

The first created file.

RETURN TYPE:

Path

Examples:

pytester.makefile(".txt", "line1", "line2")

pytester.makefile(".ini", pytest="[pytest]\naddopts=-rs\n")

To create binary files, use pathlib.Path.write_bytes() directly:

filename = pytester.path.joinpath("foo.bin")
filename.write_bytes(b"...")

makeconftest(source)
[source]

Write a conftest.py file.

PARAMETERS:

source (str) – The contents.

RETURNS:

The conftest.py file.

RETURN TYPE:

Path

makeini(source)
[source]

Write a tox.ini file.

PARAMETERS:

source (str) – The contents.

RETURNS:

The tox.ini file.

RETURN TYPE:

Path

maketoml(source)
[source]

Write a pytest.toml file.

PARAMETERS:

source (str) – The contents.

RETURNS:

The pytest.toml file.

RETURN TYPE:

Path

Added in version 9.0.

getinicfg(source)
[source]

Return the pytest section from the tox.ini config file.

makepyprojecttoml(source)
[source]

Write a pyproject.toml file.

PARAMETERS:

source (str) – The contents.

RETURNS:

The pyproject.ini file.

RETURN TYPE:

Path

Added in version 6.0.

makepyfile(*args, **kwargs)
[source]

Shortcut for .makefile() with a .py extension.

Defaults to the test name with a ‘.py’ extension, e.g test_foobar.py, overwriting existing files.

Examples:

```python
def test_something(pytester):
    # Initial file is created test_something.py.
    pytester.makepyfile("foobar")
    # To create multiple files, pass kwargs accordingly.
    pytester.makepyfile(custom="foobar")
    # At this point, both 'test_something.py' & 'custom.py' exist in the test directory.
```
maketxtfile(*args, **kwargs)
[source]

Shortcut for .makefile() with a .txt extension.

Defaults to the test name with a ‘.txt’ extension, e.g test_foobar.txt, overwriting existing files.

Examples:

```python
def test_something(pytester):
    # Initial file is created test_something.txt.
    pytester.maketxtfile("foobar")
    # To create multiple files, pass kwargs accordingly.
    pytester.maketxtfile(custom="foobar")
    # At this point, both 'test_something.txt' & 'custom.txt' exist in the test directory.
```
syspathinsert(path=None)
[source]

Prepend a directory to sys.path, defaults to path.

This is undone automatically when this object dies at the end of each test.

PARAMETERS:

path (str | PathLike[str] | None) – The path.

mkdir(name)
[source]

Create a new (sub)directory.

PARAMETERS:

name (str | PathLike[str]) – The name of the directory, relative to the pytester path.

RETURNS:

The created directory.

RETURN TYPE:

pathlib.Path

mkpydir(name)
[source]

Create a new python package.

This creates a (sub)directory with an empty __init__.py file so it gets recognised as a Python package.

copy_example(name=None)
[source]

Copy file from project’s directory into the testdir.

PARAMETERS:

name (str | None) – The name of the file to copy.

RETURNS:

Path to the copied directory (inside self.path).

RETURN TYPE:

pathlib.Path

getnode(config, arg)
[source]

Get the collection node of a file.

PARAMETERS:

config (Config) – A pytest config. See parseconfig() and parseconfigure() for creating it.

arg (str | PathLike[str]) – Path to the file.

RETURNS:

The node.

RETURN TYPE:

Collector | Item

getpathnode(path)
[source]

Return the collection node of a file.

This is like getnode() but uses parseconfigure() to create the (configured) pytest Config instance.

PARAMETERS:

path (str | PathLike[str]) – Path to the file.

RETURNS:

The node.

RETURN TYPE:

Collector | Item

genitems(colitems)
[source]

Generate all test items from a collection node.

This recurses into the collection node and returns a list of all the test items contained within.

PARAMETERS:

colitems (Sequence[Item | Collector]) – The collection nodes.

RETURNS:

The collected items.

RETURN TYPE:

list[Item]

runitem(source)
[source]

Run the “test_func” Item.

The calling test instance (class containing the test method) must provide a .getrunner() method which should return a runner which can run the test protocol for a single item, e.g. _pytest.runner.runtestprotocol.

inline_runsource(source, *cmdlineargs)
[source]

Run a test module in process using pytest.main().

This run writes “source” into a temporary file and runs pytest.main() on it, returning a HookRecorder instance for the result.

PARAMETERS:

source (str) – The source code of the test module.

cmdlineargs – Any extra command line arguments to use.

inline_genitems(*args)
[source]

Run pytest.main(['--collect-only']) in-process.

Runs the pytest.main() function to run all of pytest inside the test process itself like inline_run(), but returns a tuple of the collected items and a HookRecorder instance.

inline_run(*args, plugins=(), no_reraise_ctrlc=False)
[source]

Run pytest.main() in-process, returning a HookRecorder.

Runs the pytest.main() function to run all of pytest inside the test process itself. This means it can return a HookRecorder instance which gives more detailed results from that run than can be done by matching stdout/stderr from runpytest().

PARAMETERS:

args (str | PathLike[str]) – Command line arguments to pass to pytest.main().

plugins – Extra plugin instances the pytest.main() instance should use.

no_reraise_ctrlc (bool) – Typically we reraise keyboard interrupts from the child run. If True, the KeyboardInterrupt exception is captured.

runpytest_inprocess(*args, **kwargs)
[source]

Return result of running pytest in-process, providing a similar interface to what self.runpytest() provides.

runpytest(*args, **kwargs)
[source]

Run pytest inline or in a subprocess, depending on the command line option “–runpytest” and return a RunResult.

parseconfig(*args)
[source]

Return a new pytest pytest.Config instance from given commandline args.

This invokes the pytest bootstrapping code in _pytest.config to create a new pytest.PytestPluginManager and call the pytest_cmdline_parse hook to create a new pytest.Config instance.

If plugins has been populated they should be plugin modules to be registered with the plugin manager.

parseconfigure(*args)
[source]

Return a new pytest configured Config instance.

Returns a new pytest.Config instance like parseconfig(), but also calls the pytest_configure hook.

getitem(source, funcname='test_func')
[source]

Return the test item for a test function.

Writes the source to a python file and runs pytest’s collection on the resulting module, returning the test item for the requested function name.

PARAMETERS:

source (str | PathLike[str]) – The module source.

funcname (str) – The name of the test function for which to return a test item.

RETURNS:

The test item.

RETURN TYPE:

Item

getitems(source)
[source]

Return all test items collected from the module.

Writes the source to a Python file and runs pytest’s collection on the resulting module, returning all test items contained within.

getmodulecol(source, configargs=(), *, withinit=False)
[source]

Return the module collection node for source.

Writes source to a file using makepyfile() and then runs the pytest collection on it, returning the collection node for the test module.

PARAMETERS:

source (str | PathLike[str]) – The source code of the module to collect.

configargs – Any extra arguments to pass to parseconfigure().

withinit (bool) – Whether to also write an __init__.py file to the same directory to ensure it is a package.

collect_by_name(modcol, name)
[source]

Return the collection node for name from the module collection.

Searches a module collection node for a collection node matching the given name.

PARAMETERS:

modcol (Collector) – A module collection node; see getmodulecol().

name (str) – The name of the node to return.

popen(cmdargs, stdout=-1, stderr=-1, stdin=NotSetType.token, **kw)
[source]

Invoke subprocess.Popen.

Calls subprocess.Popen making sure the current working directory is in PYTHONPATH.

You probably want to use run() instead.

run(*cmdargs, timeout=None, stdin=NotSetType.token)
[source]

Run a command with arguments.

Run a process using subprocess.Popen saving the stdout and stderr.

PARAMETERS:

cmdargs (str | PathLike[str]) – The sequence of arguments to pass to subprocess.Popen, with path-like objects being converted to str automatically.

timeout (float | None) – The period in seconds after which to timeout and raise Pytester.TimeoutExpired.

stdin (_pytest.compat.NotSetType | bytes | IO[Any] | int) –

Optional standard input.

If it is CLOSE_STDIN (Default), then this method calls subprocess.Popen with stdin=subprocess.PIPE, and the standard input is closed immediately after the new command is started.

If it is of type bytes, these bytes are sent to the standard input of the command.

Otherwise, it is passed through to subprocess.Popen. For further information in this case, consult the document of the stdin parameter in subprocess.Popen.

RETURNS:

The result.

RETURN TYPE:

RunResult

runpython(script)
[source]

Run a python script using sys.executable as interpreter.

runpython_c(command)
[source]

Run python -c "command".

runpytest_subprocess(*args, timeout=None)
[source]

Run pytest as a subprocess with given arguments.

Any plugins added to the plugins list will be added using the -p command line option. Additionally --basetemp is used to put any temporary files and directories in a numbered directory prefixed with “runpytest-” to not conflict with the normal numbered pytest location for temporary files and directories.

PARAMETERS:

args (str | PathLike[str]) – The sequence of arguments to pass to the pytest subprocess.

timeout (float | None) – The period in seconds after which to timeout and raise Pytester.TimeoutExpired.

RETURNS:

The result.

RETURN TYPE:

RunResult

spawn_pytest(string, expect_timeout=10.0)
[source]

Run pytest using pexpect.

This makes sure to use the right pytest and sets up the temporary directory locations.

The pexpect child is returned.

spawn(cmd, expect_timeout=10.0)
[source]

Run a command using pexpect.

The pexpect child is returned.

final class RunResult
[source]

The result of running a command from Pytester.

ret: int | ExitCode

The return value.

outlines

List of lines captured from stdout.

errlines

List of lines captured from stderr.

stdout

LineMatcher of stdout.

Use e.g. str(stdout) to reconstruct stdout, or the commonly used stdout.fnmatch_lines() method.

stderr

LineMatcher of stderr.

duration

Duration in seconds.

parseoutcomes()
[source]

Return a dictionary of outcome noun -> count from parsing the terminal output that the test process produced.

The returned nouns will always be in plural form:

======= 1 failed, 1 passed, 1 warning, 1 error in 0.13s ====

Will return {"failed": 1, "passed": 1, "warnings": 1, "errors": 1}.

classmethod parse_summary_nouns(lines)
[source]

Extract the nouns from a pytest terminal summary line.

It always returns the plural noun for consistency:

======= 1 failed, 1 passed, 1 warning, 1 error in 0.13s ====

Will return {"failed": 1, "passed": 1, "warnings": 1, "errors": 1}.

assert_outcomes(passed=0, skipped=0, failed=0, errors=0, xpassed=0, xfailed=0, warnings=None, deselected=None)
[source]

Assert that the specified outcomes appear with the respective numbers (0 means it didn’t occur) in the text output from a test run.

warnings and deselected are only checked if not None.

class LineMatcher
[source]

Flexible matching of text.

This is a convenience class to test large texts like the output of commands.

The constructor takes a list of lines without their trailing newlines, i.e. text.splitlines().

__str__()
[source]

Return the entire original text.

Added in version 6.2: You can use str() in older versions.

fnmatch_lines_random(lines2)
[source]

Check lines exist in the output in any order (using fnmatch.fnmatch()).

re_match_lines_random(lines2)
[source]

Check lines exist in the output in any order (using re.match()).

get_lines_after(fnline)
[source]

Return all lines following the given line in the text.

The given line can contain glob wildcards.

fnmatch_lines(lines2, *, consecutive=False)
[source]

Check lines exist in the output (using fnmatch.fnmatch()).

The argument is a list of lines which have to match and can use glob wildcards. If they do not match a pytest.fail() is called. The matches and non-matches are also shown as part of the error message.

PARAMETERS:

lines2 (Sequence[str]) – String patterns to match.

consecutive (bool) – Match lines consecutively?

re_match_lines(lines2, *, consecutive=False)
[source]

Check lines exist in the output (using re.match()).

The argument is a list of lines which have to match using re.match. If they do not match a pytest.fail() is called.

The matches and non-matches are also shown as part of the error message.

PARAMETERS:

lines2 (Sequence[str]) – string patterns to match.

consecutive (bool) – match lines consecutively?

no_fnmatch_line(pat)
[source]

Ensure captured lines do not match the given pattern, using fnmatch.fnmatch.

PARAMETERS:

pat (str) – The pattern to match lines.

no_re_match_line(pat)
[source]

Ensure captured lines do not match the given pattern, using re.match.

PARAMETERS:

pat (str) – The regular expression to match lines.

str()
[source]

Return the entire original text.

final class HookRecorder
[source]

Record all hooks called in a plugin manager.

Hook recorders are created by Pytester.

This wraps all the hook calls in the plugin manager, recording each call before propagating the normal calls.

getcalls(names)
[source]

Get all recorded calls to hooks with the given names (or name).

matchreport(inamepart='', names=('pytest_runtest_logreport', 'pytest_collectreport'), when=None)
[source]

Return a testreport whose dotted import path matches.

final class RecordedHookCall
[source]

A recorded call to a hook.

The arguments to the hook call are set as attributes. For example:

calls = hook_recorder.getcalls("pytest_runtest_setup")
# Suppose pytest_runtest_setup was called once with `item=an_item`.
assert calls[0].item is an_item

record_property

Tutorial: record_property

record_property()
[source]

Add extra properties to the calling test.

User properties become part of the test report and are available to the configured reporters, like JUnit XML.

The fixture is callable with name, value. The value is automatically XML-encoded.

Example:

```python
def test_function(record_property):
    record_property("example_key", 1)
```
record_testsuite_property

Tutorial: record_testsuite_property

record_testsuite_property()
[source]

Record a new <property> tag as child of the root <testsuite>.

This is suitable to writing global information regarding the entire test suite, and is compatible with xunit2 JUnit family.

This is a session-scoped fixture which is called with (name, value). Example:

```python
def test_foo(record_testsuite_property):
    record_testsuite_property("ARCH", "PPC")
    record_testsuite_property("STORAGE_TYPE", "CEPH")
```
PARAMETERS:

name – The property name.

value – The property value. Will be converted to a string.

Warning

Currently this fixture does not work with the pytest-xdist plugin. See #7767 for details.

recwarn

Tutorial: Recording warnings

recwarn()
[source]

Return a WarningsRecorder instance that records all warnings emitted by test functions.

See How to capture warnings for information on warning categories.

class WarningsRecorder
[source]

A context manager to record raised warnings.

Each recorded warning is an instance of warnings.WarningMessage.

Adapted from warnings.catch_warnings.

Note

DeprecationWarning and PendingDeprecationWarning are treated differently; see Ensuring code triggers a deprecation warning.

property list: list[WarningMessage]

The list of recorded warnings.

__getitem__(i)
[source]

Get a recorded warning by index.

__iter__()
[source]

Iterate through the recorded warnings.

__len__()
[source]

The number of recorded warnings.

pop(cls=<class 'Warning'>)
[source]

Pop the first recorded warning which is an instance of cls, but not an instance of a child class of any other match. Raises AssertionError if there is no match.

clear()
[source]

Clear the list of recorded warnings.

request

Example: Pass different values to a test function, depending on command line options

The request fixture is a special fixture providing information of the requesting test function.

class FixtureRequest
[source]

The type of the request fixture.

A request object gives access to the requesting test context and has a param attribute in case the fixture is parametrized.

fixturename: Final

Fixture for which this request is being performed.

property scope: Literal['session', 'package', 'module', 'class', 'function']

Scope string, one of “function”, “class”, “module”, “package”, “session”.

property fixturenames: list[str]

Names of all active fixtures in this request.

abstract property node

Underlying collection node (depends on current request scope).

property config: Config

The pytest config object associated with this request.

property function

Test function object if the request has a per-function scope.

property cls

Class (can be None) where the test function was collected.

property instance

Instance (can be None) on which test function was collected.

property module

Python module object where the test function was collected.

property path: Path

Path where the test function was collected.

property keywords: MutableMapping[str, Any]

Keywords/markers dictionary for the underlying node.

property session: Session

Pytest session object.

abstractmethod addfinalizer(finalizer)
[source]

Add finalizer/teardown function to be called without arguments after the last test within the requesting test context finished execution.

applymarker(marker)
[source]

Apply a marker to a single test function invocation.

This method is useful if you don’t want to have a keyword/marker on all function invocations.

PARAMETERS:

marker (str | MarkDecorator) – An object created by a call to pytest.mark.NAME(...).

raiseerror(msg)
[source]

Raise a FixtureLookupError exception.

PARAMETERS:

msg (str | None) – An optional custom error message.

getfixturevalue(argname)
[source]

Dynamically run a named fixture function.

Declaring fixtures via function argument is recommended where possible. But if you can only decide whether to use another fixture at test setup time, you may use this function to retrieve it inside a fixture or test function body.

This method can be used during the test setup phase or the test run phase. Avoid using it during the teardown phase.

Changed in version 9.1: Calling request.getfixturevalue() during teardown to request a fixture that was not already requested is deprecated.

PARAMETERS:

argname (str) – The fixture name.

RAISES:

pytest.FixtureLookupError – If the given fixture could not be found.
subtests

The subtests fixture enables declaring subtests inside test functions.

Tutorial: How to use subtests

class Subtests
[source]

Subtests fixture, enables declaring subtests inside test functions via the test() method.

test(msg=None, **kwargs)
[source]

Context manager for subtests, capturing exceptions raised inside the subtest scope and reporting assertion failures and errors individually.

Usage
def test(subtests):
```python
    for i in range(5):
        with subtests.test("custom message", i=i):
            assert i % 2 == 0
```
PARAM MSG:

If given, the message will be shown in the test report in case of subtest failure.

PARAM KWARGS:

Arbitrary values that are also added to the subtest report.

testdir

Identical to pytester, but provides an instance whose methods return legacy py.path.local objects instead when applicable.

New code should avoid using testdir in favor of pytester.

final class Testdir
[source]

Similar to Pytester, but this class works with legacy legacy_path objects instead.

All methods just forward to an internal Pytester instance, converting results to legacy_path objects as necessary.

exception TimeoutExpired
property tmpdir: LocalPath

Temporary directory where tests are executed.

make_hook_recorder(pluginmanager)
[source]

See Pytester.make_hook_recorder().

chdir()
[source]

See Pytester.chdir().

makefile(ext, *args, **kwargs)
[source]

See Pytester.makefile().

makeconftest(source)
[source]

See Pytester.makeconftest().

makeini(source)
[source]

See Pytester.makeini().

getinicfg(source)
[source]

See Pytester.getinicfg().

makepyprojecttoml(source)
[source]

See Pytester.makepyprojecttoml().

makepyfile(*args, **kwargs)
[source]

See Pytester.makepyfile().

maketxtfile(*args, **kwargs)
[source]

See Pytester.maketxtfile().

syspathinsert(path=None)
[source]

See Pytester.syspathinsert().

mkdir(name)
[source]

See Pytester.mkdir().

mkpydir(name)
[source]

See Pytester.mkpydir().

copy_example(name=None)
[source]

See Pytester.copy_example().

getnode(config, arg)
[source]

See Pytester.getnode().

getpathnode(path)
[source]

See Pytester.getpathnode().

genitems(colitems)
[source]

See Pytester.genitems().

runitem(source)
[source]

See Pytester.runitem().

inline_runsource(source, *cmdlineargs)
[source]

See Pytester.inline_runsource().

inline_genitems(*args)
[source]

See Pytester.inline_genitems().

inline_run(*args, plugins=(), no_reraise_ctrlc=False)
[source]

See Pytester.inline_run().

runpytest_inprocess(*args, **kwargs)
[source]

See Pytester.runpytest_inprocess().

runpytest(*args, **kwargs)
[source]

See Pytester.runpytest().

parseconfig(*args)
[source]

See Pytester.parseconfig().

parseconfigure(*args)
[source]

See Pytester.parseconfigure().

getitem(source, funcname='test_func')
[source]

See Pytester.getitem().

getitems(source)
[source]

See Pytester.getitems().

getmodulecol(source, configargs=(), withinit=False)
[source]

See Pytester.getmodulecol().

collect_by_name(modcol, name)
[source]

See Pytester.collect_by_name().

popen(cmdargs, stdout=-1, stderr=-1, stdin=NotSetType.token, **kw)
[source]

See Pytester.popen().

run(*cmdargs, timeout=None, stdin=NotSetType.token)
[source]

See Pytester.run().

runpython(script)
[source]

See Pytester.runpython().

runpython_c(command)
[source]

See Pytester.runpython_c().

runpytest_subprocess(*args, timeout=None)
[source]

See Pytester.runpytest_subprocess().

spawn_pytest(string, expect_timeout=10.0)
[source]

See Pytester.spawn_pytest().

spawn(cmd, expect_timeout=10.0)
[source]

See Pytester.spawn().

tmp_path

Tutorial: How to use temporary directories and files in tests

tmp_path()
[source]

Return a temporary directory (as pathlib.Path object) which is unique to each test function invocation. The temporary directory is created as a subdirectory of the base temporary directory, with configurable retention, as discussed in Temporary directory location and retention.

tmp_path_factory

Tutorial: The tmp_path_factory fixture

tmp_path_factory is an instance of TempPathFactory:

final class TempPathFactory
[source]

Factory for temporary directories under the common base temp directory, as discussed at Temporary directory location and retention.

mktemp(basename, numbered=True)
[source]

Create a new temporary directory managed by the factory.

PARAMETERS:

basename (str) – Directory base name, must be a relative path.

numbered (bool) – If True, ensure the directory is unique by adding a numbered suffix greater than any existing one: basename="foo-" and numbered=True means that this function will create directories named "foo-0", "foo-1", "foo-2" and so on.

RETURNS:

The path to the new directory.

RETURN TYPE:

Path

getbasetemp()
[source]

Return the base temporary directory, creating it if needed.

RETURNS:

The base temporary directory.

RETURN TYPE:

Path

tmpdir

Tutorial: The tmpdir and tmpdir_factory fixtures

tmpdir()

Return a temporary directory (as legacy_path object) which is unique to each test function invocation. The temporary directory is created as a subdirectory of the base temporary directory, with configurable retention, as discussed in Temporary directory location and retention.

Note

These days, it is preferred to use tmp_path.

About the tmpdir and tmpdir_factory fixtures.

tmpdir_factory

Tutorial: The tmpdir and tmpdir_factory fixtures

tmpdir_factory is an instance of TempdirFactory:

final class TempdirFactory
[source]

Backward compatibility wrapper that implements py.path.local for TempPathFactory.

Note

These days, it is preferred to use tmp_path_factory.

About the tmpdir and tmpdir_factory fixtures.

mktemp(basename, numbered=True)
[source]

Same as TempPathFactory.mktemp(), but returns a py.path.local object.

getbasetemp()
[source]

Same as TempPathFactory.getbasetemp(), but returns a py.path.local object.

Hooks
