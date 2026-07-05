---
title: "Hooks Reference"
source: "https://docs.pytest.org/en/stable/reference/reference.html"
version: "stable"
---

# Hooks Reference

> 原始文档来源：https://docs.pytest.org/en/stable/reference/reference.html (API Reference 节选)

---

Tutorial: Writing plugins

Reference to all hooks which can be implemented by conftest.py files and plugins.

```python
@pytest.hookimpl
@pytest.hookimpl
```
pytest’s decorator for marking functions as hook implementations.

See Writing hook functions and pluggy.HookimplMarker().

```python
@pytest.hookspec
@pytest.hookspec
```
pytest’s decorator for marking functions as hook specifications.

See Declaring new hooks and pluggy.HookspecMarker().

Bootstrapping hooks

Bootstrapping hooks called for plugins registered early enough (internal and third-party plugins).

pytest_load_initial_conftests(early_config, parser, args)
[source]

Called to implement the loading of initial conftest files ahead of command line option parsing.

PARAMETERS:

early_config – The pytest config object.

args – Arguments passed on the command line.

parser – To add command line options.

Use in conftest plugins

This hook is not called for conftest files.

pytest_cmdline_parse(pluginmanager, args)
[source]

Return an initialized Config, parsing the specified args.

Stops at first non-None result, see firstresult: stop at first non-None result.

Note

This hook is only called for plugin classes passed to the plugins arg when using pytest.main to perform an in-process test run.

PARAMETERS:

pluginmanager – The pytest plugin manager.

args – List of arguments passed on the command line.

RETURNS:

A pytest config object.

Use in conftest plugins

This hook is not called for conftest files.

pytest_cmdline_main(config)
[source]

Called for performing the main command line action.

The default implementation will invoke the configure hooks and pytest_runtestloop.

Stops at first non-None result, see firstresult: stop at first non-None result.

PARAMETERS:

config – The pytest config object.

RETURNS:

The exit code.

Use in conftest plugins

This hook is only called for initial conftests.

Initialization hooks

Initialization hooks called for plugins and conftest.py files.

pytest_addoption(parser, pluginmanager)
[source]

Register argparse-style options and config-style config values, called once at the beginning of a test run.

PARAMETERS:

parser – To add command line options, call parser.addoption(...). To add config-file values call parser.addini(...).

pluginmanager – The pytest plugin manager, which can be used to install hookspec()’s or hookimpl()’s and allow one plugin to call another plugin’s hooks to change how command line options are added.

Options can later be accessed through the config object, respectively:

config.getoption(name) to retrieve the value of a command line option.

config.getini(name) to retrieve a value read from a configuration file.

The config object is passed around on many internal objects via the .config attribute or can be retrieved as the pytestconfig fixture.

Note

This hook is incompatible with hook wrappers.

Use in conftest plugins

If a conftest plugin implements this hook, it will be called immediately when the conftest is registered.

This hook is only called for initial conftests.

pytest_addhooks(pluginmanager)
[source]

Called at plugin registration time to allow adding new hooks via a call to pluginmanager.add_hookspecs(module_or_class, prefix).

PARAMETERS:

pluginmanager – The pytest plugin manager.

Note

This hook is incompatible with hook wrappers.

Use in conftest plugins

If a conftest plugin implements this hook, it will be called immediately when the conftest is registered.

pytest_configure(config)
[source]

Allow plugins and conftest files to perform initial configuration.

Note

This hook is incompatible with hook wrappers.

PARAMETERS:

config – The pytest config object.

Use in conftest plugins

This hook is called for every initial conftest file after command line options have been parsed. After that, the hook is called for other conftest files as they are registered.

pytest_unconfigure(config)
[source]

Called before test process is exited.

PARAMETERS:

config – The pytest config object.

Use in conftest plugins

Any conftest file can implement this hook.

pytest_sessionstart(session)
[source]

Called after the Session object has been created and before performing collection and entering the run test loop.

PARAMETERS:

session – The pytest session object.

Use in conftest plugins

This hook is only called for initial conftests.

pytest_sessionfinish(session, exitstatus)
[source]

Called after whole test run finished, right before returning the exit status to the system.

PARAMETERS:

session – The pytest session object.

exitstatus – The status which pytest will return to the system.

Use in conftest plugins

Any conftest file can implement this hook.

pytest_plugin_registered(plugin, plugin_name, manager)
[source]

A new pytest plugin got registered.

PARAMETERS:

plugin – The plugin module or instance.

plugin_name – The name by which the plugin is registered.

manager – The pytest plugin manager.

Note

This hook is incompatible with hook wrappers.

Use in conftest plugins

If a conftest plugin implements this hook, it will be called immediately when the conftest is registered, once for each plugin registered thus far (including itself!), and for all plugins thereafter when they are registered.

Collection hooks

pytest calls the following hooks for collecting files and directories:

pytest_collection(session)
[source]

Perform the collection phase for the given session.

Stops at first non-None result, see firstresult: stop at first non-None result. The return value is not used, but only stops further processing.

The default collection phase is this (see individual hooks for full details):

Starting from session as the initial collector:

pytest_collectstart(collector)

report = pytest_make_collect_report(collector)

pytest_exception_interact(collector, call, report) if an interactive exception occurred

For each collected node:

If an item, pytest_itemcollected(item)

If a collector, recurse into it.

pytest_collectreport(report)

pytest_collection_modifyitems(session, config, items)

pytest_deselected(items) for any deselected items (may be called multiple times)

Set session.items to the list of collected items

pytest_collection_finish(session)

Set session.testscollected to the number of collected items

You can implement this hook to only perform some action before collection, for example the terminal plugin uses it to start displaying the collection counter (and returns None).

PARAMETERS:

session – The pytest session object.

Use in conftest plugins

This hook is only called for initial conftests.

pytest_ignore_collect(collection_path, config)
[source]

Return True to ignore this path for collection.

Return None to let other plugins ignore the path for collection.

Returning False will forcefully not ignore this path for collection, without giving a chance for other plugins to ignore this path.

This hook is consulted for all files and directories prior to calling more specific hooks.

Stops at first non-None result, see firstresult: stop at first non-None result.

PARAMETERS:

collection_path (pathlib.Path) – The path to analyze.

config – The pytest config object.

Changed in version 7.0.0: The collection_path parameter was added as a pathlib.Path equivalent of the path parameter. The path parameter has been deprecated and removed in pytest 9.0.0.

Use in conftest plugins

Any conftest file can implement this hook. For a given collection path, only conftest files in parent directories of the collection path are consulted (if the path is a directory, its own conftest file is not consulted - a directory cannot ignore itself!).

pytest_collect_directory(path, parent)
[source]

Create a Collector for the given directory, or None if not relevant.

Added in version 8.0.

For best results, the returned collector should be a subclass of Directory, but this is not required.

The new node needs to have the specified parent as a parent.

Stops at first non-None result, see firstresult: stop at first non-None result.

PARAMETERS:

path (pathlib.Path) – The path to analyze.

See Using a custom directory collector for a simple example of use of this hook.

Use in conftest plugins

Any conftest file can implement this hook. For a given collection path, only conftest files in parent directories of the collection path are consulted (if the path is a directory, its own conftest file is not consulted - a directory cannot collect itself!).

pytest_collect_file(file_path, parent)
[source]

Create a Collector for the given path, or None if not relevant.

For best results, the returned collector should be a subclass of File, but this is not required.

The new node needs to have the specified parent as a parent.

PARAMETERS:

file_path (pathlib.Path) – The path to analyze.

Changed in version 7.0.0: The file_path parameter was added as a pathlib.Path equivalent of the path parameter. The path parameter has been deprecated and removed in pytest 9.0.0.

Use in conftest plugins

Any conftest file can implement this hook. For a given file path, only conftest files in parent directories of the file path are consulted.

pytest_pycollect_makemodule(module_path, parent)
[source]

Return a pytest.Module collector or None for the given path.

This hook will be called for each matching test module path. The pytest_collect_file hook needs to be used if you want to create test modules for files that do not match as a test module.

Stops at first non-None result, see firstresult: stop at first non-None result.

PARAMETERS:

module_path (pathlib.Path) – The path of the module to collect.

Changed in version 7.0.0: The module_path parameter was added as a pathlib.Path equivalent of the path parameter. The path parameter has been deprecated in favor of module_path and removed in pytest 9.0.0.

Use in conftest plugins

Any conftest file can implement this hook. For a given parent collector, only conftest files in the collector’s directory and its parent directories are consulted.

For influencing the collection of objects in Python modules you can use the following hook:

pytest_pycollect_makeitem(collector, name, obj)
[source]

Return a custom item/collector for a Python object in a module, or None.

Stops at first non-None result, see firstresult: stop at first non-None result.

PARAMETERS:

collector – The module/class collector.

name – The name of the object in the module/class.

obj – The object.

RETURNS:

The created items/collectors.

Use in conftest plugins

Any conftest file can implement this hook. For a given collector, only conftest files in the collector’s directory and its parent directories are consulted.

pytest_generate_tests(metafunc)
[source]

Generate (multiple) parametrized calls to a test function.

PARAMETERS:

metafunc – The Metafunc helper for the test function.

Use in conftest plugins

Any conftest file can implement this hook. For a given function definition, only conftest files in the functions’s directory and its parent directories are consulted.

pytest_make_parametrize_id(config, val, argname)
[source]

Return a user-friendly string representation of the given val that will be used by @pytest.mark.parametrize calls, or None if the hook doesn’t know about val.

The parameter name is available as argname, if required.

Stops at first non-None result, see firstresult: stop at first non-None result.

PARAMETERS:

config – The pytest config object.

val – The parametrized value.

argname – The automatic parameter name produced by pytest.

Use in conftest plugins

Any conftest file can implement this hook.

Hooks for influencing test skipping:

pytest_markeval_namespace(config)
[source]

Called when constructing the globals dictionary used for evaluating string conditions in xfail/skipif markers.

This is useful when the condition for a marker requires objects that are expensive or impossible to obtain during collection time, which is required by normal boolean conditions.

Added in version 6.2.

PARAMETERS:

config – The pytest config object.

RETURNS:

A dictionary of additional globals to add.

Use in conftest plugins

Any conftest file can implement this hook. For a given item, only conftest files in parent directories of the item are consulted.

After collection is complete, you can modify the order of items, delete or otherwise amend the test items:

pytest_collection_modifyitems(session, config, items)
[source]

Called after collection has been performed. May filter or re-order the items in-place.

When items are deselected (filtered out from items), the hook pytest_deselected must be called explicitly with the deselected items to properly notify other plugins, e.g. with config.hook.pytest_deselected(items=deselected_items).

PARAMETERS:

session – The pytest session object.

config – The pytest config object.

items – List of item objects.

Use in conftest plugins

Any conftest plugin can implement this hook.

Note

If this hook is implemented in conftest.py files, it always receives all collected items, not only those under the conftest.py where it is implemented.

pytest_collection_finish(session)
[source]

Called after collection has been performed and modified.

PARAMETERS:

session – The pytest session object.

Use in conftest plugins

Any conftest plugin can implement this hook.

Test running (runtest) hooks

All runtest related hooks receive a pytest.Item object.

pytest_runtestloop(session)
[source]

Perform the main runtest loop (after collection finished).

The default hook implementation performs the runtest protocol for all items collected in the session (session.items), unless the collection failed or the collectonly pytest option is set.

If at any point pytest.exit() is called, the loop is terminated immediately.

If at any point session.shouldfail or session.shouldstop are set, the loop is terminated after the runtest protocol for the current item is finished.

PARAMETERS:

session – The pytest session object.

Stops at first non-None result, see firstresult: stop at first non-None result. The return value is not used, but only stops further processing.

Use in conftest plugins

Any conftest file can implement this hook.

pytest_runtest_protocol(item, nextitem)
[source]

Perform the runtest protocol for a single test item.

The default runtest protocol is this (see individual hooks for full details):

pytest_runtest_logstart(nodeid, location)

Setup phase:

call = pytest_runtest_setup(item) (wrapped in CallInfo(when="setup"))

report = pytest_runtest_makereport(item, call)

pytest_runtest_logreport(report)

pytest_exception_interact(call, report) if an interactive exception occurred

Call phase, if the setup passed and the setuponly pytest option is not set:

call = pytest_runtest_call(item) (wrapped in CallInfo(when="call"))

report = pytest_runtest_makereport(item, call)

pytest_runtest_logreport(report)

pytest_exception_interact(call, report) if an interactive exception occurred

Teardown phase:

call = pytest_runtest_teardown(item, nextitem) (wrapped in CallInfo(when="teardown"))

report = pytest_runtest_makereport(item, call)

pytest_runtest_logreport(report)

pytest_exception_interact(call, report) if an interactive exception occurred

pytest_runtest_logfinish(nodeid, location)

PARAMETERS:

item – Test item for which the runtest protocol is performed.

nextitem – The scheduled-to-be-next test item (or None if this is the end my friend).

Stops at first non-None result, see firstresult: stop at first non-None result. The return value is not used, but only stops further processing.

Use in conftest plugins

Any conftest file can implement this hook.

pytest_runtest_logstart(nodeid, location)
[source]

Called at the start of running the runtest protocol for a single item.

See pytest_runtest_protocol for a description of the runtest protocol.

PARAMETERS:

nodeid – Full node ID of the item.

location – A tuple of (filename, lineno, testname) where filename is a file path relative to config.rootpath and lineno is 0-based.

Use in conftest plugins

Any conftest file can implement this hook. For a given item, only conftest files in the item’s directory and its parent directories are consulted.

pytest_runtest_logfinish(nodeid, location)
[source]

Called at the end of running the runtest protocol for a single item.

See pytest_runtest_protocol for a description of the runtest protocol.

PARAMETERS:

nodeid – Full node ID of the item.

location – A tuple of (filename, lineno, testname) where filename is a file path relative to config.rootpath and lineno is 0-based.

Use in conftest plugins

Any conftest file can implement this hook. For a given item, only conftest files in the item’s directory and its parent directories are consulted.

pytest_runtest_setup(item)
[source]

Called to perform the setup phase for a test item.

The default implementation runs setup() on item and all of its parents (which haven’t been setup yet). This includes obtaining the values of fixtures required by the item (which haven’t been obtained yet).

PARAMETERS:

item – The item.

Use in conftest plugins

Any conftest file can implement this hook. For a given item, only conftest files in the item’s directory and its parent directories are consulted.

pytest_runtest_call(item)
[source]

Called to run the test for test item (the call phase).

The default implementation calls item.runtest().

PARAMETERS:

item – The item.

Use in conftest plugins

Any conftest file can implement this hook. For a given item, only conftest files in the item’s directory and its parent directories are consulted.

pytest_runtest_teardown(item, nextitem)
[source]

Called to perform the teardown phase for a test item.

The default implementation runs the finalizers and calls teardown() on item and all of its parents (which need to be torn down). This includes running the teardown phase of fixtures required by the item (if they go out of scope).

PARAMETERS:

item – The item.

nextitem – The scheduled-to-be-next test item (None if no further test item is scheduled). This argument is used to perform exact teardowns, i.e. calling just enough finalizers so that nextitem only needs to call setup functions.

Use in conftest plugins

Any conftest file can implement this hook. For a given item, only conftest files in the item’s directory and its parent directories are consulted.

pytest_runtest_makereport(item, call)
[source]

Called to create a TestReport for each of the setup, call and teardown runtest phases of a test item.

See pytest_runtest_protocol for a description of the runtest protocol.

PARAMETERS:

item – The item.

call – The CallInfo for the phase.

Stops at first non-None result, see firstresult: stop at first non-None result.

Use in conftest plugins

Any conftest file can implement this hook. For a given item, only conftest files in the item’s directory and its parent directories are consulted.

For deeper understanding you may look at the default implementation of these hooks in _pytest.runner and maybe also in _pytest.pdb which interacts with _pytest.capture and its input/output capturing in order to immediately drop into interactive debugging when a test failure occurs.

pytest_pyfunc_call(pyfuncitem)
[source]

Call underlying test function.

Stops at first non-None result, see firstresult: stop at first non-None result.

PARAMETERS:

pyfuncitem – The function item.

Use in conftest plugins

Any conftest file can implement this hook. For a given item, only conftest files in the item’s directory and its parent directories are consulted.

Reporting hooks

Session related reporting hooks:

pytest_collectstart(collector)
[source]

Collector starts collecting.

PARAMETERS:

collector – The collector.

Use in conftest plugins

Any conftest file can implement this hook. For a given collector, only conftest files in the collector’s directory and its parent directories are consulted.

pytest_make_collect_report(collector)
[source]

Perform collector.collect() and return a CollectReport.

Stops at first non-None result, see firstresult: stop at first non-None result.

PARAMETERS:

collector – The collector.

Use in conftest plugins

Any conftest file can implement this hook. For a given collector, only conftest files in the collector’s directory and its parent directories are consulted.

pytest_itemcollected(item)
[source]

We just collected a test item.

PARAMETERS:

item – The item.

Use in conftest plugins

Any conftest file can implement this hook. For a given item, only conftest files in the item’s directory and its parent directories are consulted.

pytest_collectreport(report)
[source]

Collector finished collecting.

PARAMETERS:

report – The collect report.

Use in conftest plugins

Any conftest file can implement this hook. For a given collector, only conftest files in the collector’s directory and its parent directories are consulted.

pytest_deselected(items)
[source]

Called for deselected test items, e.g. by keyword.

Note that this hook has two integration aspects for plugins:

it can be implemented to be notified of deselected items

it must be called from pytest_collection_modifyitems implementations when items are deselected (to properly notify other plugins).

May be called multiple times.

PARAMETERS:

items – The items.

Use in conftest plugins

Any conftest file can implement this hook.

pytest_report_header(config, start_path)
[source]

Return a string or list of strings to be displayed as header info for terminal reporting.

PARAMETERS:

config – The pytest config object.

start_path (pathlib.Path) – The starting dir.

Note

Lines returned by a plugin are displayed before those of plugins which ran before it. If you want to have your line(s) displayed first, use trylast=True.

Changed in version 7.0.0: The start_path parameter was added as a pathlib.Path equivalent of the startdir parameter. The startdir parameter has been deprecated and removed in pytest 9.0.0.

Use in conftest plugins

This hook is only called for initial conftests.

pytest_report_collectionfinish(config, start_path, items)
[source]

Return a string or list of strings to be displayed after collection has finished successfully.

These strings will be displayed after the standard “collected X items” message.

Added in version 3.2.

PARAMETERS:

config – The pytest config object.

start_path (pathlib.Path) – The starting dir.

items – List of pytest items that are going to be executed; this list should not be modified.

Note

Lines returned by a plugin are displayed before those of plugins which ran before it. If you want to have your line(s) displayed first, use trylast=True.

Changed in version 7.0.0: The start_path parameter was added as a pathlib.Path equivalent of the startdir parameter. The startdir parameter has been deprecated and removed in pytest 9.0.0.

Use in conftest plugins

Any conftest plugin can implement this hook.

pytest_report_teststatus(report, config)
[source]

Return result-category, shortletter and verbose word for status reporting.

The result-category is a category in which to count the result, for example “passed”, “skipped”, “error” or the empty string.

The shortletter is shown as testing progresses, for example “.”, “s”, “E” or the empty string.

The verbose word is shown as testing progresses in verbose mode, for example “PASSED”, “SKIPPED”, “ERROR” or the empty string.

pytest may style these implicitly according to the report outcome. To provide explicit styling, return a tuple for the verbose word, for example "rerun", "R", ("RERUN", {"yellow": True}).

PARAMETERS:

report – The report object whose status is to be returned.

config – The pytest config object.

RETURNS:

The test status.

Stops at first non-None result, see firstresult: stop at first non-None result.

Use in conftest plugins

Any conftest plugin can implement this hook.

pytest_report_to_serializable(config, report)
[source]

Serialize the given report object into a data structure suitable for sending over the wire, e.g. converted to JSON.

PARAMETERS:

config – The pytest config object.

report – The report.

Use in conftest plugins

Any conftest file can implement this hook. The exact details may depend on the plugin which calls the hook.

pytest_report_from_serializable(config, data)
[source]

Restore a report object previously serialized with pytest_report_to_serializable.

PARAMETERS:

config – The pytest config object.

Use in conftest plugins

Any conftest file can implement this hook. The exact details may depend on the plugin which calls the hook.

pytest_terminal_summary(terminalreporter, exitstatus, config)
[source]

Add a section to terminal summary reporting.

PARAMETERS:

terminalreporter – The internal terminal reporter object.

exitstatus – The exit status that will be reported back to the OS.

config – The pytest config object.

Added in version 4.2: The config parameter.

Use in conftest plugins

Any conftest plugin can implement this hook.

pytest_fixture_setup(fixturedef, request)
[source]

Perform fixture setup execution.

PARAMETERS:

fixturedef – The fixture definition object.

request – The fixture request object.

RETURNS:

The return value of the call to the fixture function.

Stops at first non-None result, see firstresult: stop at first non-None result.

Note

If the fixture function returns None, other implementations of this hook function will continue to be called, according to the behavior of the firstresult: stop at first non-None result option.

Use in conftest plugins

Any conftest file can implement this hook. For a given fixture, only conftest files in the fixture scope’s directory and its parent directories are consulted.

pytest_fixture_post_finalizer(fixturedef, request)
[source]

Called after fixture teardown, but before the cache is cleared, so the fixture result fixturedef.cached_result is still available (not None).

PARAMETERS:

fixturedef – The fixture definition object.

request – The fixture request object.

Use in conftest plugins

Any conftest file can implement this hook. For a given fixture, only conftest files in the fixture scope’s directory and its parent directories are consulted.

pytest_warning_recorded(warning_message, when, nodeid, location)
[source]

Process a warning captured by the internal pytest warnings plugin.

PARAMETERS:

warning_message – The captured warning. This is the same object produced by warnings.catch_warnings, and contains the same attributes as the parameters of warnings.showwarning().

when –

Indicates when the warning was captured. Possible values:

"config": during pytest configuration/initialization stage.

"collect": during test collection.

"runtest": during test execution.

nodeid – Full id of the item. Empty string for warnings that are not specific to a particular node.

location – When available, holds information about the execution context of the captured warning (filename, linenumber, function). function evaluates to <module> when the execution context is at the module level.

Added in version 6.0.

Use in conftest plugins

Any conftest file can implement this hook. If the warning is specific to a particular node, only conftest files in parent directories of the node are consulted.

Central hook for reporting about test execution:

pytest_runtest_logreport(report)
[source]

Process the TestReport produced for each of the setup, call and teardown runtest phases of an item.

See pytest_runtest_protocol for a description of the runtest protocol.

Use in conftest plugins

Any conftest file can implement this hook. For a given item, only conftest files in the item’s directory and its parent directories are consulted.

Assertion related hooks:

pytest_assertrepr_compare(config, op, left, right)
[source]

Return explanation for comparisons in failing assert expressions.

Return None for no custom explanation, otherwise return a list of strings. The strings will be joined by newlines but any newlines in a string will be escaped. Note that all but the first line will be indented slightly, the intention is for the first line to be a summary.

PARAMETERS:

config – The pytest config object.

op – The operator, e.g. "==", "!=", "not in".

left – The left operand.

right – The right operand.

Use in conftest plugins

Any conftest file can implement this hook. For a given item, only conftest files in the item’s directory and its parent directories are consulted.

pytest_assertion_pass(item, lineno, orig, expl)
[source]

Called whenever an assertion passes.

Added in version 5.0.

Use this hook to do some processing after a passing assertion. The original assertion information is available in the orig string and the pytest introspected assertion information is available in the expl string.

This hook must be explicitly enabled by the enable_assertion_pass_hook configuration option:

toml
[pytest]
enable_assertion_pass_hook = true

ini

You need to clean the .pyc files in your project directory and interpreter libraries when enabling this option, as assertions will require to be re-written.

PARAMETERS:

item – pytest item object of current test.

lineno – Line number of the assert statement.

orig – String with the original assertion.

expl – String with the assert explanation.

Use in conftest plugins

Any conftest file can implement this hook. For a given item, only conftest files in the item’s directory and its parent directories are consulted.

Debugging/Interaction hooks

There are few hooks which can be used for special reporting or interaction with exceptions:

pytest_internalerror(excrepr, excinfo)
[source]

Called for internal errors.

Return True to suppress the fallback handling of printing an INTERNALERROR message directly to sys.stderr.

PARAMETERS:

excrepr – The exception repr object.

excinfo – The exception info.

Use in conftest plugins

Any conftest plugin can implement this hook.

pytest_keyboard_interrupt(excinfo)
[source]

Called for keyboard interrupt.

PARAMETERS:

excinfo – The exception info.

Use in conftest plugins

Any conftest plugin can implement this hook.

pytest_exception_interact(node, call, report)
[source]

Called when an exception was raised which can potentially be interactively handled.

May be called during collection (see pytest_make_collect_report), in which case report is a CollectReport.

May be called during runtest of an item (see pytest_runtest_protocol), in which case report is a TestReport.

This hook is not called if the exception that was raised is an internal exception like skip.Exception.

PARAMETERS:

node – The item or collector.

call – The call information. Contains the exception.

report – The collection or test report.

Use in conftest plugins

Any conftest file can implement this hook. For a given node, only conftest files in parent directories of the node are consulted.

pytest_enter_pdb(config, pdb)
[source]

Called upon pdb.set_trace().

Can be used by plugins to take special action just before the python debugger enters interactive mode.

PARAMETERS:

config – The pytest config object.

pdb – The Pdb instance.

Use in conftest plugins

Any conftest plugin can implement this hook.

pytest_leave_pdb(config, pdb)
[source]

Called when leaving pdb (e.g. with continue after pdb.set_trace()).

Can be used by plugins to take special action just after the python debugger leaves interactive mode.

PARAMETERS:

config – The pytest config object.

pdb – The Pdb instance.

Use in conftest plugins

Any conftest plugin can implement this hook.

Collection tree objects

These are the collector and item classes (collectively called “nodes”) which make up the collection tree.

Node
class Node
[source]

Bases: ABC

Base class of Collector and Item, the components of the test collection tree.

Collector's are the internal nodes of the tree, and Item's are the leaf nodes.

fspath: LEGACY_PATH

A LEGACY_PATH copy of the path attribute. Intended for usage for methods not migrated to pathlib.Path yet, such as Item.reportinfo. Will be deprecated in a future release, prefer using path instead.

name: str

A unique name within the scope of the parent node.

parent

The parent collector node.

config: Config

The pytest config object.

session: Session

The pytest session this node is part of.

path: pathlib.Path

Filesystem path where this node was collected from.

keywords: MutableMapping[str, Any]

Keywords/markers collected from all scopes.

own_markers: list[Mark]

The marker objects belonging to this node.

extra_keyword_matches: set[str]

Allow adding of extra keywords to use for matching.

stash: Stash

A place where plugins can store information on the node for their own use.

classmethod from_parent(parent, **kw)
[source]

Public constructor for Nodes.

This indirection got introduced in order to enable removing the fragile logic from the node constructors.

Subclasses can use super().from_parent(...) when overriding the construction.

PARAMETERS:

parent (Node) – The parent node of this Node.

property ihook: HookRelay

Path-sensitive hook proxy used to call pytest hooks.

warn(warning)
[source]

Issue a warning for this Node.

Warnings will be displayed after the test session, unless explicitly suppressed.

PARAMETERS:

warning (Warning) – The warning instance to issue.

RAISES:

ValueError – If warning instance is not a subclass of Warning.

Example usage:

node.warn(PytestWarning("some message"))
node.warn(UserWarning("some message"))

Changed in version 6.2: Any subclass of Warning is now accepted, rather than only PytestWarning subclasses.

property nodeid: str

A ::-separated string denoting its collection tree address.

iter_parents()
[source]

Iterate over all parent collectors starting from and including self up to the root of the collection tree.

Added in version 8.1.

listchain()
[source]

Return a list of all parent collectors starting from the root of the collection tree down to and including self.

add_marker(marker, append=True)
[source]

Dynamically add a marker object to the node.

PARAMETERS:

marker (str | MarkDecorator) – The marker.

append (bool) – Whether to append the marker, or prepend it.

iter_markers(name=None)
[source]

Iterate over all markers of the node.

PARAMETERS:

name (str | None) – If given, filter the results by the name attribute.

RETURNS:

An iterator of the markers of the node.

RETURN TYPE:

Iterator[Mark]

iter_markers_with_node(name=None)
[source]

Iterate over all markers of the node.

PARAMETERS:

name (str | None) – If given, filter the results by the name attribute.

RETURNS:

An iterator of (node, mark) tuples.

RETURN TYPE:

Iterator[tuple[Node, Mark]]

get_closest_marker(name: str) → Mark | None
[source]
get_closest_marker(name: str, default: Mark) → Mark

Return the first marker matching the name, from closest (for example function) to farther level (for example module level).

PARAMETERS:

default (Mark | None) – Fallback return value if no marker was found.

name (str) – Name to filter by.

listextrakeywords()
[source]

Return a set of all extra keywords in self and any parents.

addfinalizer(fin)
[source]

Register a function to be called without arguments when this node is finalized.

This method can only be called when this node is active in a setup chain, for example during self.setup().

getparent(cls)
[source]

Get the closest parent node (including self) which is an instance of the given class.

PARAMETERS:

cls (type[_NodeType]) – The node class to search for.

RETURNS:

The node, if found.

RETURN TYPE:

_NodeType | None

repr_failure(excinfo, style=None)
[source]

Return a representation of a collection or test failure.

See also

Working with non-python tests

PARAMETERS:

excinfo (ExceptionInfo[BaseException]) – Exception information for the failure.

Collector
class Collector
[source]

Bases: Node, ABC

Base class of all collectors.

Collector create children through collect() and thus iteratively build the collection tree.

exception CollectError
[source]

Bases: Exception

An error during collection, contains a custom message.

abstractmethod collect()
[source]

Collect children (items and collectors) for this collector.

repr_failure(excinfo)
[source]

Return a representation of a collection failure.

PARAMETERS:

excinfo (ExceptionInfo[BaseException]) – Exception information for the failure.

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

Item
class Item
[source]

Bases: Node, ABC

Base class of all test invocation items.

Note that for a single function there might be multiple test invocation items.

user_properties: list[tuple[str, object]]

A list of tuples (name, value) that holds user defined properties for this test.

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

abstractmethod runtest()
[source]

Run the test case for this item.

Must be implemented by subclasses.

See also

Working with non-python tests

add_report_section(when, key, content)
[source]

Add a new report section, similar to what’s done internally to add stdout and stderr captured output:

item.add_report_section("call", "stdout", "report section contents")

PARAMETERS:

when (str) – One of the possible capture states, "setup", "call", "teardown".

key (str) – Name of the section, can be customized at will. Pytest uses "stdout" and "stderr" internally.

content (str) – The full contents as a string.

reportinfo()
[source]

Get location information for this item for test reports.

Returns a tuple with three elements:

The path of the test (default self.path)

The 0-based line number of the test (default None)

A name of the test to be shown (default "")

See also

Working with non-python tests

property location: tuple[str, int | None, str]
[source]

Returns a tuple of (relfspath, lineno, testname) for this item where relfspath is file path relative to config.rootpath and lineno is a 0-based line number.

File
class File
[source]

Bases: FSCollector, ABC

Base class for collecting tests from a file.

Working with non-python tests.

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

FSCollector
class FSCollector
[source]

Bases: Collector, ABC

Base class for filesystem collectors.

path

Filesystem path where this node was collected from.

classmethod from_parent(parent, *, fspath=None, path=None, **kw)
[source]

The public constructor.

config

The pytest config object.

name

A unique name within the scope of the parent node.

parent

The parent collector node.

session

The pytest session this node is part of.

Session
final class Session
[source]

Bases: Collector

The root of the collection tree.

Session collects the initial paths given as arguments to pytest.

exception Interrupted

Bases: KeyboardInterrupt

Signals that the test run was interrupted.

exception Failed

Bases: Exception

Signals a stop as failed test run.

property startpath: Path

The path from which pytest was invoked.

Added in version 7.0.0.

isinitpath(path, *, with_parents=False)
[source]

Is path an initial path?

An initial path is a path explicitly given to pytest on the command line.

PARAMETERS:

with_parents (bool) – If set, also return True if the path is a parent of an initial path.

Changed in version 8.0: Added the with_parents parameter.

perform_collect(args: Sequence[str] | None = None, genitems: Literal[True] = True) → Sequence[Item]
[source]
perform_collect(args: Sequence[str] | None = None, genitems: bool = True) → Sequence[Item | Collector]

Perform the collection phase for this session.

This is called by the default pytest_collection hook implementation; see the documentation of this hook for more details. For testing purposes, it may also be called directly on a fresh Session.

This function normally recursively expands any collectors collected from the session to their items, and only items are returned. For testing purposes, this may be suppressed by passing genitems=False, in which case the return value contains these collectors unexpanded, and session.items is empty.

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

Package
class Package
[source]

Bases: Directory

Collector for files and directories in a Python packages – directories with an __init__.py file.

Note

Directories without an __init__.py file are instead collected by Dir by default. Both are Directory collectors.

Changed in version 8.0: Now inherits from Directory.

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

Module
class Module
[source]

Bases: File, PyCollector

Collector for test classes and functions in a Python module.

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

Class
class Class
[source]

Bases: PyCollector

Collector for test methods (and nested classes) in a Python class.

classmethod from_parent(parent, *, name, obj=None, **kw)
[source]

The public constructor.

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

Function
class Function
[source]

Bases: PyobjMixin, Item

Item responsible for setting up and executing a Python test function.

PARAMETERS:

name – The full function name, including any decorations like those added by parametrization (my_func[my_param]).

parent – The parent Node.

config – The pytest Config object.

callspec – If given, this function has been parametrized and the callspec contains meta information about the parametrization.

callobj – If given, the object which will be called when the Function is invoked, otherwise the callobj will be obtained from parent using originalname.

keywords – Keywords bound to the function object for “-k” matching.

session – The pytest Session object.

fixtureinfo – Fixture information already resolved at this fixture node..

originalname – The attribute name to use for accessing the underlying function object. Defaults to name. Set this if name is different from the original name, for example when it contains decorations like those added by parametrization (my_func[my_param]).

originalname

Original function name, without any decorations (for example parametrization adds a "[...]" suffix to function names), used to access the underlying function object from parent (in case callobj is not given explicitly).

Added in version 3.0.

classmethod from_parent(parent, **kw)
[source]

The public constructor.

property function

Underlying python ‘function’ object.

property instance

Python instance object the function is bound to.

Returns None if not a test method, e.g. for a standalone test function, a class or a module.

runtest()
[source]

Execute the underlying test function.

repr_failure(excinfo)
[source]

Return a representation of a collection or test failure.

See also

Working with non-python tests

PARAMETERS:

excinfo (ExceptionInfo[BaseException]) – Exception information for the failure.

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

FunctionDefinition
class FunctionDefinition
[source]

Bases: Function

This class is a stop gap solution until we evolve to have actual function definition nodes and manage to get rid of metafunc.

runtest()
[source]

Execute the underlying test function.

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

setup()

Execute the underlying test function.

Objects
