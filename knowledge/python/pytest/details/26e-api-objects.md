---
title: "Objects Reference"
source: "https://docs.pytest.org/en/stable/reference/reference.html"
version: "stable"
---

# Objects Reference

> 原始文档来源：https://docs.pytest.org/en/stable/reference/reference.html (API Reference 节选)

---

Objects accessible from fixtures or hooks or importable from pytest.

CallInfo
final class CallInfo
[source]

Result/Exception info of a function invocation.

excinfo: ExceptionInfo[BaseException] | None

The captured exception of the call, if it raised.

start: float

The system time when the call started, in seconds since the epoch.

stop: float

The system time when the call ended, in seconds since the epoch.

duration: float

The call duration, in seconds.

when: Literal['collect', 'setup', 'call', 'teardown']

The context of invocation: “collect”, “setup”, “call” or “teardown”.

property result: TResult

The return value of the call, if it didn’t raise.

Can only be accessed if excinfo is None.

classmethod from_call(func, when, reraise=None)
[source]

Call func, wrapping the result in a CallInfo.

PARAMETERS:

func (Callable[[], _pytest.runner.TResult]) – The function to call. Called without arguments.

when (Literal['collect', 'setup', 'call', 'teardown']) – The phase in which the function is called.

reraise (type[BaseException] | tuple[type[BaseException], ...] | None) – Exception or exceptions that shall propagate if raised by the function, instead of being wrapped in the CallInfo.

CollectReport
final class CollectReport
[source]

Bases: BaseReport

Collection report object.

Reports can contain arbitrary extra attributes.

nodeid: str

Normalized collection nodeid.

outcome: Literal['passed', 'failed', 'skipped']

Test outcome, always one of “passed”, “failed”, “skipped”.

longrepr: None | ExceptionInfo[BaseException] | tuple[str, int, str] | str | TerminalRepr

None or a failure representation.

result

The collected items and collection nodes.

sections: list[tuple[str, str]]

Tuples of str (heading, content) with extra information for the test report. Used by pytest to add text captured from stdout, stderr, and intercepted logging events. May be used by other plugins to add arbitrary information to reports.

property caplog: str

Return captured log lines, if log capturing is enabled.

Added in version 3.5.

property capstderr: str

Return captured text from stderr, if capturing is enabled.

Added in version 3.0.

property capstdout: str

Return captured text from stdout, if capturing is enabled.

Added in version 3.0.

property count_towards_summary: bool

Experimental Whether this report should be counted towards the totals shown at the end of the test session: “1 passed, 1 failure, etc”.

Note

This function is considered experimental, so beware that it is subject to changes even in patch releases.

property failed: bool

Whether the outcome is failed.

property fspath: str

The path portion of the reported node, as a string.

property head_line: str | None

Experimental The head line shown with longrepr output for this report, more commonly during traceback representation during failures:

________ Test.foo ________

In the example above, the head_line is “Test.foo”.

Note

This function is considered experimental, so beware that it is subject to changes even in patch releases.

property longreprtext: str

Read-only property that returns the full string representation of longrepr.

Added in version 3.0.

property passed: bool

Whether the outcome is passed.

property skipped: bool

Whether the outcome is skipped.

Config
final class Config
[source]

Access to configuration values, pluginmanager and plugin hooks.

PARAMETERS:

pluginmanager (PytestPluginManager) – A pytest PluginManager.

invocation_params (InvocationParams) – Object containing parameters regarding the pytest.main() invocation.

final class InvocationParams(*, args, plugins, dir)
[source]

Holds parameters passed during pytest.main().

The object attributes are read-only.

Added in version 5.1.

Note

Note that the environment variable PYTEST_ADDOPTS and the addopts configuration option are handled by pytest, not being included in the args attribute.

Plugins accessing InvocationParams must be aware of that.

args: tuple[str, ...]

The command-line arguments as passed to pytest.main().

plugins: Sequence[str | object] | None

Extra plugins, might be None.

dir: Path

The directory from which pytest.main() was invoked.

class ArgsSource(*values)
[source]

Indicates the source of the test arguments.

Added in version 7.2.

ARGS = 1

Command line arguments.

INVOCATION_DIR = 2

Invocation directory.

TESTPATHS = 3

‘testpaths’ configuration value.

option

Access to command line option as attributes.

TYPE:

argparse.Namespace

invocation_params

The parameters with which pytest was invoked.

TYPE:

InvocationParams

pluginmanager

The plugin manager handles plugin registration and hook invocation.

TYPE:

PytestPluginManager

stash

A place where plugins can store information on the config for their own use.

TYPE:

Stash

property rootpath: Path

The path to the rootdir.

Added in version 6.1.

property inipath: Path | None

The path to the configfile.

Added in version 6.1.

add_cleanup(func)
[source]

Add a function to be called when the config object gets out of use (usually coinciding with pytest_unconfigure).

classmethod fromdictargs(option_dict, args)
[source]

Constructor usable for subprocesses.

issue_config_time_warning(warning, stacklevel)
[source]

Issue and handle a warning during the “configure” stage.

During pytest_configure we can’t capture warnings using the catch_warnings_for_item function because it is not possible to have hook wrappers around pytest_configure.

This function is mainly intended for plugins that need to issue warnings during pytest_configure (or similar stages).

PARAMETERS:

warning (Warning) – The warning instance.

stacklevel (int) – stacklevel forwarded to warnings.warn.

addinivalue_line(name, line)
[source]

Add a line to a configuration option. The option must have been declared but might not yet be set in which case the line becomes the first line in its value.

getini(name)
[source]

Return configuration value the an configuration file.

If a configuration value is not defined in a configuration file, then the default value provided while registering the configuration through parser.addini will be returned. Please note that you can even provide None as a valid default value.

If default is not provided while registering using parser.addini, then a default value based on the type parameter passed to parser.addini will be returned. The default values based on type are: paths, pathlist, args and linelist : empty list [] bool : False string : empty string "" int : 0 float : 0.0

If neither the default nor the type parameter is passed while registering the configuration through parser.addini, then the configuration is treated as a string and a default empty string ‘’ is returned.

If the specified name hasn’t been registered through a prior parser.addini call (usually from a plugin), a ValueError is raised.

getoption(name, default=NotSetType.token, skip=False)
[source]

Return command line option value.

PARAMETERS:

name (str) – Name of the option. You may also specify the literal --OPT option instead of the “dest” option name.

default (Any) – Fallback value if no option of that name is declared via pytest_addoption. Note this parameter will be ignored when the option is declared even if the option’s value is None.

skip (bool) – If True, raise pytest.skip() if option is undeclared or has a None value. Note that even if True, if a default was specified it will be returned instead of a skip.

getvalue(name, path=None)
[source]

Deprecated, use getoption() instead.

getvalueorskip(name, path=None)
[source]

Deprecated, use getoption(skip=True) instead.

VERBOSITY_ASSERTIONS: Final = 'assertions'

Verbosity type for failed assertions (see verbosity_assertions).

VERBOSITY_TEST_CASES: Final = 'test_cases'

Verbosity type for test case execution (see verbosity_test_cases).

VERBOSITY_SUBTESTS: Final = 'subtests'

Verbosity type for failed subtests (see verbosity_subtests).

get_verbosity(verbosity_type=None)
[source]

Retrieve the verbosity level for a fine-grained verbosity type.

PARAMETERS:

verbosity_type (str | None) – Verbosity type to get level for. If a level is configured for the given type, that value will be returned. If the given type is not a known verbosity type, the global verbosity level will be returned. If the given type is None (default), the global verbosity level will be returned.

To configure a level for a fine-grained verbosity type, the configuration file should have a setting for the configuration name and a numeric value for the verbosity level. A special value of “auto” can be used to explicitly use the global verbosity level.

Example:

toml
[tool.pytest]
verbosity_assertions = 2

ini
pytest -v

print(config.get_verbosity())  # 1
print(config.get_verbosity(Config.VERBOSITY_ASSERTIONS))  # 2

Dir
final class Dir
[source]

Collector of files in a file system directory.

Added in version 8.0.

Note

Python directories with an __init__.py file are instead collected by Package by default. Both are Directory collectors.

classmethod from_parent(parent, *, path)
[source]

The public constructor.

PARAMETERS:

parent (nodes.Collector) – The parent collector of this Dir.

path (pathlib.Path) – The directory’s path.

collect()
[source]

Collect children (items and collectors) for this collector.

config

The pytest config object.

name

A unique name within the scope of the parent node.

parent

The parent collector node.

path

Filesystem path where this node was collected from.

session

The pytest session this node is part of.

Directory
class Directory
[source]

Base class for collecting files from a directory.

A basic directory collector does the following: goes over the files and sub-directories in the directory and creates collectors for them by calling the hooks pytest_collect_directory and pytest_collect_file, after checking that they are not ignored using pytest_ignore_collect.

The default directory collectors are Dir and Package.

Added in version 8.0.

Using a custom directory collector.

config

The pytest config object.

name

A unique name within the scope of the parent node.

parent

The parent collector node.

path

Filesystem path where this node was collected from.

session

The pytest session this node is part of.

ExceptionInfo
final class ExceptionInfo
[source]

Wraps sys.exc_info() objects and offers help for navigating the traceback.

classmethod from_exception(exception, exprinfo=None)
[source]

Return an ExceptionInfo for an existing exception.

The exception must have a non-None __traceback__ attribute, otherwise this function fails with an assertion error. This means that the exception must have been raised, or added a traceback with the with_traceback() method.

PARAMETERS:

exprinfo (str | None) – A text string helping to determine if we should strip AssertionError from the output. Defaults to the exception message/__str__().

Added in version 7.4.

classmethod from_exc_info(exc_info, exprinfo=None)
[source]

Like from_exception(), but using old-style exc_info tuple.

classmethod from_current(exprinfo=None)
[source]

Return an ExceptionInfo matching the current traceback.

Warning

Experimental API

PARAMETERS:

exprinfo (str | None) – A text string helping to determine if we should strip AssertionError from the output. Defaults to the exception message/__str__().

classmethod for_later()
[source]

Return an unfilled ExceptionInfo.

fill_unfilled(exc_info)
[source]

Fill an unfilled ExceptionInfo created with for_later().

property type: type[E]

The exception class.

property value: E

The exception value.

property tb: TracebackType

The exception raw traceback.

property typename: str

The type name of the exception.

property traceback: Traceback

The traceback.

exconly(tryshort=False)
[source]

Return the exception as a string.

This is usually a single line “<exception type>: <exception str>”, but may also include additional lines for the exception notes, and detailed information for SyntaxError’s.

PARAMETERS:

tryshort (bool) – If true, and the exception is an AssertionError, strip ‘AssertionError: ‘ from the beginning.

errisinstance(exc)
[source]

Return True if the exception is an instance of exc.

Consider using isinstance(excinfo.value, exc) instead.

getrepr(showlocals=False, style='long', abspath=False, tbfilter=True, funcargs=False, truncate_locals=True, truncate_args=True, chain=True)
[source]

Return str()able representation of this exception info.

The formatting parameters are ineffective if style="native", since in this case the native formatting is used.

PARAMETERS:

showlocals (bool) – Show locals per traceback entry.

style (str) – long|short|line|no|native|value traceback style.

abspath (bool) – If paths should be changed to absolute or left unchanged.

tbfilter (bool | Callable[[ExceptionInfo[BaseException]], Traceback]) –

A filter for traceback entries.

If false, don’t hide any entries.

If true, hide internal entries and entries that contain a local variable __tracebackhide__ = True.

If a callable, delegates the filtering to the callable.

funcargs (bool) – Show function arguments per traceback entry.

truncate_locals (bool) – Whether to show a size-limited repr() of locals, or a full pretty-printing.

truncate_args (bool) – Whether to show a size-limited truncated repr() of function arguments, or a full pretty-printing.

chain (bool) – If chained exceptions should be shown.

Changed in version 3.9: Added the chain parameter.

match(regexp)
[source]

Check whether the regular expression regexp matches the string representation of the exception using re.search().

If it matches True is returned, otherwise an AssertionError is raised.

group_contains(expected_exception, *, match=None, depth=None)
[source]

Check whether a captured exception group contains a matching exception.

PARAMETERS:

expected_exception (Type[BaseException] | Tuple[Type[BaseException]]) – The expected exception type, or a tuple if one of multiple possible exception types are expected.

match (str | re.Pattern[str] | None) –

If specified, a string containing a regular expression, or a regular expression object, that is tested against the string representation of the exception and its PEP-678 <https://peps.python.org/pep-0678/> __notes__ using re.search().

To match a literal string that may contain special characters, the pattern can first be escaped with re.escape().

depth (Optional[int]) – If None, will search for a matching exception at any nesting depth. If >= 1, will only match an exception if it’s at the specified depth (depth = 1 being the exceptions contained within the topmost exception group).

Added in version 8.0.

Warning

This helper makes it easy to check for the presence of specific exceptions, but it is very bad for checking that the group does not contain any other exceptions. You should instead consider using pytest.RaisesGroup

ExitCode
class ExitCode(*values)

Encodes the valid exit codes by pytest.

Currently users and plugins may supply other exit codes as well.

Added in version 5.0.

FixtureDef
class FixtureDef
[source]

Bases: Generic[FixtureValue]

A container for a fixture definition.

Note: At this time, only explicitly documented fields and methods are considered public stable API.

property scope: Literal['session', 'package', 'module', 'class', 'function']

Scope string, one of “function”, “class”, “module”, “package”, “session”.

execute(request)
[source]

Return the value of this fixture, executing it if not cached.

MarkDecorator
class MarkDecorator
[source]

A decorator for applying a mark on test functions and classes.

MarkDecorators are created with pytest.mark:

mark1 = pytest.mark.NAME  # Simple MarkDecorator
mark2 = pytest.mark.NAME(name1=value)  # Parametrized MarkDecorator

and can then be applied as decorators to test functions:

@mark2
```python
def test_function():
    pass
```
When a MarkDecorator is called, it does the following:

If called with a single class as its only positional argument and no additional keyword arguments, it attaches the mark to the class so it gets applied automatically to all test cases found in that class.

If called with a single function as its only positional argument and no additional keyword arguments, it attaches the mark to the function, containing all the arguments already stored internally in the MarkDecorator.

When called in any other case, it returns a new MarkDecorator instance with the original MarkDecorator’s content updated with the arguments passed to this call.

Note: The rules above prevent a MarkDecorator from storing only a single function or class reference as its positional argument with no additional keyword or positional arguments. You can work around this by using with_args().

property name: str

Alias for mark.name.

property args: tuple[Any, ...]

Alias for mark.args.

property kwargs: Mapping[str, Any]

Alias for mark.kwargs.

with_args(*args, **kwargs)
[source]

Return a MarkDecorator with extra arguments added.

Unlike calling the MarkDecorator, with_args() can be used even if the sole argument is a callable/class.

MarkGenerator
final class MarkGenerator
[source]

Factory for MarkDecorator objects - exposed as a pytest.mark singleton instance.

Example:

```python
import pytest

@pytest.mark.slowtest
def test_function():
    pass
```
applies a ‘slowtest’ Mark on test_function.

Mark
final class Mark
[source]

A pytest mark.

name: str

Name of the mark.

args: tuple[Any, ...]

Positional arguments of the mark decorator.

kwargs: Mapping[str, Any]

Keyword arguments of the mark decorator.

combined_with(other)
[source]

Return a new Mark which is a combination of this Mark and another Mark.

Combines by appending args and merging kwargs.

PARAMETERS:

other (Mark) – The mark to combine with.

RETURN TYPE:

Mark

Metafunc
final class Metafunc
[source]

Objects passed to the pytest_generate_tests hook.

They help to inspect a test function and to generate tests according to test configuration or values specified in the class or module where a test function is defined.

definition

Access to the underlying _pytest.python.FunctionDefinition.

config

Access to the pytest.Config object for the test session.

module

The module object where the test function is defined in.

function

Underlying Python test function.

fixturenames

Set of fixture names required by the test function.

cls

Class object where the test function is defined in or None.

parametrize(argnames, argvalues, indirect=False, ids=None, scope=None, *, _param_mark=None)
[source]

Add new invocations to the underlying test function using the list of argvalues for the given argnames. Parametrization is performed during the collection phase. If you need to setup expensive resources see about setting indirect to do it at test setup time instead.

Can be called multiple times per test function (but only on different argument names), in which case each call parametrizes all previous parametrizations, e.g.

unparametrized:         t
parametrize ["x", "y"]: t[x], t[y]
parametrize [1, 2]:     t[x-1], t[x-2], t[y-1], t[y-2]

PARAMETERS:

argnames (str | Sequence[str]) – A comma-separated string denoting one or more argument names, or a list/tuple of argument strings.

argvalues (Iterable[ParameterSet | Sequence[object] | object]) –

The list of argvalues determines how often a test is invoked with different argument values.

If only one argname was specified argvalues is a list of values. If N argnames were specified, argvalues must be a list of N-tuples, where each tuple-element specifies a value for its respective argname.

Changed in version 9.1: Passing a non-Collection iterable (such as a generator or iterator) is deprecated. See Non-Collection iterables in @pytest.mark.parametrize for details.

indirect (bool | Sequence[str]) – A list of arguments’ names (subset of argnames) or a boolean. If True the list contains all names from the argnames. Each argvalue corresponding to an argname in this list will be passed as request.param to its respective argname fixture function so that it can perform more expensive setups during the setup phase of a test rather than at collection time.

ids (Iterable[object | None] | Callable[[Any], object | None] | None) –

Sequence of (or generator for) ids for argvalues, or a callable to return part of the id for each argvalue.

With sequences (and generators like itertools.count()) the returned ids should be of type string, int, float, bool, or None. They are mapped to the corresponding index in argvalues. None means to use the auto-generated id.

Added in version 8.4: pytest.HIDDEN_PARAM means to hide the parameter set from the test name. Can only be used at most 1 time, as test names need to be unique.

If it is a callable it will be called for each entry in argvalues, and the return value is used as part of the auto-generated id for the whole set (where parts are joined with dashes (“-“)). This is useful to provide more specific ids for certain items, e.g. dates. Returning None will use an auto-generated id.

If no ids are provided they will be generated automatically from the argvalues.

scope (Literal['session', 'package', 'module', 'class', 'function'] | None) – If specified it denotes the scope of the parameters. The scope is used for grouping tests by parameter instances. It will also override any fixture-function defined scope, allowing to set a dynamic scope using test context or configuration.

Parser
final class Parser
[source]

Parser for command line arguments and config-file values.

VARIABLES:

extra_info – Dict of generic param -> value to display in case there’s an error processing the command line arguments.

getgroup(name, description='', after=None)
[source]

Get (or create) a named option Group.

PARAMETERS:

name (str) – Name of the option group.

description (str) – Long description for –help output.

after (str | None) – Name of another group, used for ordering –help output.

RETURNS:

The option group.

RETURN TYPE:

OptionGroup

The returned group object has an addoption method with the same signature as parser.addoption but will be shown in the respective group in the output of pytest --help.

addoption(*opts, **attrs)
[source]

Register a command line option.

PARAMETERS:

opts (str) – Option names, can be short or long options.

attrs (Any) – Same attributes as the argparse library’s add_argument() function accepts.

After command line parsing, options are available on the pytest config object via config.option.NAME where NAME is usually set by passing a dest attribute, for example addoption("--long", dest="NAME", ...).

parse_known_args(args, namespace=None)
[source]

Parse the known arguments at this point.

RETURNS:

An argparse namespace object.

RETURN TYPE:

Namespace

parse_known_and_unknown_args(args, namespace=None)
[source]

Parse the known arguments at this point, and also return the remaining unknown flag arguments.

RETURNS:

A tuple containing an argparse namespace object for the known arguments, and a list of unknown flag arguments.

RETURN TYPE:

tuple[Namespace, list[str]]

addini(name, help, type=None, default=NotSetType.token, *, aliases=())
[source]

Register a configuration file option.

PARAMETERS:

name (str) – Name of the configuration.

type (Literal['string', 'paths', 'pathlist', 'args', 'linelist', 'bool', 'int', 'float'] | None) –

Type of the configuration. Can be:

string: a string

bool: a boolean

args: a list of strings, separated as in a shell

linelist: a list of strings, separated by line breaks

paths: a list of pathlib.Path, separated as in a shell

pathlist: a list of py.path, separated as in a shell

int: an integer

float: a floating-point number

Added in version 8.4: The float and int types.

For paths and pathlist types, they are considered relative to the config-file. In case the execution is happening without a config-file defined, they will be considered relative to the current working directory (for example with --override-ini).

Added in version 7.0: The paths variable type.

Added in version 8.1: Use the current working directory to resolve paths and pathlist in the absence of a config-file.

Defaults to string if None or not passed.

default (Any) – Default value if no config-file option exists but is queried.

aliases (Sequence[str]) –

Additional names by which this option can be referenced. Aliases resolve to the canonical name.

Added in version 9.0: The aliases parameter.

The value of configuration keys can be retrieved via a call to config.getini(name).

OptionGroup
class OptionGroup
[source]

A group of options shown in its own section.

addoption(*opts, **attrs)
[source]

Add an option to this group.

If a shortened version of a long option is specified, it will be suppressed in the help. addoption('--twowords', '--two-words') results in help showing --two-words only, but --twowords gets accepted and the automatic destination is in args.twowords.

PARAMETERS:

opts (str) – Option names, can be short or long options. Note that lower-case short options (e.g. -x) are reserved.

attrs (Any) – Same attributes as the argparse library’s add_argument() function accepts.

PytestPluginManager
final class PytestPluginManager
[source]

Bases: PluginManager

A pluggy.PluginManager with additional pytest-specific functionality:

Loading plugins from the command line, PYTEST_PLUGINS env variable and pytest_plugins global variables found in plugins being loaded.

conftest.py loading during start-up.

skipped_plugins: list[tuple[str, str]]
rewrite_hook: RewriteHook
register(plugin, name=None)
[source]

Register a plugin and return its name.

PARAMETERS:

name (str | None) – The name under which to register the plugin. If not specified, a name is generated using get_canonical_name().

RETURNS:

The plugin name. If the name is blocked from registering, returns None.

RETURN TYPE:

str | None

If the plugin is already registered, raises a ValueError.

getplugin(name)
[source]
hasplugin(name)
[source]

Return whether a plugin with the given name is registered.

import_plugin(modname, consider_entry_points=False)
[source]

Import a plugin with modname.

If consider_entry_points is True, entry point names are also considered to find a plugin.

add_hookcall_monitoring(before, after)

Add before/after tracing functions for all hooks.

Returns an undo function which, when called, removes the added tracers.

before(hook_name, hook_impls, kwargs) will be called ahead of all hook calls and receive a hookcaller instance, a list of HookImpl instances and the keyword arguments for the hook call.

after(outcome, hook_name, hook_impls, kwargs) receives the same arguments as before but also a Result object which represents the result of the overall hook call.

add_hookspecs(module_or_class)

Add new hook specifications defined in the given module_or_class.

Functions are recognized as hook specifications if they have been decorated with a matching HookspecMarker.

check_pending()

Verify that all hooks which have not been verified against a hook specification are optional, otherwise raise PluginValidationError.

enable_tracing()

Enable tracing of hook calls.

Returns an undo function which, when called, removes the added tracing.

get_canonical_name(plugin)

Return a canonical name for a plugin object.

Note that a plugin may be registered under a different name specified by the caller of register(plugin, name). To obtain the name of a registered plugin use get_name(plugin) instead.

get_hookcallers(plugin)

Get all hook callers for the specified plugin.

RETURNS:

The hook callers, or None if plugin is not registered in this plugin manager.

RETURN TYPE:

list[HookCaller] | None

get_name(plugin)

Return the name the plugin is registered under, or None if is isn’t.

get_plugin(name)

Return the plugin registered under the given name, if any.

get_plugins()

Return a set of all registered plugin objects.

has_plugin(name)

Return whether a plugin with the given name is registered.

is_blocked(name)

Return whether the given plugin name is blocked.

is_registered(plugin)

Return whether the plugin is already registered.

list_name_plugin()

Return a list of (name, plugin) pairs for all registered plugins.

list_plugin_distinfo()

Return a list of (plugin, distinfo) pairs for all setuptools-registered plugins.

load_setuptools_entrypoints(group, name=None)

Load modules from querying the specified setuptools group.

PARAMETERS:

group (str) – Entry point group to load plugins.

name (str | None) – If given, loads only plugins with the given name.

RETURNS:

The number of plugins loaded by this call.

RETURN TYPE:

int

set_blocked(name)

Block registrations of the given name, unregister if already registered.

subset_hook_caller(name, remove_plugins)

Return a proxy HookCaller instance for the named method which manages calls to all registered plugins except the ones from remove_plugins.

unblock(name)

Unblocks a name.

Returns whether the name was actually blocked.

unregister(plugin=None, name=None)

Unregister a plugin and all of its hook implementations.

The plugin can be specified either by the plugin object or the plugin name. If both are specified, they must agree.

Returns the unregistered plugin, or None if not found.

project_name

The project name.

hook

The “hook relay”, used to call a hook on all registered plugins. See Calling hooks.

trace

The tracing entry point. See Built-in tracing.

RaisesExc
final class RaisesExc
[source]

Added in version 8.4.

This is the class constructed when calling pytest.raises(), but may be used directly as a helper class with RaisesGroup when you want to specify requirements on sub-exceptions.

You don’t need this if you only want to specify the type, since RaisesGroup accepts type[BaseException].

PARAMETERS:

expected_exception (type[BaseException] | tuple[type[BaseException]] | None) –

The expected type, or one of several possible types. May be None in order to only make use of match and/or check

The type is checked with isinstance(), and does not need to be an exact match. If that is wanted you can use the check parameter.

match (str | Pattern[str]) – A regex to match.

check (Callable[[BaseException], bool]) – If specified, a callable that will be called with the exception as a parameter after checking the type and the match regex if specified. If it returns True it will be considered a match, if not it will be considered a failed match.

RaisesExc.matches() can also be used standalone to check individual exceptions.

Examples:

with RaisesGroup(RaisesExc(ValueError, match="string"))
    ...
with RaisesGroup(RaisesExc(check=lambda x: x.args == (3, "hello"))):
    ...
with RaisesGroup(RaisesExc(check=lambda x: type(x) is ValueError)):
    ...
fail_reason

Set after a call to matches() to give a human-readable reason for why the match failed. When used as a context manager the string will be printed as the reason for the test failing.

matches(exception)
[source]

Check if an exception matches the requirements of this RaisesExc. If it fails, RaisesExc.fail_reason will be set.

Examples:

assert RaisesExc(ValueError).matches(my_exception):
# is equivalent to
assert isinstance(my_exception, ValueError)

# this can be useful when checking e.g. the ``__cause__`` of an exception.
with pytest.raises(ValueError) as excinfo:
    ...
assert RaisesExc(SyntaxError, match="foo").matches(excinfo.value.__cause__)
# above line is equivalent to
assert isinstance(excinfo.value.__cause__, SyntaxError)
assert re.search("foo", str(excinfo.value.__cause__)

RaisesGroup

Tutorial: Assertions about expected exception groups

final class RaisesGroup
[source]

Added in version 8.4.

Contextmanager for checking for an expected ExceptionGroup. This works similar to pytest.raises(), but allows for specifying the structure of an ExceptionGroup. ExceptionInfo.group_contains() also tries to handle exception groups, but it is very bad at checking that you didn’t get unexpected exceptions.

The catching behaviour differs from except*, being much stricter about the structure by default. By using allow_unwrapped=True and flatten_subgroups=True you can match except* fully when expecting a single exception.

PARAMETERS:

args –

Any number of exception types, RaisesGroup or RaisesExc to specify the exceptions contained in this exception. All specified exceptions must be present in the raised group, and no others.

If you expect a variable number of exceptions you need to use pytest.raises(ExceptionGroup) and manually check the contained exceptions. Consider making use of RaisesExc.matches().

It does not care about the order of the exceptions, so RaisesGroup(ValueError, TypeError) is equivalent to RaisesGroup(TypeError, ValueError).

match (str | re.Pattern[str] | None) –

If specified, a string containing a regular expression, or a regular expression object, that is tested against the string representation of the exception group and its PEP 678 __notes__ using re.search().

To match a literal string that may contain special characters, the pattern can first be escaped with re.escape().

Note that “ (5 subgroups)” will be stripped from the repr before matching.

check (Callable[[E], bool]) – If specified, a callable that will be called with the group as a parameter after successfully matching the expected exceptions. If it returns True it will be considered a match, if not it will be considered a failed match.

allow_unwrapped (bool) –

If expecting a single exception or RaisesExc it will match even if the exception is not inside an exceptiongroup.

Using this together with match, check or expecting multiple exceptions will raise an error.

flatten_subgroups (bool) – “flatten” any groups inside the raised exception group, extracting all exceptions inside any nested groups, before matching. Without this it expects you to fully specify the nesting structure by passing RaisesGroup as expected parameter.

Examples:

with RaisesGroup(ValueError):
    raise ExceptionGroup("", (ValueError(),))
# match
with RaisesGroup(
    ValueError,
    ValueError,
    RaisesExc(TypeError, match="^expected int$"),
    match="^my group$",
):
    raise ExceptionGroup(
        "my group",
        [
            ValueError(),
            TypeError("expected int"),
            ValueError(),
        ],
    )
# check
with RaisesGroup(
    KeyboardInterrupt,
    match="^hello$",
    check=lambda x: isinstance(x.__cause__, ValueError),
):
    raise BaseExceptionGroup("hello", [KeyboardInterrupt()]) from ValueError
# nested groups
with RaisesGroup(RaisesGroup(ValueError)):
    raise ExceptionGroup("", (ExceptionGroup("", (ValueError(),)),))
# flatten_subgroups
with RaisesGroup(ValueError, flatten_subgroups=True):
    raise ExceptionGroup("", (ExceptionGroup("", (ValueError(),)),))
# allow_unwrapped
with RaisesGroup(ValueError, allow_unwrapped=True):
    raise ValueError
RaisesGroup.matches() can also be used directly to check a standalone exception group.

The matching algorithm is greedy, which means cases such as this may fail:

with RaisesGroup(ValueError, RaisesExc(ValueError, match="hello")):
    raise ExceptionGroup("", (ValueError("hello"), ValueError("goodbye")))
even though it generally does not care about the order of the exceptions in the group. To avoid the above you should specify the first ValueError with a RaisesExc as well.

Note

When raised exceptions don’t match the expected ones, you’ll get a detailed error message explaining why. This includes repr(check) if set, which in Python can be overly verbose, showing memory locations etc etc.

If installed and imported (in e.g. conftest.py), the hypothesis library will monkeypatch this output to provide shorter & more readable repr’s.

fail_reason

Set after a call to matches() to give a human-readable reason for why the match failed. When used as a context manager the string will be printed as the reason for the test failing.

matches(exception: BaseException | None) → TypeGuard[ExceptionGroup[ExcT_1]]
[source]
matches(exception: BaseException | None) → TypeGuard[BaseExceptionGroup[BaseExcT_1]]

Check if an exception matches the requirements of this RaisesGroup. If it fails, RaisesGroup.fail_reason will be set.

Example:

with pytest.raises(TypeError) as excinfo:
    ...
assert RaisesGroup(ValueError).matches(excinfo.value.__cause__)
# the above line is equivalent to
myexc = excinfo.value.__cause
assert isinstance(myexc, BaseExceptionGroup)
assert len(myexc.exceptions) == 1
assert isinstance(myexc.exceptions[0], ValueError)

TerminalReporter
final class TerminalReporter(config, file=None)
[source]
wrap_write(content, *, flush=False, margin=8, line_sep='\n', **markup)
[source]

Wrap message with margin for progress info.

rewrite(line, **markup)
[source]

Rewinds the terminal cursor to the beginning and writes the given line.

PARAMETERS:

erase – If True, will also add spaces until the full terminal width to ensure previous lines are properly erased.

The rest of the keyword arguments are markup instructions.

build_summary_stats_line()
[source]

Build the parts used in the last summary stats line.

The summary stats line is the line shown at the end, “=== 12 passed, 2 errors in Xs===”.

This function builds a list of the “parts” that make up for the text in that line, in the example above it would be:

[
    ("12 passed", {"green": True}),
    ("2 errors", {"red": True}
]

That last dict for each line is a “markup dictionary”, used by TerminalWriter to color output.

The final color of the line is also determined by this function, and is the second element of the returned tuple.

TestReport
class TestReport
[source]

Bases: BaseReport

Basic test report object (also used for setup and teardown calls if they fail).

Reports can contain arbitrary extra attributes.

nodeid: str

Normalized collection nodeid.

location: tuple[str, int | None, str]

A (filesystempath, lineno, domaininfo) tuple indicating the actual location of a test item - it might be different from the collected one e.g. if a method is inherited from a different module. The filesystempath may be relative to config.rootdir. The line number is 0-based.

keywords: Mapping[str, Any]

A name -> value dictionary containing all keywords and markers associated with a test invocation.

outcome: Literal['passed', 'failed', 'skipped']

Test outcome, always one of “passed”, “failed”, “skipped”.

longrepr: None | ExceptionInfo[BaseException] | tuple[str, int, str] | str | TerminalRepr

None or a failure representation.

when: Literal['setup', 'call', 'teardown']

One of ‘setup’, ‘call’, ‘teardown’ to indicate runtest phase.

user_properties

User properties is a list of tuples (name, value) that holds user defined properties of the test.

sections: list[tuple[str, str]]

Tuples of str (heading, content) with extra information for the test report. Used by pytest to add text captured from stdout, stderr, and intercepted logging events. May be used by other plugins to add arbitrary information to reports.

duration: float

Time it took to run just the test.

start: float

The system time when the call started, in seconds since the epoch.

stop: float

The system time when the call ended, in seconds since the epoch.

classmethod from_item_and_call(item, call)
[source]

Create and fill a TestReport with standard item and call info.

PARAMETERS:

item (Item) – The item.

call (CallInfo[None]) – The call info.

property caplog: str

Return captured log lines, if log capturing is enabled.

Added in version 3.5.

property capstderr: str

Return captured text from stderr, if capturing is enabled.

Added in version 3.0.

property capstdout: str

Return captured text from stdout, if capturing is enabled.

Added in version 3.0.

property count_towards_summary: bool

Experimental Whether this report should be counted towards the totals shown at the end of the test session: “1 passed, 1 failure, etc”.

Note

This function is considered experimental, so beware that it is subject to changes even in patch releases.

property failed: bool

Whether the outcome is failed.

property fspath: str

The path portion of the reported node, as a string.

property head_line: str | None

Experimental The head line shown with longrepr output for this report, more commonly during traceback representation during failures:

________ Test.foo ________

In the example above, the head_line is “Test.foo”.

Note

This function is considered experimental, so beware that it is subject to changes even in patch releases.

property longreprtext: str

Read-only property that returns the full string representation of longrepr.

Added in version 3.0.

property passed: bool

Whether the outcome is passed.

property skipped: bool

Whether the outcome is skipped.

TestShortLogReport
class TestShortLogReport
[source]

Used to store the test status result category, shortletter and verbose word. For example "rerun", "R", ("RERUN", {"yellow": True}).

VARIABLES:

category – The class of result, for example “passed”, “skipped”, “error”, or the empty string.

letter – The short letter shown as testing progresses, for example ".", "s", "E", or the empty string.

word – Verbose word is shown as testing progresses in verbose mode, for example "PASSED", "SKIPPED", "ERROR", or the empty string.

category: str

Alias for field number 0

letter: str

Alias for field number 1

word: str | tuple[str, Mapping[str, bool]]

Alias for field number 2

Result

Result object used within hook wrappers, see Result in the pluggy documentation for more information.

Stash
class Stash
[source]

Stash is a type-safe heterogeneous mutable mapping that allows keys and value types to be defined separately from where it (the Stash) is created.

Usually you will be given an object which has a Stash, for example Config or a Node:

stash: Stash = some_object.stash

If a module or plugin wants to store data in this Stash, it creates StashKeys for its keys (at the module level):

# At the top-level of the module
some_str_key = StashKey[str]()
some_bool_key = StashKey[bool]()

To store information:

# Value type must match the key.
stash[some_str_key] = "value"
stash[some_bool_key] = True

To retrieve the information:

# The static type of some_str is str.
some_str = stash[some_str_key]
# The static type of some_bool is bool.
some_bool = stash[some_bool_key]

Added in version 7.0.

__setitem__(key, value)
[source]

Set a value for key.

__getitem__(key)
[source]

Get the value for key.

Raises KeyError if the key wasn’t set before.

get(key, default)
[source]

Get the value for key, or return default if the key wasn’t set before.

setdefault(key, default)
[source]

Return the value of key if already set, otherwise set the value of key to default and return default.

__delitem__(key)
[source]

Delete the value for key.

Raises KeyError if the key wasn’t set before.

__contains__(key)
[source]

Return whether key was set.

__len__()
[source]

Return how many items exist in the stash.

class StashKey
[source]

Bases: Generic[T]

StashKey is an object used as a key to a Stash.

A StashKey is associated with the type T of the value of the key.

A StashKey is unique and cannot conflict with another key.

Added in version 7.0.

Global Variables

pytest treats some global variables in a special manner when defined in a test module or conftest.py files.

collect_ignore

Tutorial: Customizing test collection

Can be declared in conftest.py files to exclude test directories or modules. Needs to be a list of paths (str, pathlib.Path or any os.PathLike).

collect_ignore = ["setup.py"]

collect_ignore_glob

Tutorial: Customizing test collection

Can be declared in conftest.py files to exclude test directories or modules with Unix shell-style wildcards. Needs to be list[str] where str can contain glob patterns.

collect_ignore_glob = ["*_ignore.py"]

pytest_plugins

Tutorial: Requiring/Loading plugins in a test module or conftest file

Can be declared at the global level in test modules and conftest.py files to register additional plugins. Can be either a str or Sequence[str].

pytest_plugins = "myapp.testsupport.myplugin"

pytest_plugins = ("myapp.testsupport.tools", "myapp.testsupport.regression")

pytestmark

Tutorial: Marking whole classes or modules

Can be declared at the global level in test modules to apply one or more marks to all test functions and methods. Can be either a single mark or a list of marks (applied in left-to-right order).

import pytest
pytestmark = pytest.mark.webtest

import pytest
pytestmark = [pytest.mark.integration, pytest.mark.slow]

Environment Variables

Environment variables that can be used to change pytest’s behavior.

CI

When set to a non-empty value, pytest acknowledges that it is running in a CI process. See also CI Pipelines.

BUILD_NUMBER

When set to a non-empty value, pytest acknowledges that it is running in a CI process. Alternative to CI. See also CI Pipelines.

PYTEST_ADDOPTS

This contains a command-line (parsed by the py:mod:shlex module) that will be prepended to the command line given by the user, see Builtin configuration file options for more information.

PYTEST_VERSION

This environment variable is defined at the start of the pytest session and is undefined afterwards. It contains the value of pytest.__version__, and among other things can be used to easily check if a code is running from within a pytest run.

PYTEST_CURRENT_TEST

This is not meant to be set by users, but is set by pytest internally with the name of the current test so other processes can inspect it, see PYTEST_CURRENT_TEST environment variable for more information.

PYTEST_DEBUG

When set, pytest will print tracing and debug information.

PYTEST_DEBUG_TEMPROOT

Root for temporary directories produced by fixtures like tmp_path as discussed in Temporary directory location and retention.

PYTEST_DISABLE_PLUGIN_AUTOLOAD

When set, disables plugin auto-loading through entry point packaging metadata. Only plugins explicitly specified in PYTEST_PLUGINS or with -p will be loaded. See also –disable-plugin-autoload.

PYTEST_PLUGINS

Contains comma-separated list of modules that should be loaded as plugins:

export PYTEST_PLUGINS=mymodule.plugin,xdist

See also -p.

PYTEST_THEME

Sets a pygment style to use for the code output.

PYTEST_THEME_MODE

Sets the PYTEST_THEME to be either dark or light.

PY_COLORS

When set to 1, pytest will use color in terminal output. When set to 0, pytest will not use color. PY_COLORS takes precedence over NO_COLOR and FORCE_COLOR.

NO_COLOR

When set to a non-empty string (regardless of value), pytest will not use color in terminal output. PY_COLORS takes precedence over NO_COLOR, which takes precedence over FORCE_COLOR. See no-color.org for other libraries supporting this community standard.

FORCE_COLOR

When set to a non-empty string (regardless of value), pytest will use color in terminal output. PY_COLORS and NO_COLOR take precedence over FORCE_COLOR.

Exceptions
