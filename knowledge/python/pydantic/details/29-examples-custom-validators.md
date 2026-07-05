---
title: "Examples Custom Validators"

# Examples Custom Validators
> 原始文档来源：https://pydantic.dev/docs/validation/latest/examples/custom_validators/

---
Custom Validators

This page provides example snippets for creating more complex, custom validators in Pydantic. Many of these examples are adapted from Pydantic issues and discussions, and are intended to showcase the flexibility and power of Pydantic’s validation system.

Custom datetime Validator via Annotated Metadata

In this example, we’ll construct a custom validator, attached to an Annotated type, that ensures a datetime object adheres to a given timezone constraint.

The custom validator supports string specification of the timezone, and will raise an error if the datetime object does not have the correct timezone.

We use __get_pydantic_core_schema__ in the validator to customize the schema of the annotated type (in this case, datetime), which allows us to add custom validation logic. Notably, we use a wrap validator function so that we can perform operations both before and after the default pydantic validation of a datetime.

```python
import datetime as dt
from dataclasses import dataclass
from pprint import pprint
from typing import Annotated, Any, Callable, Optional
import pytz
from pydantic_core import CoreSchema, core_schema
from pydantic import (
  GetCoreSchemaHandler,
  PydanticUserError,
  TypeAdapter,
  ValidationError,
```
)
```python
@dataclass(frozen=True)
class MyDatetimeValidator:
  tz_constraint: Optional[str] = None
  def tz_constraint_validator(
      self,
      value: dt.datetime,
      handler: Callable,  
  ):
      """Validate tz_constraint and tz_info."""
      # handle naive datetimes
      if self.tz_constraint is None:
          assert (
              value.tzinfo is None
          ), 'tz_constraint is None, but provided value is tz-aware.'
          return handler(value)
      # validate tz_constraint and tz-aware tzinfo
      if self.tz_constraint not in pytz.all_timezones:
          raise PydanticUserError(
              f'Invalid tz_constraint: {self.tz_constraint}',
              code='unevaluable-type-annotation',
          )
      result = handler(value)  
      assert self.tz_constraint == str(
          result.tzinfo
      ), f'Invalid tzinfo: {str(result.tzinfo)}, expected: {self.tz_constraint}'
      return result
  def __get_pydantic_core_schema__(
      self,
      source_type: Any,
      handler: GetCoreSchemaHandler,
  ) -> CoreSchema:
      return core_schema.no_info_wrap_validator_function(
          self.tz_constraint_validator,
          handler(source_type),
      )
LA = 'America/Los_Angeles'
ta = TypeAdapter(Annotated[dt.datetime, MyDatetimeValidator(LA)])
print(
  ta.validate_python(dt.datetime(2023, 1, 1, 0, 0, tzinfo=pytz.timezone(LA)))
```
)
#> 2023-01-01 00:00:00-07:53
LONDON = 'Europe/London'
We can also enforce UTC offset constraints in a similar way. Assuming we have a lower_bound and an upper_bound, we can create a custom validator to ensure our datetime has a UTC offset that is inclusive within the boundary we define:

```python
import datetime as dt
from dataclasses import dataclass
from pprint import pprint
from typing import Annotated, Any, Callable
import pytz
from pydantic_core import CoreSchema, core_schema
from pydantic import GetCoreSchemaHandler, TypeAdapter, ValidationError
@dataclass(frozen=True)
class MyDatetimeValidator:
    lower_bound: int
    upper_bound: int
    def validate_tz_bounds(self, value: dt.datetime, handler: Callable):
        """Validate and test bounds"""
        assert value.utcoffset() is not None, 'UTC offset must exist'
        assert self.lower_bound <= self.upper_bound, 'Invalid bounds'
        result = handler(value)
        hours_offset = value.utcoffset().total_seconds() / 3600
        assert (
            self.lower_bound <= hours_offset <= self.upper_bound
        ), 'Value out of bounds'
        return result
    def __get_pydantic_core_schema__(
        self,
        source_type: Any,
        handler: GetCoreSchemaHandler,
    ) -> CoreSchema:
        return core_schema.no_info_wrap_validator_function(
            self.validate_tz_bounds,
            handler(source_type),
        )
LA = 'America/Los_Angeles'  # UTC-7 or UTC-8
ta = TypeAdapter(Annotated[dt.datetime, MyDatetimeValidator(-10, -5)])
print(
    ta.validate_python(dt.datetime(2023, 1, 1, 0, 0, tzinfo=pytz.timezone(LA)))
```
)
#> 2023-01-01 00:00:00-07:53
LONDON = 'Europe/London'

Here, we demonstrate two ways to validate a field of a nested model, where the validator utilizes data from the parent model.

In this example, we construct a validator that checks that each user’s password is not in a list of forbidden passwords specified by the parent model.

One way to do this is to place a custom validator on the outer model:

```python
from typing_extensions import Self
from pydantic import BaseModel, ValidationError, model_validator
class User(BaseModel):
    username: str
    password: str
class Organization(BaseModel):
    forbidden_passwords: list[str]
    users: list[User]
    @model_validator(mode='after')
    def validate_user_passwords(self) -> Self:
        """Check that user password is not in forbidden list. Raise a validation error if a forbidden password is encountered."""
        for user in self.users:
            current_pw = user.password
            if current_pw in self.forbidden_passwords:
                raise ValueError(
                    f'Password {current_pw} is forbidden. Please choose another password for user {user.username}.'
                )
        return self
data = {
    'forbidden_passwords': ['123'],
    'users': [
        {'username': 'Spartacat', 'password': '123'},
        {'username': 'Iceburgh', 'password': '87'},
    ],
```
}
try:
Alternatively, a custom validator can be used in the nested model class (User), with the forbidden passwords data from the parent model being passed in via validation context.

> **Caution**

The ability to mutate the context within a validator adds a lot of power to nested validation, but can also lead to confusing or hard-to-debug code. Use this approach at your own risk!

```python
from pydantic import BaseModel, ValidationError, ValidationInfo, field_validator
class User(BaseModel):
    username: str
    password: str
    @field_validator('password', mode='after')
    @classmethod
    def validate_user_passwords(
        cls, password: str, info: ValidationInfo
    ) -> str:
        """Check that user password is not in forbidden list."""
        forbidden_passwords = (
            info.context.get('forbidden_passwords', []) if info.context else []
        )
        if password in forbidden_passwords:
            raise ValueError(f'Password {password} is forbidden.')
        return password
class Organization(BaseModel):
    forbidden_passwords: list[str]
    users: list[User]
    @field_validator('forbidden_passwords', mode='after')
    @classmethod
    def add_context(cls, v: list[str], info: ValidationInfo) -> list[str]:
        if info.context is not None:
            info.context.update({'forbidden_passwords': v})
        return v
data = {
    'forbidden_passwords': ['123'],
    'users': [
        {'username': 'Spartacat', 'password': '123'},
        {'username': 'Iceburgh', 'password': '87'},
    ],
```
}
try:
Note that if the context property is not included in model_validate, then info.context will be None and the forbidden passwords list will not get added to the context in the above implementation. As such, validate_user_passwords would not carry out the desired password validation.

More details about validation context can be found in the validators documentation.

