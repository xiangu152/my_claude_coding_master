---
title: "Examples Dynamic Models"

# Examples Dynamic Models
> 原始文档来源：https://pydantic.dev/docs/validation/latest/examples/dynamic_models/

---
Dynamic models

Models can be created dynamically using the create_model() factory function.

In this example, we will show how to dynamically derive a model from an existing one, making every field optional. To achieve this, we will make use of the model_fields model class attribute, and derive new annotations from the field definitions to be passed to the create_model() factory. Of course, this example can apply to any use case where you need to derive a new model from another (remove default values, add aliases, etc).

Python 3.9
Python 3.10
Python 3.11 and above
```python
from typing import Annotated, Union
from pydantic import BaseModel, Field, create_model
def make_fields_optional(model_cls: type[BaseModel]) -> type[BaseModel]:
  new_fields = {}
  for f_name, f_info in model_cls.model_fields.items():
      f_dct = f_info.asdict()
      new_fields[f_name] = (
          Annotated[(Union[f_dct['annotation'], None], *f_dct['metadata'], Field(**f_dct['attributes']))],
          None,
      )
  return create_model(
      f'{model_cls.__name__}Optional',
      __base__=model_cls,  
      **new_fields,
  )
```

The parent fields are overridden by the ones we define.

For each field, we generate a dictionary representation of the FieldInfo instance using the asdict() method, containing the annotation, metadata and attributes.

With the following model:

```python
class Model(BaseModel):
    f: Annotated[int, Field(gt=1), WithJsonSchema({'extra': 'data'}), Field(title='F')] = 1
```

The FieldInfo instance of f will have three items in its dictionary representation:

annotation: int.
With that in mind, we can recreate an annotation that “simulates” the one from the original model:

Python 3.9 and above
Python 3.11 and above
new_annotation = Annotated[(

and specify None as a default value (the second element of the tuple for the field definition accepted by create_model()).

Here is a demonstration of our factory function:

```python
from pydantic import BaseModel, Field
class Model(BaseModel):
    a: Annotated[int, Field(gt=1)]
ModelOptional = make_fields_optional(Model)
m = ModelOptional()
print(m.a)
```
#> None

A couple notes on the implementation:

Our make_fields_optional() function is defined as returning an arbitrary Pydantic model class (-> type[BaseModel]). An alternative solution can be to use a type variable to preserve the input class:
Python 3.9 and above
Python 3.12 and above
ModelTypeT = TypeVar('ModelTypeT', bound=type[BaseModel])
However, note that static type checkers won’t be able to understand that all fields are now optional.

The experimental MISSING sentinel can be used as an alternative to None for the default values. Simply replace None by MISSING in the new annotation and default value.

You might be tempted to make a copy of the original FieldInfo instances, add a default and/or perform other mutations, to then reuse it as Annotated metadata. While this may work in some cases, it is not a supported pattern, and could break or be deprecated at any point. We strongly encourage using the pattern from this example instead.

