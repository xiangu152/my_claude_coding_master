---
title: "Strict Mode"

# Strict Mode
> 原始文档来源：https://pydantic.dev/docs/validation/latest/concepts/strict_mode/

---
Strict Mode

By default, Pydantic will attempt to coerce values to the desired type when possible. For example, you can pass the string '123' as the input for the int number type, and it will be converted to the value 123. This coercion behavior is useful in many scenarios — think: UUIDs, URL parameters, HTTP headers, environment variables, dates, etc.

However, there are also situations where this is not desirable, and you want Pydantic to error instead of coercing data.

To better support this use case, Pydantic provides a “strict mode”. When strict mode is enabled, Pydantic will be much less lenient when coercing data, and will instead error if the data is not of the correct type.

Most of the time, strict mode will only allow instances of the type to be provided, although looser rules may apply to JSON input (for instance, the date and time types allow strings even in strict mode).

The strict behavior for each type can be found in the standard library types documentation, and is summarized in the conversion table.

Here is a brief example showing the validation behavior difference in strict and the default lax mode:

```python
from pydantic import BaseModel, ValidationError
class MyModel(BaseModel):
    x: int
print(MyModel.model_validate({'x': '123'}))  # lax mode
```
#> x=123
try:
Strict mode can be enabled in various ways:

As a validation parameter, such as when using model_validate(), on Pydantic models.
At the field level.
At the configuration level (with the possibility to override at the field level).
As a validation parameter

Strict mode can be enaled on a per-validation-call basis, when using the validation methods on Pydantic models and type adapters.

```python
from datetime import date
from pydantic import TypeAdapter, ValidationError
print(TypeAdapter(date).validate_python('2000-01-01'))  # OK: lax
```
#> 2000-01-01
try:
#> 2000-01-01
At the field level

Strict mode can be enabled on specific fields, by setting the strict parameter of the Field() function to True. Strict mode will be applied for such fields, even when the validation methods are called in lax mode.

```python
from pydantic import BaseModel, Field, ValidationError
class User(BaseModel):
  name: str
  age: int = Field(strict=True)  
user = User(name='John', age=42)
print(user)
```
#> name='John' age=42
try:

As an alternative to the Field() function, Pydantic provides the Strict metadata class, meant to be used with the annotated pattern. It also provides convenience aliases for the most common types (namely StrictBool, StrictInt, StrictFloat, StrictStr and StrictBytes).

```python
from typing import Annotated
from uuid import UUID
from pydantic import BaseModel, Strict, StrictInt
class User(BaseModel):
  id: Annotated[UUID, Strict()]
  age: StrictInt  
```
As a configuration value

Strict mode behavior can be controlled at the configuration level. When used on a Pydantic model (or model like class such as dataclasses), strictness can still be overridden at the field level:

```python
from pydantic import BaseModel, ConfigDict, Field
class User(BaseModel):
    model_config = ConfigDict(strict=True)
    name: str
    age: int = Field(strict=False)
print(User(name='John', age='18'))
```
#> name='John' age=18
