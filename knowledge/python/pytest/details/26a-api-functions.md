---
title: "Functions, Constants, and Classes"
source: "https://docs.pytest.org/en/stable/reference/reference.html"
version: "stable"
---

# Functions, Constants, and Classes

> 原始文档来源：https://docs.pytest.org/en/stable/reference/reference.html (API Reference 节选)

---

API Reference

This page contains the full reference to pytest’s API.

Constants
pytest.__version__
The current pytest version, as a string:

```python
>>> import pytest
>>> pytest.__version__
```
'9.0.2'

pytest.HIDDEN_PARAM
Added in version 8.4.

Can be passed to ids of Metafunc.parametrize or to id of pytest.param() to hide a parameter set from the test name. Can only be used at most 1 time, as test names need to be unique.

pytest.version_tuple
Added in version 7.0.

The current pytest version, as a tuple:

```python
>>> import pytest
>>> pytest.version_tuple
```
(7, 0, 0)

For pre-releases, the last component will be a string with the prerelease version:

```python
>>> import pytest
>>> pytest.version_tuple
```
(7, 0, '0rc1')

Functions
pytest.approx
approx(expected, rel=None, abs=None, nan_ok=False)
[source]

Assert that two numbers (or two ordered sequences of numbers) are equal to each other within some tolerance.

Due to the Floating-Point Arithmetic: Issues and Limitations, numbers that we would intuitively expect to be equal are not always so:

```python
>>> 0.1 + 0.2 == 0.3
```
False

This problem is commonly encountered when writing tests, e.g. when making sure that floating-point values are what you expect them to be. One way to deal with this problem is to assert that two floating-point numbers are equal to within some appropriate tolerance:

```python
>>> abs((0.1 + 0.2) - 0.3) < 1e-6
```
True

However, comparisons like this are tedious to write and difficult to understand. Furthermore, absolute comparisons like the one above are usually discouraged because there’s no tolerance that works well for all situations. 1e-6 is good for numbers around 1, but too small for very big numbers and too big for very small ones. It’s better to express the tolerance as a fraction of the expected value, but relative comparisons like that are even more difficult to write correctly and concisely.

The approx class performs floating-point comparisons using a syntax that’s as intuitive as possible:

```python
>>> from pytest import approx
>>> 0.1 + 0.2 == approx(0.3)
```
True

The same syntax also works for ordered sequences of numbers:

```python
>>> (0.1 + 0.2, 0.2 + 0.4) == approx((0.3, 0.6))
```
True

numpy arrays:

```python
>>> import numpy as np
>>> np.array([0.1, 0.2]) + np.array([0.2, 0.4]) == approx(np.array([0.3, 0.6]))
```
True

And for a numpy array against a scalar:

```python
>>> import numpy as np
>>> np.array([0.1, 0.2]) + np.array([0.2, 0.1]) == approx(0.3)
```
True

Only ordered sequences are supported, because approx needs to infer the relative position of the sequences without ambiguity. This means sets and other unordered sequences are not supported.

Finally, dictionary values can also be compared:

```python
>>> {'a': 0.1 + 0.2, 'b': 0.2 + 0.4} == approx({'a': 0.3, 'b': 0.6})
```
True

The comparison will be true if both mappings have the same keys and their respective values match the expected tolerances.

Tolerances

By default, approx considers numbers within a relative tolerance of 1e-6 (i.e. one part in a million) of its expected value to be equal. This treatment would lead to surprising results if the expected value was 0.0, because nothing but 0.0 itself is relatively close to 0.0. To handle this case less surprisingly, approx also considers numbers within an absolute tolerance of 1e-12 of its expected value to be equal. Infinity and NaN are special cases. Infinity is only considered equal to itself, regardless of the relative tolerance. NaN is not considered equal to anything by default, but you can make it be equal to itself by setting the nan_ok argument to True. (This is meant to facilitate comparing arrays that use NaN to mean “no data”.)

Both the relative and absolute tolerances can be changed by passing arguments to the approx constructor:

```python
>>> 1.0001 == approx(1)
```
False
```python
>>> 1.0001 == approx(1, rel=1e-3)
```
True
```python
>>> 1.0001 == approx(1, abs=1e-3)
```
True

If you specify abs but not rel, the comparison will not consider the relative tolerance at all. In other words, two numbers that are within the default relative tolerance of 1e-6 will still be considered unequal if they exceed the specified absolute tolerance. If you specify both abs and rel, the numbers will be considered equal if either tolerance is met:

```python
>>> 1 + 1e-8 == approx(1)
```
True
```python
>>> 1 + 1e-8 == approx(1, abs=1e-12)
```
False
```python
>>> 1 + 1e-8 == approx(1, rel=1e-6, abs=1e-12)
```
True

Non-numeric types

You can also use approx to compare non-numeric types, or dicts and sequences containing non-numeric types, in which case it falls back to strict equality. This can be useful for comparing dicts and sequences that can contain optional values:

```python
>>> {"required": 1.0000005, "optional": None} == approx({"required": 1, "optional": None})
```
True
```python
>>> [None, 1.0000005] == approx([None,1])
```
True
```python
>>> ["foo", 1.0000005] == approx([None,1])
```
False

datetime and timedelta

You can also use approx to compare datetime and timedelta objects by specifying an absolute tolerance as a timedelta:

```python
>>> from datetime import datetime, timedelta
>>> dt1 = datetime(2024, 1, 1, 12, 0, 0)
>>> dt2 = datetime(2024, 1, 1, 12, 0, 0, 500000)
>>> dt1 == approx(dt2, abs=timedelta(seconds=1))
```
True

Note that rel is not supported for datetime comparisons. For timedelta comparisons, rel is a number (not a timedelta) that represents a relative tolerance – a fraction of the expected value. abs must be a timedelta object in both cases.

Added in version 8.4.

If you’re thinking about using approx, then you might want to know how it compares to other good ways of comparing floating-point numbers. All of these algorithms are based on relative and absolute tolerances and should agree for the most part, but they do have meaningful differences:

math.isclose(a, b, rel_tol=1e-9, abs_tol=0.0): True if the relative tolerance is met w.r.t. either a or b or if the absolute tolerance is met. Because the relative tolerance is calculated w.r.t. both a and b, this test is symmetric (i.e. neither a nor b is a “reference value”). You have to specify an absolute tolerance if you want to compare to 0.0 because there is no tolerance by default. More information: math.isclose().

numpy.isclose(a, b, rtol=1e-5, atol=1e-8): True if the difference between a and b is less that the sum of the relative tolerance w.r.t. b and the absolute tolerance. Because the relative tolerance is only calculated w.r.t. b, this test is asymmetric and you can think of b as the reference value. Support for comparing sequences is provided by numpy.allclose(). More information: numpy.isclose.

unittest.TestCase.assertAlmostEqual(a, b): True if a and b are within an absolute tolerance of 1e-7. No relative tolerance is considered , so this function is not appropriate for very large or very small numbers. Also, it’s only available in subclasses of unittest.TestCase and it’s ugly because it doesn’t follow PEP8. More information: unittest.TestCase.assertAlmostEqual().

a == pytest.approx(b, rel=1e-6, abs=1e-12): True if the relative tolerance is met w.r.t. b or if the absolute tolerance is met. Because the relative tolerance is only calculated w.r.t. b, this test is asymmetric and you can think of b as the reference value. In the special case that you explicitly specify an absolute tolerance but not a relative tolerance, only the absolute tolerance is considered.

Note

approx can handle numpy arrays, but we recommend the specialised test helpers in Test support if you need support for comparisons, NaNs, or ULP-based tolerances.

To match strings using regex, you can use Matches from the re_assert package.

Note

Unlike built-in equality, this function considers booleans unequal to numeric zero or one. For example:

```python
>>> 1 == approx(True)
```
False

Warning

Changed in version 3.2.

In order to avoid inconsistent behavior, TypeError is raised for >, >=, < and <= comparisons. The example below illustrates the problem:

assert approx(0.1) > 0.1 + 1e-10  # calls approx(0.1).__gt__(0.1 + 1e-10)
assert 0.1 + 1e-10 > approx(0.1)  # calls approx(0.1).__lt__(0.1 + 1e-10)

In the second example one expects approx(0.1).__le__(0.1 + 1e-10) to be called. But instead, approx(0.1).__lt__(0.1 + 1e-10) is used to comparison. This is because the call hierarchy of rich comparisons follows a fixed behavior. More information: object.__ge__()

Changed in version 3.7.1: approx raises TypeError when it encounters a dict value or sequence element of non-numeric type.

Changed in version 6.1.0: approx falls back to strict equality for non-numeric types instead of raising TypeError.

pytest.fail
Tutorial: How to use skip and xfail to deal with tests that cannot succeed

fail(reason[, pytrace=True])

Explicitly fail an executing test with the given message.

PARAMETERS:

reason – The message to show the user as reason for the failure.

pytrace – If False, msg represents the full failure information and no python traceback will be reported.

RAISES:

```python
pytest.fail.Exception – The exception that is raised.

class pytest.fail.Exception
```
The exception raised by pytest.fail().

pytest.skip
skip(reason[, allow_module_level=False])

Skip an executing test with the given message.

This function should be called only during testing (setup, call or teardown) or during collection by using the allow_module_level flag. This function can be called in doctests as well.

PARAMETERS:

reason – The message to show the user as reason for the skip.

allow_module_level –

Allows this function to be called at module level. Raising the skip exception at module level will stop the execution of the module and prevent the collection of all tests in the module, even those defined before the skip call.

Defaults to False.

RAISES:

pytest.skip.Exception – The exception that is raised.
Note

It is better to use the pytest.mark.skipif marker when possible to declare a test to be skipped under certain conditions like mismatching platforms or dependencies. Similarly, use the # doctest: +SKIP directive (see doctest.SKIP) to skip a doctest statically.

class pytest.skip.Exception

The exception raised by pytest.skip().

pytest.importorskip
importorskip(modname, minversion=None, reason=None, *, exc_type=None)
[source]

Import and return the requested module modname, or skip the current test if the module cannot be imported.

PARAMETERS:

modname – The name of the module to import.

minversion – If given, the imported module’s __version__ attribute must be at least this minimal version, otherwise the test is still skipped.

reason – If given, this reason is shown as the message when the module cannot be imported.

exc_type –

The exception that should be captured in order to skip modules. Must be ImportError or a subclass.

Defaults to ModuleNotFoundError when not given, which means the module must be missing for the test to be skipped. Pass exc_type=ImportError to also skip modules that raise ImportError during import.

See pytest.importorskip default behavior regarding ImportError for details.

RETURNS:

The imported module. This should be assigned to its canonical name.

RAISES:

pytest.skip.Exception – If the module cannot be imported.
Example:

docutils = pytest.importorskip("docutils")

Added in version 8.2: The exc_type parameter.

Changed in version 9.1: The default for exc_type is now ModuleNotFoundError.

pytest.xfail
xfail(reason='')

Imperatively xfail an executing test or setup function with the given reason.

This function should be called only during testing (setup, call or teardown).

No other code is executed after using xfail() (it is implemented internally by raising an exception).

PARAMETERS:

reason – The message to show the user as reason for the xfail.

Note

It is better to use the pytest.mark.xfail marker when possible to declare a test to be xfailed under certain conditions like known bugs or missing features.

RAISES:

```python
pytest.xfail.Exception – The exception that is raised.

class pytest.xfail.Exception
```
The exception raised by pytest.xfail().

pytest.exit
exit(reason[, returncode=None])

Exit testing process.

PARAMETERS:

reason – The message to show as the reason for exiting pytest. reason has a default value only because msg is deprecated.

returncode – Return code to be used when exiting pytest. None means the same as 0 (no error), same as sys.exit().

RAISES:

```python
pytest.exit.Exception – The exception that is raised.

class pytest.exit.Exception
```
The exception raised by pytest.exit().

pytest.main
Tutorial: Calling pytest from Python code

main(args=None, plugins=None)
[source]

Perform an in-process test run.

PARAMETERS:

args – List of command line arguments. If None or not given, defaults to reading arguments directly from the process command line (sys.argv).

plugins – List of plugin objects to be auto-registered during initialization.

RETURNS:

An exit code.

pytest.param
param(*values[, id][, marks])
[source]

Specify a parameter in pytest.mark.parametrize calls or parametrized fixtures.

```python
@pytest.mark.parametrize(
    "test_input,expected",
    [
        ("3+5", 8),
        pytest.param("6*9", 42, marks=pytest.mark.xfail),
    ],
```
)
```python
def test_eval(test_input, expected):
    assert eval(test_input) == expected
```
PARAMETERS:

values – Variable args of the values of the parameter set, in order.

marks –

A single mark or a list of marks to be applied to this parameter set.

pytest.mark.usefixtures cannot be added via this parameter.
id (str | Literal[pytest.HIDDEN_PARAM] | None) –

The id to attribute to this parameter set.

Added in version 8.4: pytest.HIDDEN_PARAM means to hide the parameter set from the test name. Can only be used at most 1 time, as test names need to be unique.

pytest.raises
Tutorial: Assertions about expected exceptions

with raises(expected_exception: type[E] | tuple[type[E], ...], *, match: str | Pattern[str] | None = ..., check: Callable[[E], bool] = ...) → RaisesExc[E] as excinfo
[source]
with raises(*, match: str | Pattern[str], check: Callable[[BaseException], bool] = ...) → RaisesExc[BaseException] as excinfo
with raises(*, check: Callable[[BaseException], bool]) → RaisesExc[BaseException] as excinfo
with raises(expected_exception: type[E] | tuple[type[E], ...], func: Callable[P, object], *args: P.args, **kwargs: P.kwargs) → ExceptionInfo[E] as excinfo

Assert that a code block/function call raises an exception type, or one of its subclasses.

PARAMETERS:

expected_exception –

The expected exception type, or a tuple if one of multiple possible exception types are expected. Note that subclasses of the passed exceptions will also match.

This is not a required parameter, you may opt to only use match and/or check for verifying the raised exception.

match (str | re.Pattern[str] | None) –

If specified, a string containing a regular expression, or a regular expression object, that is tested against the string representation of the exception and its PEP 678 __notes__ using re.search().

To match a literal string that may contain special characters, the pattern can first be escaped with re.escape().

(This is only used when pytest.raises is used as a context manager, and passed through to the function otherwise. When using pytest.raises as a function, you can use: pytest.raises(Exc, func, match="passed on").match("my pattern").)

check (Callable[[BaseException], bool]) –

Added in version 8.4.

If specified, a callable that will be called with the exception as a parameter after checking the type and the match regex if specified. If it returns True it will be considered a match, if not it will be considered a failed match.

Use pytest.raises as a context manager, which will capture the exception of the given type, or any of its subclasses:

```python
>>> import pytest
>>> with pytest.raises(ZeroDivisionError):
...    1/0
```

If the code block does not raise the expected exception (ZeroDivisionError in the example above), or no exception at all, the check will fail instead.

You can also use the keyword argument match to assert that the exception matches a text or regex:

```python
>>> with pytest.raises(ValueError, match='must be 0 or None'):
...     raise ValueError("value must be 0 or None")

>>> with pytest.raises(ValueError, match=r'must be \d+$'):
...     raise ValueError("value must be 42")
```

The match argument searches the formatted exception string, which includes any PEP-678 __notes__:

```python
>>> with pytest.raises(ValueError, match=r"had a note added"):
...     e = ValueError("value must be 42")
...     e.add_note("had a note added")
...     raise e
```

The check argument, if provided, must return True when passed the raised exception for the match to be successful, otherwise an AssertionError is raised.

```python
>>> import errno
>>> with pytest.raises(OSError, check=lambda e: e.errno == errno.EACCES):
...     raise OSError(errno.EACCES, "no permission to view")
```

The context manager produces an ExceptionInfo object which can be used to inspect the details of the captured exception:

```python
>>> with pytest.raises(ValueError) as exc_info:
...     raise ValueError("value must be 42")
>>> assert exc_info.type is ValueError
>>> assert exc_info.value.args[0] == "value must be 42"
```

Warning

Given that pytest.raises matches subclasses, be wary of using it to match Exception like this:

# Careful, this will catch ANY exception raised.
with pytest.raises(Exception):
    some_function()
Because Exception is the base class of almost all exceptions, it is easy for this to hide real bugs, where the user wrote this expecting a specific exception, but some other exception is being raised due to a bug introduced during a refactoring.

Avoid using pytest.raises to catch Exception unless certain that you really want to catch any exception raised.

Note

When using pytest.raises as a context manager, it’s worthwhile to note that normal context manager rules apply and that the exception raised must be the final line in the scope of the context manager. Lines of code after that, within the scope of the context manager will not be executed. For example:

```python
>>> value = 15
>>> with pytest.raises(ValueError) as exc_info:
...     if value > 10:
...         raise ValueError("value must be <= 10")
...     assert exc_info.type is ValueError  # This will not execute.
```

Instead, the following approach must be taken (note the difference in scope):

```python
>>> with pytest.raises(ValueError) as exc_info:
...     if value > 10:
...         raise ValueError("value must be <= 10")
```
...
```python
>>> assert exc_info.type is ValueError
```

Expecting exception groups

When expecting exceptions wrapped in BaseExceptionGroup or ExceptionGroup, you should instead use pytest.RaisesGroup.

Using with pytest.mark.parametrize

When using pytest.mark.parametrize it is possible to parametrize tests such that some runs raise an exception and others do not.

See Parametrizing conditional raising for an example.

See also

Assertions about expected exceptions for more examples and detailed discussion.

Note

Similar to caught exception objects in Python, explicitly clearing local references to returned ExceptionInfo objects can help the Python interpreter speed up its garbage collection.

Clearing those references breaks a reference cycle (ExceptionInfo –> caught exception –> frame stack raising the exception –> current frame stack –> local variables –> ExceptionInfo) which makes Python keep all objects referenced from that cycle (including all local variables in the current frame) alive until the next cyclic garbage collection run. More detailed information can be found in the official Python documentation for the try statement.

pytest.deprecated_call
Tutorial: Ensuring code triggers a deprecation warning

with deprecated_call(*, match: str | Pattern[str] | None = ...) → WarningsRecorder
[source]
with deprecated_call(func: Callable[P, T], *args: P.args, **kwargs: P.kwargs) → T

Assert that code produces a DeprecationWarning or PendingDeprecationWarning or FutureWarning.

This function can be used as a context manager:

```python
>>> import warnings
>>> def api_call_v2():
...     warnings.warn('use v3 of this api', DeprecationWarning)
...     return 200

>>> import pytest
>>> with pytest.deprecated_call():
...    assert api_call_v2() == 200
>>> with pytest.deprecated_call(match="^use v3 of this api$") as warning_messages:
...    assert api_call_v2() == 200
```

You may use the keyword argument match to assert that the warning matches a text or regex.

The return value is a list of warnings.WarningMessage objects, one for each warning emitted (regardless of whether it is an expected_warning or not).

pytest.register_assert_rewrite
Tutorial: Assertion Rewriting

register_assert_rewrite(*names)
[source]

Register one or more module names to be rewritten on import.

This function will make sure that this module or all modules inside the package will get their assert statements rewritten. Thus you should make sure to call this before the module is actually imported, usually in your __init__.py if you are a plugin using a package.

PARAMETERS:

names – The module names to register.

pytest.register_fixture
register_fixture(*, name, func, node, scope='function', params=None, ids=None, autouse=False)
[source]

Register a fixture imperatively.

This is an advanced function intended for use by plugins.

Normally, fixtures should be registered declaratively using the @pytest.fixture decorator. Pytest looks for these fixture definitions during the collection phase and registers them automatically. For some plugin usecases the declarative interface can be cumbersome or nonviable, in which case the imperative interface can be used.

Fixture registration is expected to happen during the collection phase, and this is the only sanctioned use. However, to allow for more creative uses, this is not enforced. But do so at your own risk!

PARAMETERS:

name – The fixture’s name.

func – The fixture’s implementation function.

node –

The visibility of the fixture.

Only items that are descendents of this node in the collection tree will be able to request this fixture. You can think of this as the place where you would put the @pytest.fixture.

For global visibility, pass the session node, which is the root of the collection tree.

scope – The fixture’s scope.

params – The fixture’s parametrization params.

ids – The fixture’s IDs.

autouse – Whether this is an autouse fixture.

pytest.warns
Tutorial: Asserting warnings with the warns function

with warns(expected_warning: type[Warning] | tuple[type[Warning], ...] = <class 'Warning'>, *, match: str | ~re.Pattern[str] | None = ...) → WarningsChecker
[source]
with warns(expected_warning: type[Warning] | tuple[type[Warning], ...], func: Callable[P, T], *args: P.args, **kwargs: P.kwargs) → T

Assert that code raises a particular class of warning.

Specifically, the parameter expected_warning can be a warning class or tuple of warning classes, and the code inside the with block must issue at least one warning of that class or classes.

This helper produces a list of warnings.WarningMessage objects, one for each warning emitted (regardless of whether it is an expected_warning or not). Since pytest 8.0, unmatched warnings are also re-emitted when the context closes.

This function should be used as a context manager:

```python
>>> import pytest
>>> with pytest.warns(RuntimeWarning):
...    warnings.warn("my warning", RuntimeWarning)
```

The match keyword argument can be used to assert that the warning matches a text or regex:

```python
>>> with pytest.warns(UserWarning, match='must be 0 or None'):
...     warnings.warn("value must be 0 or None", UserWarning)

>>> with pytest.warns(UserWarning, match=r'must be \d+$'):
...     warnings.warn("value must be 42", UserWarning)

>>> with pytest.warns(UserWarning):  # catch re-emitted warning
...     with pytest.warns(UserWarning, match=r'must be \d+$'):
...         warnings.warn("this is not here", UserWarning)
```
Traceback (most recent call last):
  ...
Failed: Regex pattern did not match any of the 1 warnings emitted.
 Regex: ...
 Emitted warnings: ...UserWarning...

Using with pytest.mark.parametrize

When using pytest.mark.parametrize it is possible to parametrize tests such that some runs raise a warning and others do not.

This could be achieved in the same way as with exceptions, see Parametrizing conditional raising for an example.

pytest.freeze_includes
Tutorial: Freezing pytest

freeze_includes()
[source]

Return a list of module names used by pytest that should be included by cx_freeze.

Marks
