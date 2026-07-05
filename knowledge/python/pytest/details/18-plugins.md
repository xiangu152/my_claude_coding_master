---
title: "Using Plugins"
source: "https://docs.pytest.org/en/stable/how-to/plugins.html"
version: "stable"
---

# Using Plugins

> 原始文档来源：https://docs.pytest.org/en/stable/how-to/plugins.html

---

How to install and use plugins

This section talks about installing and using third party plugins. For writing your own plugins, please refer to Writing plugins.

Installing a third party plugin can be easily done with pip:

```bash
pip install pytest-NAME
```
pip uninstall pytest-NAME

If a plugin is installed, pytest automatically finds and integrates it, there is no need to activate it.

Here is a little annotated list for some popular plugins:

pytest-django: write tests for django apps, using pytest integration.

pytest-twisted: write tests for twisted apps, starting a reactor and processing deferreds from test functions.

pytest-cov: coverage reporting, compatible with distributed testing

pytest-xdist: to distribute tests to CPUs and remote hosts, to run in boxed mode that allows pytest to survive segmentation faults, to run in looponfailing mode, automatically re-running failing tests on file changes.

pytest-instafail: to report failures while the test run is happening.

pytest-bdd: to write tests using behaviour-driven testing.

pytest-timeout: to timeout tests based on function marks or global definitions.

pytest-pep8: a --pep8 option to enable PEP8 compliance checking.

pytest-flakes: check source code with pyflakes.

allure-pytest: report test results via allure-framework.

To see a complete list of all plugins with their latest testing status against different pytest and Python versions, please visit Pytest Plugin List.

You may also discover more plugins through a pytest- pypi.org search.

Requiring/Loading plugins in a test module or conftest file

You can require plugins in a test module or a conftest file using pytest_plugins:

pytest_plugins = ("myapp.testsupport.myplugin",)

When the test module or conftest plugin is loaded the specified plugins will be loaded as well.

Note

Requiring plugins using a pytest_plugins variable in non-root conftest.py files is deprecated. See full explanation in the Writing plugins section.

Note

The name pytest_plugins is reserved and should not be used as a name for a custom plugin module.

Finding out which plugins are active

If you want to find out which plugins are active in your environment you can type:

pytest --trace-config

and will get an extended test header which shows activated plugins and their names. It will also print local plugins aka conftest.py files when they are loaded.

Deactivating / unregistering a plugin by name

You can prevent plugins from loading or unregister them:

pytest -p no:NAME

This means that any subsequent try to activate/load the named plugin will not work.

If you want to unconditionally disable a plugin for a project, you can add this option to your configuration file:

toml
```toml
[pytest]
```
addopts = ["-p", "no:NAME"]

ini

Alternatively to disable it only in certain environments (for example in a CI server), you can set PYTEST_ADDOPTS environment variable to -p no:name.

See Finding out which plugins are active for how to obtain the name of a plugin.

Disabling plugins from autoloading

If you want to disable plugins from loading automatically, instead of requiring you to manually specify each plugin with -p or PYTEST_PLUGINS, you can use --disable-plugin-autoload or PYTEST_DISABLE_PLUGIN_AUTOLOAD.

export PYTEST_DISABLE_PLUGIN_AUTOLOAD=1
export PYTEST_PLUGINS=NAME,NAME2
pytest

pytest --disable-plugin-autoload -p NAME -p NAME2

toml
```toml
[pytest]
```
addopts = ["--disable-plugin-autoload", "-p", "NAME", "-p", "NAME2"]

ini

Added in version 8.4: The --disable-plugin-autoload command-line flag.

Note

-p and PYTEST_PLUGINS are both ways to explicitly control which plugins are loaded, but they serve slightly different use-cases.

-p loads (or disables with -p no:<name>) a plugin by name or entry point for a specific pytest invocation, and is processed early during startup.

PYTEST_PLUGINS is a comma-separated list of Python modules that are imported and registered as plugins during startup. This mechanism is commonly used by test suites, for example when testing a plugin.

When explicitly controlling plugin loading (especially with PYTEST_DISABLE_PLUGIN_AUTOLOAD or --disable-plugin-autoload), avoid specifying the same plugin via multiple mechanisms. Registering the same plugin more than once can lead to errors during plugin registration.

Examples:

# Disable auto-loading and load only specific plugins for this invocation
PYTEST_DISABLE_PLUGIN_AUTOLOAD=1 pytest -p xdist

# Disable auto-loading and load plugin modules during startup
PYTEST_DISABLE_PLUGIN_AUTOLOAD=1 PYTEST_PLUGINS=mymodule.plugin,xdist pytest
