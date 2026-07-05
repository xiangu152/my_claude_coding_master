---
title: "Experimental"

# Experimental
> 原始文档来源：https://pydantic.dev/docs/validation/latest/concepts/experimental/

---
Experimental

In this section you will find documentation for new, experimental features in Pydantic. These features are subject to change or removal, and we are looking for feedback and suggestions before making them a permanent part of Pydantic.

See our Version Policy for more information on experimental features.

Feedback

We welcome feedback on experimental features! Please open an issue on the Pydantic GitHub repository to share your thoughts, requests, or suggestions.

We also encourage you to read through existing feedback and add your thoughts to existing issues.

Pipeline API

Pydantic v2.8.0 introduced an experimental “pipeline” API that allows composing of parsing (validation), constraints and transformations in a more type-safe manner than existing APIs. This API is subject to change or removal, we are looking for feedback and suggestions before making it a permanent part of Pydantic.


Generally, the pipeline API is used to define a sequence of steps to apply to incoming data during validation. The pipeline API is designed to be more type-safe and composable than the existing Pydantic API.

Each step in the pipeline can be:

A validation step that runs pydantic validation on the provided type
A transformation step that modifies the data
A constraint step that checks the data against a condition
A predicate step that checks the data against a condition and raises an error if it returns False

Note that the following example attempts to be exhaustive at the cost of complexity: if you find yourself writing this many transformations in type annotations you may want to consider having a UserIn and UserOut model (example below) or similar where you make the transformations via idiomatic plain Python code. These APIs are meant for situations where the code savings are significant and the added complexity is relatively small.

```python
from __future__ import annotations
from datetime import datetime
from typing import Annotated
from pydantic import BaseModel
from pydantic.experimental.pipeline import validate_as
class User(BaseModel):
  name: Annotated[str, validate_as(str).str_lower()]  
  age: Annotated[int, validate_as(int).gt(0)]  
  username: Annotated[str, validate_as(str).str_pattern(r'[a-z]+')]  
  password: Annotated[
      str,
      validate_as(str)
      .transform(str.lower)
      .predicate(lambda x: x != 'password'),  
  ]
  favorite_number: Annotated[  
      int,
      (validate_as(int) | validate_as(str).str_strip().validate_as(int)).gt(
          0
      ),
  ]
  friends: Annotated[list[User], validate_as(...).len(0, 100)]  
  bio: Annotated[
      datetime,
      validate_as(int)
      .transform(lambda x: x / 1_000_000)
      .validate_as(...),  # (8)
  ]
```
Mapping from BeforeValidator, AfterValidator and WrapValidator

The validate_as method is a more type-safe way to define BeforeValidator, AfterValidator and WrapValidator:

```python
from typing import Annotated
from pydantic.experimental.pipeline import transform, validate_as
# BeforeValidator
```
Annotated[int, validate_as(str).str_strip().validate_as(...)]  
# AfterValidator
# WrapValidator
  int,
  validate_as(str)
Alternative patterns

There are many alternative patterns to use depending on the scenario. Just as an example, consider the UserIn and UserOut pattern mentioned above:

```python
from __future__ import annotations
from pydantic import BaseModel
class UserIn(BaseModel):
    favorite_number: int | str
class UserOut(BaseModel):
    favorite_number: int
def my_api(user: UserIn) -> UserOut:
    favorite_number = user.favorite_number
    if isinstance(favorite_number, str):
        favorite_number = int(user.favorite_number.strip())
    return UserOut(favorite_number=favorite_number)
assert my_api(UserIn(favorite_number=' 1 ')).favorite_number == 1
```

This example uses plain idiomatic Python code that may be easier to understand, type-check, etc. than the examples above. The approach you choose should really depend on your use case. You will have to compare verbosity, performance, ease of returning meaningful errors to your users, etc. to choose the right pattern. Just be mindful of abusing advanced patterns like the pipeline API just because you can.

Partial Validation
New in v2.10

Partial validation allows you to validate an incomplete JSON string, or a Python object representing incomplete input data.

Partial validation is particularly helpful when processing the output of an LLM, where the model streams structured responses, and you may wish to begin validating the stream while you’re still receiving data (e.g. to show partial data to users).

> **Caution**

Partial validation is an experimental feature and may change in future versions of Pydantic. The current implementation should be considered a proof of concept at this time and has a number of limitations.

Partial validation can be enabled when using the three validation methods on TypeAdapter: TypeAdapter.validate_json(), TypeAdapter.validate_python(), and TypeAdapter.validate_strings(). This allows you to parse and validation incomplete JSON, but also to validate Python objects created by parsing incomplete data of any format.

The experimental_allow_partial flag can be passed to these methods to enable partial validation. It can take the following values (and is False, by default):

False or 'off' - disable partial validation
True or 'on' - enable partial validation, but don’t support trailing strings
'trailing-strings' - enable partial validation and support trailing strings

'trailing-strings'
mode

'trailing-strings' mode allows for trailing incomplete strings at the end of partial JSON to be included in the output. For example, if you’re validating against the following model:

```python
from typing import TypedDict
class Model(TypedDict):
    a: str
    b: str
```

Then the following JSON input would be considered valid, despite the incomplete string at the end:

'{"a": "hello", "b": "wor'

And would be validated as:

{'a': 'hello', 'b': 'wor'}

experiment_allow_partial in action:

```python
from typing import Annotated
from annotated_types import MinLen
from typing_extensions import NotRequired, TypedDict
from pydantic import TypeAdapter
class Foobar(TypedDict):  
  a: int
  b: NotRequired[float]
  c: NotRequired[Annotated[str, MinLen(5)]]
ta = TypeAdapter(list[Foobar])
v = ta.validate_json('[{"a": 1, "b"', experimental_allow_partial=True)  
print(v)
```
#> [{'a': 1}]
v = ta.validate_json(
print(v)
v = ta.validate_json(
print(v)
v = ta.validate_json(
print(v)
v = ta.validate_python([{'a': 1}], experimental_allow_partial=True)  
v = ta.validate_python(
print(v)
v = ta.validate_json(
print(v)
How Partial Validation Works

Partial validation follows the zen of Pydantic — it makes no guarantees about what the input data might have been, but it does guarantee to return a valid instance of the type you required, or raise a validation error.

To do this, the experimental_allow_partial flag enables two pieces of behavior:

1. Partial JSON parsing

The jiter JSON parser used by Pydantic already supports parsing partial JSON, experimental_allow_partial is simply passed to jiter via the allow_partial argument.

> **Note**

If you just want pure JSON parsing with support for partial JSON, you can use the jiter Python library directly, or pass the allow_partial argument when calling pydantic_core.from_json.

2. Ignore errors in the last element of the input 

Only having access to part of the input data means errors can commonly occur in the last element of the input data.

For example:

if a string has a constraint MinLen(5), when you only see part of the input, validation might fail because part of the string is missing (e.g. {"name": "Sam instead of {"name": "Samuel"})

The point is that if you only see part of some valid input data, validation errors can often occur in the last element of a sequence or last value of mapping.

To avoid these errors breaking partial validation, Pydantic will ignore ALL errors in the last element of the input data.

Errors in last element ignored
```python
from typing import Annotated
from annotated_types import MinLen
from pydantic import BaseModel, TypeAdapter
class MyModel(BaseModel):
    a: int
    b: Annotated[str, MinLen(5)]
ta = TypeAdapter(list[MyModel])
v = ta.validate_json(
    '[{"a": 1, "b": "12345"}, {"a": 1,',
    experimental_allow_partial=True,
```
)
print(v)
Limitations of Partial Validation
TypeAdapter only

You can only pass experiment_allow_partial to TypeAdapter methods, it’s not yet supported via other Pydantic entry points like BaseModel.

Types supported

Right now only a subset of collection validators know how to handle partial validation:

list
set
frozenset
dict (as in dict[X, Y])
TypedDict — only non-required fields may be missing, e.g. via NotRequired or total=False)

While you can use experimental_allow_partial while validating against types that include other collection validators, those types will be validated “all or nothing”, and partial validation will not work on more nested types.

E.g. in the above example partial validation works although the second item in the list is dropped completely since BaseModel doesn’t (yet) support partial validation.

But partial validation won’t work at all in the follow example because BaseModel doesn’t support partial validation so it doesn’t forward the allow_partial instruction down to the list validator in b:

```python
from typing import Annotated
from annotated_types import MinLen
from pydantic import BaseModel, TypeAdapter, ValidationError
class MyModel(BaseModel):
  a: int = 1
  b: list[Annotated[str, MinLen(5)]] = []  
ta = TypeAdapter(MyModel)
try:
  v = ta.validate_json(
      '{"a": 1, "b": ["12345", "12', experimental_allow_partial=True
  )
except ValidationError as e:
  print(e)
  """
  1 validation error for MyModel
  b.1
    String should have at least 5 characters [type=string_too_short, input_value='12', input_type=str]
  """
```
Some invalid but complete JSON will be accepted

The way jiter (the JSON parser used by Pydantic) works means it’s currently not possible to differentiate between complete JSON like {"a": 1, "b": "12"} and incomplete JSON like {"a": 1, "b": "12.

This means that some invalid JSON will be accepted by Pydantic when using experimental_allow_partial, e.g.:

```python
from typing import Annotated
from annotated_types import MinLen
from typing_extensions import TypedDict
from pydantic import TypeAdapter
class Foobar(TypedDict, total=False):
  a: int
  b: Annotated[str, MinLen(5)]
ta = TypeAdapter(Foobar)
v = ta.validate_json(
  '{"a": 1, "b": "12', experimental_allow_partial=True  
```
)
print(v)
v = ta.validate_json(
print(v)
Any error in the last field of the input will be ignored

As described above, many errors can result from truncating the input. Rather than trying to specifically ignore errors that could result from truncation, Pydantic ignores all errors in the last element of the input in partial validation mode.

This means clearly invalid data will pass validation if the error is in the last field of the input:

```python
from typing import Annotated
from annotated_types import Ge
from pydantic import TypeAdapter
ta = TypeAdapter(list[Annotated[int, Ge(10)]])
v = ta.validate_python([20, 30, 4], experimental_allow_partial=True)  
print(v)
```
#> [20, 30]
ta = TypeAdapter(list[int])
Validation of a callable’s arguments

Pydantic provides the @validate_call decorator to perform validation on the provided arguments (and additionally return type) of a callable. However, it only allows arguments to be provided by actually calling the decorated callable. In some situations, you may want to just validate the arguments, such as when loading from other data sources such as JSON data.

For this reason, the experimental generate_arguments_schema() function can be used to construct a core schema, which can later be used with a SchemaValidator.

```python
from pydantic_core import SchemaValidator
from pydantic.experimental.arguments_schema import generate_arguments_schema
def func(p: bool, *args: str, **kwargs: int) -> None: ...
arguments_schema = generate_arguments_schema(func=func)
val = SchemaValidator(arguments_schema, config={'coerce_numbers_to_str': True})
```
args, kwargs = val.validate_json(
  '{"p": true, "args": ["arg1", 1], "kwargs": {"extra": 1}}'
print(args, kwargs)  
from inspect import signature
#> {'p': True, 'args': ('arg1', '1'), 'kwargs': {'extra': 1}}

> **Note**

Unlike @validate_call, this core schema will only validate the provided arguments; the underlying callable will not be called.

Additionally, you can ignore specific parameters by providing a callback, which is called for every parameter:

```python
from typing import Any
from pydantic_core import SchemaValidator
from pydantic.experimental.arguments_schema import generate_arguments_schema
def func(p: bool, *args: str, **kwargs: int) -> None: ...
def skip_first_parameter(index: int, name: str, annotation: Any) -> Any:
    if index == 0:
        return 'skip'
arguments_schema = generate_arguments_schema(
    func=func,
    parameters_callback=skip_first_parameter,
```
)
val = SchemaValidator(arguments_schema)
print(args, kwargs)
MISSING sentinel

The MISSING sentinel is a singleton indicating a field value was not provided during validation.

This singleton can be used as a default value, as an alternative to None when it has an explicit meaning. During serialization, any field with MISSING as a value is excluded from the output.

```python
from typing import Union
from pydantic import BaseModel
from pydantic.experimental.missing_sentinel import MISSING
class Configuration(BaseModel):
    timeout: Union[int, None, MISSING] = MISSING
# configuration defaults, stored somewhere else:
defaults = {'timeout': 200}
conf = Configuration()
# `timeout` is excluded from the serialization output:
```
conf.model_dump()
# {}
#> {'anyOf': [{'type': 'integer'}, {'type': 'null'}], 'title': 'Timeout'}}
# `is` can be used to discriminate between the sentinel and other values:
This feature is marked as experimental because it relies on the draft PEP 661, introducing sentinels in the standard library.

As such, the following limitations currently apply:

Static type checking of sentinels is only supported with Pyright 1.1.402 or greater, and the enableExperimentalFeatures type evaluation setting should be enabled.
Pickling of models containing MISSING as a value is not supported.

> **Note**

When applying constraints to a union containing the MISSING sentinel, such constraints are automatically applied to the remaining type(s) of the union.

