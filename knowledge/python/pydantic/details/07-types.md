---
title: "Types"

# Types
> 原始文档来源：https://pydantic.dev/docs/validation/latest/concepts/types/

---
Types

Pydantic uses types to define how validation and serialization should be performed. Built-in and standard library types (such as int, str, date) can be used as is. Strictness can be controlled and constraints can be applied on them.

On top of these, Pydantic provides extra types, either directly in the library (e.g. SecretStr) or in the pydantic-extra-types external library. These are implemented using the patterns described in the custom types section. Strictness and constraints can’t be applied on them.

The built-in and standard library types documentation goes over the supported types: the allowed values, the possible validation constraints, and whether strictness can be configured.

See also the conversion table for a summary of the allowed values for each type.

This page will go over defining your own custom types.

Custom Types

There are several ways to define your custom types.

Using the annotated pattern

The annotated pattern can be used to make types reusable across your code base. For example, to create a type representing a positive integer:

```python
from typing import Annotated
from pydantic import Field, TypeAdapter, ValidationError
PositiveInt = Annotated[int, Field(gt=0)]  
ta = TypeAdapter(PositiveInt)
print(ta.validate_python(1))
```
#> 1
try:

You can add or override validation, serialization, and JSON schemas to an arbitrary type using the markers that Pydantic exports:

```python
from typing import Annotated
from pydantic import (
    AfterValidator,
    PlainSerializer,
    TypeAdapter,
    WithJsonSchema,
```
)
TruncatedFloat = Annotated[
ta = TypeAdapter(TruncatedFloat)

Type variables can be used within the Annotated type:

```python
from typing import Annotated, TypeVar
from annotated_types import Gt, Len
from pydantic import TypeAdapter, ValidationError
T = TypeVar('T')
ShortList = Annotated[list[T], Len(max_length=4)]
ta = TypeAdapter(ShortList[int])
v = ta.validate_python([1, 2, 3, 4])
assert v == [1, 2, 3, 4]
try:
    ta.validate_python([1, 2, 3, 4, 5])
except ValidationError as exc:
    print(exc)
    """
    1 validation error for list[int]
      List should have at most 4 items after validation, not 5 [type=too_long, input_value=[1, 2, 3, 4, 5], input_type=list]
    """
PositiveList = list[Annotated[T, Gt(0)]]
ta = TypeAdapter(PositiveList[float])
v = ta.validate_python([1.0])
assert type(v[0]) is float
try:
    ta.validate_python([-1.0])
except ValidationError as exc:
    print(exc)
    """
    1 validation error for list[constrained-float]
    0
      Input should be greater than 0 [type=greater_than, input_value=-1.0, input_type=float]
    """
```
Named type aliases
New in v2.11

Named type aliases are now fully supported.

The above examples make use of implicit type aliases, assigned to a variable. At runtime, Pydantic has no way of knowing the name of the variable it was assigned to, and this can be problematic for two reasons:

The JSON Schema of the alias won’t be converted into a definition. This is mostly useful when you are using the alias more than once in a model definition.
In most cases, recursive type aliases won’t work.

By leveraging the new type statement (introduced in PEP 695), you can define aliases as follows:

Python 3.9 and above
Python 3.12 and above (new syntax)
```python
from typing import Annotated
from annotated_types import Gt
from typing_extensions import TypeAliasType
from pydantic import BaseModel
PositiveIntList = TypeAliasType('PositiveIntList', list[Annotated[int, Gt(0)]])
class Model(BaseModel):
  x: PositiveIntList
  y: PositiveIntList
print(Model.model_json_schema())  
```
"""
{
  '$defs': {
"""

When to use named type aliases

While (named) PEP 695 and implicit type aliases are meant to be equivalent for static type checkers, Pydantic will not understand field-specific metadata inside named aliases. That is, metadata such as alias, default, deprecated, cannot be used:

Python 3.9 and above
Python 3.12 and above (new syntax)
```python
from typing import Annotated
from typing_extensions import TypeAliasType
from pydantic import BaseModel, Field
MyAlias = TypeAliasType('MyAlias', Annotated[int, Field(default=1)])
class Model(BaseModel):
    x: MyAlias  # This is not allowed
```

Only metadata that can be applied to the annotated type itself is allowed (e.g. validation constraints and JSON metadata). Trying to support field-specific metadata would require eagerly inspecting the type alias’s __value__, and as such Pydantic wouldn’t be able to have the alias stored as a JSON Schema definition.

> **Note**

As with implicit type aliases, type variables can also be used inside the generic alias:

Python 3.9 and above
Python 3.12 and above (new syntax)
```python
from typing import Annotated, TypeVar
from annotated_types import Len
from typing_extensions import TypeAliasType
T = TypeVar('T')
ShortList = TypeAliasType(
    'ShortList', Annotated[list[T], Len(max_length=4)], type_params=(T,)
```
)
Named recursive types

Named type aliases should be used whenever you need to define recursive type aliases 
.

For instance, here is an example definition of a JSON type:

Python 3.9 and above
Python 3.12 and above (new syntax)
```python
from typing import Union
from typing_extensions import TypeAliasType
from pydantic import TypeAdapter
Json = TypeAliasType(
  'Json',
  'Union[dict[str, Json], list[Json], str, int, float, bool, None]',  
```
)
ta = TypeAdapter(Json)
{
  '$defs': {
"""

> **Tip**

Pydantic defines a JsonValue type as a convenience.

Customizing validation with __get_pydantic_core_schema__

To do more extensive customization of how Pydantic handles custom classes, and in particular when you have access to the class or can subclass it, you can implement a special __get_pydantic_core_schema__ to tell Pydantic how to generate the pydantic-core schema.

While pydantic uses pydantic-core internally to handle validation and serialization, it is a new API for Pydantic V2, thus it is one of the areas most likely to be tweaked in the future and you should try to stick to the built-in constructs like those provided by annotated-types, pydantic.Field, or BeforeValidator and so on.

You can implement __get_pydantic_core_schema__ both on a custom type and on metadata intended to be put in Annotated. In both cases the API is middleware-like and similar to that of “wrap” validators: you get a source_type (which isn’t necessarily the same as the class, in particular for generics) and a handler that you can call with a type to either call the next metadata in Annotated or call into Pydantic’s internal schema generation.

The simplest no-op implementation calls the handler with the type you are given, then returns that as the result. You can also choose to modify the type before calling the handler, modify the core schema returned by the handler, or not call the handler at all.

As a method on a custom type

The following is an example of a type that uses __get_pydantic_core_schema__ to customize how it gets validated. This is equivalent to implementing __get_validators__ in Pydantic V1.

```python
from typing import Any
from pydantic_core import CoreSchema, core_schema
from pydantic import GetCoreSchemaHandler, TypeAdapter
class Username(str):
    @classmethod
    def __get_pydantic_core_schema__(
        cls, source_type: Any, handler: GetCoreSchemaHandler
    ) -> CoreSchema:
        return core_schema.no_info_after_validator_function(cls, handler(str))
ta = TypeAdapter(Username)
res = ta.validate_python('abc')
assert isinstance(res, Username)
assert res == 'abc'
```

See JSON Schema for more details on how to customize JSON schemas for custom types.

As an annotation

Often you’ll want to parametrize your custom type by more than just generic type parameters (which you can do via the type system and will be discussed later). Or you may not actually care (or want to) make an instance of your subclass; you actually want the original type, just with some extra validation done.

For example, if you were to implement pydantic.AfterValidator (see Adding validation and serialization) yourself, you’d do something similar to the following:

```python
from dataclasses import dataclass
from typing import Annotated, Any, Callable
from pydantic_core import CoreSchema, core_schema
from pydantic import BaseModel, GetCoreSchemaHandler
@dataclass(frozen=True)  
class MyAfterValidator:
  func: Callable[[Any], Any]
  def __get_pydantic_core_schema__(
      self, source_type: Any, handler: GetCoreSchemaHandler
  ) -> CoreSchema:
      return core_schema.no_info_after_validator_function(
          self.func, handler(source_type)
      )
Username = Annotated[str, MyAfterValidator(str.lower)]
class Model(BaseModel):
  name: Username
assert Model(name='ABC').name == 'abc'  
```
Handling third-party types

Another use case for the pattern in the previous section is to handle third party types.

```python
from typing import Annotated, Any
from pydantic_core import core_schema
from pydantic import (
    BaseModel,
    GetCoreSchemaHandler,
    GetJsonSchemaHandler,
    ValidationError,
```
)
```python
from pydantic.json_schema import JsonSchemaValue
class ThirdPartyType:
    """
    This is meant to represent a type from a third-party library that wasn't designed with Pydantic
    integration in mind, and so doesn't have a `pydantic_core.CoreSchema` or anything.
    """
    x: int
    def __init__(self):
        self.x = 0
class _ThirdPartyTypePydanticAnnotation:
    @classmethod
    def __get_pydantic_core_schema__(
        cls,
        _source_type: Any,
        _handler: GetCoreSchemaHandler,
    ) -> core_schema.CoreSchema:
        """
        We return a pydantic_core.CoreSchema that behaves in the following ways:
        * ints will be parsed as `ThirdPartyType` instances with the int as the x attribute
        * `ThirdPartyType` instances will be parsed as `ThirdPartyType` instances without any changes
        * Nothing else will pass validation
        * Serialization will always return just an int
        """
        def validate_from_int(value: int) -> ThirdPartyType:
            result = ThirdPartyType()
            result.x = value
            return result
        from_int_schema = core_schema.chain_schema(
            [
                core_schema.int_schema(),
                core_schema.no_info_plain_validator_function(validate_from_int),
            ]
        )
        return core_schema.json_or_python_schema(
            json_schema=from_int_schema,
            python_schema=core_schema.union_schema(
                [
                    # check if it's an instance first before doing any further work
                    core_schema.is_instance_schema(ThirdPartyType),
                    from_int_schema,
                ]
            ),
            serialization=core_schema.plain_serializer_function_ser_schema(
                lambda instance: instance.x
            ),
        )
    @classmethod
    def __get_pydantic_json_schema__(
        cls, _core_schema: core_schema.CoreSchema, handler: GetJsonSchemaHandler
    ) -> JsonSchemaValue:
        # Use the same schema that would be used for `int`
        return handler(core_schema.int_schema())
# We now create an `Annotated` wrapper that we'll use as the annotation for fields on `BaseModel`s, etc.
PydanticThirdPartyType = Annotated[
    ThirdPartyType, _ThirdPartyTypePydanticAnnotation
```
]
# Create a model class that uses this annotation as a field
m_instance = Model(third_party_type=instance)

You can use this approach to e.g. define behavior for Pandas or Numpy types.

Using GetPydanticSchema to reduce boilerplate

You may notice that the above examples where we create a marker class require a good amount of boilerplate. For many simple cases you can greatly minimize this by using pydantic.GetPydanticSchema:

```python
from typing import Annotated
from pydantic_core import core_schema
from pydantic import BaseModel, GetPydanticSchema
class Model(BaseModel):
    y: Annotated[
        str,
        GetPydanticSchema(
            lambda tp, handler: core_schema.no_info_after_validator_function(
                lambda x: x * 2, handler(tp)
            )
        ),
    ]
assert Model(y='ab').y == 'abab'
```
Summary

Let’s recap:

Pydantic provides high level hooks to customize types via Annotated like AfterValidator and Field. Use these when possible.
Under the hood these use pydantic-core to customize validation, and you can hook into that directly using GetPydanticSchema or a marker class with __get_pydantic_core_schema__.
If you really want a custom type you can implement __get_pydantic_core_schema__ on the type itself.
Handling custom generic classes

> **Caution**

This is an advanced technique that you might not need in the beginning. In most of the cases you will probably be fine with standard Pydantic models.

You can use Generic Classes as field types and perform custom validation based on the “type parameters” (or sub-types) with __get_pydantic_core_schema__.

If the Generic class that you are using as a sub-type has a classmethod __get_pydantic_core_schema__, you don’t need to use arbitrary_types_allowed for it to work.

Because the source_type parameter is not the same as the cls parameter, you can use typing.get_args (or typing_extensions.get_args) to extract the generic parameters. Then you can use the handler to generate a schema for them by calling handler.generate_schema. Note that we do not do something like handler(get_args(source_type)[0]) because we want to generate an unrelated schema for that generic parameter, not one that is influenced by the current context of Annotated metadata and such. This is less important for custom types, but crucial for annotated metadata that modifies schema building.

```python
from dataclasses import dataclass
from typing import Any, Generic, TypeVar
from pydantic_core import CoreSchema, core_schema
from typing_extensions import get_args, get_origin
from pydantic import (
    BaseModel,
    GetCoreSchemaHandler,
    ValidationError,
    ValidatorFunctionWrapHandler,
```
)
ItemType = TypeVar('ItemType')
print(model)
car_owner=Owner(name='John', item=Car(color='black')) home_owner=Owner(name='James', item=House(rooms=3))
"""
try:
print(model)
car_owner=Owner(name='John', item=Car(color='black')) home_owner=Owner(name='James', item=House(rooms=3))
"""
try:

The same idea can be applied to create generic container types, like a custom Sequence type:

```python
from collections.abc import Sequence
from typing import Any, TypeVar
from pydantic_core import ValidationError, core_schema
from typing_extensions import get_args
from pydantic import BaseModel, GetCoreSchemaHandler
T = TypeVar('T')
class MySequence(Sequence[T]):
    def __init__(self, v: Sequence[T]):
        self.v = v
    def __getitem__(self, i):
        return self.v[i]
    def __len__(self):
        return len(self.v)
    @classmethod
    def __get_pydantic_core_schema__(
        cls, source: Any, handler: GetCoreSchemaHandler
    ) -> core_schema.CoreSchema:
        instance_schema = core_schema.is_instance_schema(cls)
        args = get_args(source)
        if args:
            # replace the type and rely on Pydantic to generate the right schema
            # for `Sequence`
            sequence_t_schema = handler.generate_schema(Sequence[args[0]])
        else:
            sequence_t_schema = handler.generate_schema(Sequence)
        non_instance_schema = core_schema.no_info_after_validator_function(
            MySequence, sequence_t_schema
        )
        return core_schema.union_schema([instance_schema, non_instance_schema])
class M(BaseModel):
    model_config = dict(validate_default=True)
    s1: MySequence = [3]
m = M()
print(m)
```
#> s1=<__main__.MySequence object at 0x0123456789ab>
print(m.s1.v)
```python
class M(BaseModel):
    s1: MySequence[int]
```
M(s1=[1])
try:

> **Note**

This was not possible with Pydantic V2 to V2.3, it was re-added in Pydantic V2.4.

As of Pydantic V2.4, you can access the field name via the handler.field_name within __get_pydantic_core_schema__ and thereby set the field name which will be available from info.field_name.

```python
from typing import Any
from pydantic_core import core_schema
from pydantic import BaseModel, GetCoreSchemaHandler, ValidationInfo
class CustomType:
    """Custom type that stores the field it was used in."""
    def __init__(self, value: int, field_name: str):
        self.value = value
        self.field_name = field_name
    def __repr__(self):
        return f'CustomType<{self.value} {self.field_name!r}>'
    @classmethod
    def validate(cls, value: int, info: ValidationInfo):
        return cls(value, info.field_name)
    @classmethod
    def __get_pydantic_core_schema__(
        cls, source_type: Any, handler: GetCoreSchemaHandler
    ) -> core_schema.CoreSchema:
        return core_schema.with_info_after_validator_function(
            cls.validate, handler(int)
        )
class MyModel(BaseModel):
    my_field: CustomType
m = MyModel(my_field=1)
print(m.my_field)
```
#> CustomType<1 'my_field'>

You can also access field_name from the markers used with Annotated, like AfterValidator.

```python
from typing import Annotated
from pydantic import AfterValidator, BaseModel, ValidationInfo
def my_validators(value: int, info: ValidationInfo):
    return f'<{value} {info.field_name!r}>'
class MyModel(BaseModel):
    my_field: Annotated[int, AfterValidator(my_validators)]
m = MyModel(my_field=1)
print(m.my_field)
```
#> <1 'my_field'>
