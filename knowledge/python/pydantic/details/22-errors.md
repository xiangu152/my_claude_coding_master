---
title: "Errors"

# Errors
> 原始文档来源：https://pydantic.dev/docs/validation/latest/errors/errors/

---
Error Handling

Pydantic will raise a ValidationError whenever it finds an error in the data it’s validating.

> **Note**

Validation code should not raise the ValidationError itself, but rather raise a ValueError or a AssertionError (or subclass thereof) which will be caught and used to populate the final ValidationError.

For more details, refer to the dedicated section of the validators documentation.

That ValidationError will contain information about all the errors and how they happened.

You can access these errors in several ways:

Method	Description
errors()	Returns a list of ErrorDetails errors found in the input data.
error_count()	Returns the number of errors.
json()	Returns a JSON representation of the list errors.
str(e)	Returns a human-readable representation of the errors.

The ErrorDetails object is a dictionary. It contains the following:

Property	Description
ctx	An optional object which contains values required to render the error message.
input	The input provided for validation.
loc	The error’s location as a list.
msg	A human-readable explanation of the error.
type	A computer-readable identifier of the error type.
url	The documentation URL giving information about the error.

The first item in the loc list will be the field where the error occurred, and if the field is a sub-model, subsequent items will be present to indicate the nested location of the error.

As a demonstration:

```python
from pydantic import BaseModel, Field, ValidationError, field_validator
class Location(BaseModel):
    lat: float = 0.1
    lng: float = 10.1
class Model(BaseModel):
    is_required: float
    gt_int: int = Field(gt=42)
    list_of_ints: list[int]
    a_float: float
    recursive_model: Location
    @field_validator('a_float', mode='after')
    @classmethod
    def validate_float(cls, value: float) -> float:
        if value > 2.0:
            raise ValueError('Invalid float value')
        return value
data = {
    'list_of_ints': ['1', 2, 'bad'],
    'a_float': 3.0,
    'recursive_model': {'lat': 4.2, 'lng': 'New York'},
    'gt_int': 21,
```
}
try:

Pydantic attempts to provide useful default error messages for validation and usage errors, which can be found here:

Validation Errors: Errors that happen during data validation.
Usage Errors: Errors that happen when using Pydantic.
Customize error messages

You can customize error messages by creating a custom error handler.

```python
from pydantic_core import ErrorDetails
from pydantic import BaseModel, HttpUrl, ValidationError
CUSTOM_MESSAGES = {
    'int_parsing': 'This is not an integer! 🤦',
    'url_scheme': 'Hey, use the right URL scheme! I wanted {expected_schemes}.',
```
}
```python
def convert_errors(
    e: ValidationError, custom_messages: dict[str, str]
```
) -> list[ErrorDetails]:
```python
    new_errors: list[ErrorDetails] = []
    for error in e.errors():
        custom_message = custom_messages.get(error['type'])
        if custom_message:
            ctx = error.get('ctx')
            error['msg'] = (
                custom_message.format(**ctx) if ctx else custom_message
            )
        new_errors.append(error)
    return new_errors
```
```python
class Model(BaseModel):
    a: int
    b: HttpUrl
try:
    Model(a='wrong', b='ftp://example.com')
except ValidationError as e:
    errors = convert_errors(e, CUSTOM_MESSAGES)
    print(errors)
    """
    [
        {
            'type': 'int_parsing',
            'loc': ('a',),
            'msg': 'This is not an integer! 🤦',
            'input': 'wrong',
            'url': 'https://errors.pydantic.dev/2/v/int_parsing',
        },
        {
            'type': 'url_scheme',
            'loc': ('b',),
            'msg': "Hey, use the right URL scheme! I wanted 'http' or 'https'.",
            'input': 'ftp://example.com',
            'ctx': {'expected_schemes': "'http' or 'https'"},
            'url': 'https://errors.pydantic.dev/2/v/url_scheme',
        },
    ]
    """
```

A common use case would be to translate error messages. For example, in the above example, we could translate the error messages replacing the CUSTOM_MESSAGES dictionary with a dictionary of translations.

Another example is customizing the way that the 'loc' value of an error is represented.

```python
from typing import Any, Union
from pydantic import BaseModel, ValidationError
def loc_to_dot_sep(loc: tuple[Union[str, int], ...]) -> str:
  path = ''
  for i, x in enumerate(loc):
      if isinstance(x, str):
          if i > 0:
              path += '.'
          path += x
      elif isinstance(x, int):
          path += f'[{x}]'
      else:
          raise TypeError('Unexpected type')
  return path
def convert_errors(e: ValidationError) -> list[dict[str, Any]]:
  new_errors: list[dict[str, Any]] = e.errors()
  for error in new_errors:
      error['loc'] = loc_to_dot_sep(error['loc'])
  return new_errors
class TestNestedModel(BaseModel):
  key: str
  value: str
class TestModel(BaseModel):
  items: list[TestNestedModel]
data = {'items': [{'key': 'foo', 'value': 'bar'}, {'key': 'baz'}]}
try:
  TestModel.model_validate(data)
except ValidationError as e:
  print(e.errors())  
  """
  [
      {
          'type': 'missing',
          'loc': ('items', 1, 'value'),
          'msg': 'Field required',
          'input': {'key': 'baz'},
          'url': 'https://errors.pydantic.dev/2/v/missing',
      }
  ]
  """
  pretty_errors = convert_errors(e)
  print(pretty_errors)  
  """
  [
      {
          'type': 'missing',
          'loc': 'items[1].value',
          'msg': 'Field required',
          'input': {'key': 'baz'},
          'url': 'https://errors.pydantic.dev/2/v/missing',
      }
  ]
  """
```
