---
title: "Why Pydantic"

# Why Pydantic
> 原始文档来源：https://pydantic.dev/docs/validation/latest/get-started/why/

---
Why use Pydantic

Today, Pydantic is downloaded many times a month and used by some of the largest and most recognisable organisations in the world.

It’s hard to know why so many people have adopted Pydantic since its inception six years ago, but here are a few guesses.

Type hints powering schema validation 

The schema that Pydantic validates against is generally defined by Python type hints.

Type hints are great for this since, if you’re writing modern Python, you already know how to use them. Using type hints also means that Pydantic integrates well with static typing tools (like mypy and Pyright) and IDEs (like PyCharm and VSCode).

Example - just type hints
```python
from typing import Annotated, Literal
from annotated_types import Gt
from pydantic import BaseModel
class Fruit(BaseModel):
  name: str  
  color: Literal['red', 'green']  
  weight: Annotated[float, Gt(0)]  
  bazam: dict[str, list[tuple[int, bool, float]]]  
print(
  Fruit(
      name='Apple',
      color='red',
      weight=4.2,
      bazam={'foobar': [(1, True, 0.1)]},
  )
```
)
#> name='Apple' color='red' weight=4.2 bazam={'foobar': [(1, True, 0.1)]}

Learn more

See the documentation on supported types.

Performance

Pydantic’s core validation logic is implemented in a separate package (pydantic-core), where validation for most types is implemented in Rust.

As a result, Pydantic is among the fastest data validation libraries for Python.

Performance Example - Pydantic vs. dedicated code

Unlike other performance-centric libraries written in compiled languages, Pydantic also has excellent support for customizing validation via functional validators.

Learn more

Samuel Colvin’s talk at PyCon 2023 explains how pydantic-core works and how it integrates with Pydantic.

Serialization

Pydantic provides functionality to serialize model in three ways:

To a Python dict made up of the associated Python objects.
To a Python dict made up only of “jsonable” types.
To a JSON string.

In all three modes, the output can be customized by excluding specific fields, excluding unset fields, excluding default values, and excluding None values.

Example - Serialization 3 ways

Learn more

See the documentation on serialization.

JSON Schema

A JSON Schema can be generated for any Pydantic schema — allowing self-documenting APIs and integration with a wide variety of tools which support the JSON Schema format.

Example - JSON Schema

Pydantic is compliant with the latest version of JSON Schema specification (2020-12), which is compatible with OpenAPI 3.1.

Learn more

See the documentation on JSON Schema.

Strict mode and data coercion 

By default, Pydantic is tolerant to common incorrect types and coerces data to the right type — e.g. a numeric string passed to an int field will be parsed as an int.

Pydantic also has as strict mode, where types are not coerced and a validation error is raised unless the input data exactly matches the expected schema.

But strict mode would be pretty useless when validating JSON data since JSON doesn’t have types matching many common Python types like datetime, UUID or bytes.

To solve this, Pydantic can parse and validate JSON in one step. This allows sensible data conversion (e.g. when parsing strings into datetime objects). Since the JSON parsing is implemented in Rust, it’s also very performant.

Example - Strict mode that's actually useful

Learn more

See the documentation on strict mode.

Dataclasses, TypedDicts, and more 

Pydantic provides four ways to create schemas and perform validation and serialization:

BaseModel — Pydantic’s own super class with many common utilities available via instance methods.
Pydantic dataclasses — a wrapper around standard dataclasses with additional validation performed.
TypeAdapter — a general way to adapt any type for validation and serialization. This allows types like TypedDict and NamedTuple to be validated as well as simple types (like int or timedelta) — all types supported can be used with TypeAdapter.
validate_call — a decorator to perform validation when calling a function.
Example - schema based on a TypedDict
Customisation

Functional validators and serializers, as well as a powerful protocol for custom types, means the way Pydantic operates can be customized on a per-field or per-type basis.

Customisation Example - wrap validators

Learn more

See the documentation on validators, custom serializers, and custom types.

Ecosystem

At the time of writing there are 466,400 repositories on GitHub and 8,119 packages on PyPI that depend on Pydantic.

Some notable libraries that depend on Pydantic:

huggingface/transformers 138,570 stars
hwchase17/langchain 99,542 stars
tiangolo/fastapi 80,497 stars
apache/airflow 38,577 stars
lm-sys/FastChat 37,650 stars
microsoft/DeepSpeed 36,521 stars
OpenBB-finance/OpenBBTerminal 35,971 stars
gradio-app/gradio 35,740 stars
ray-project/ray 35,176 stars
pola-rs/polars 31,698 stars
Lightning-AI/lightning 28,902 stars
mindsdb/mindsdb 27,141 stars
embedchain/embedchain 24,379 stars
pynecone-io/reflex 21,558 stars
heartexlabs/label-studio 20,571 stars
Sanster/lama-cleaner 20,313 stars
mlflow/mlflow 19,393 stars
RasaHQ/rasa 19,337 stars
spotDL/spotify-downloader 18,604 stars
chroma-core/chroma 17,393 stars
airbytehq/airbyte 17,120 stars
openai/evals 15,437 stars
tiangolo/sqlmodel 15,127 stars
ydataai/ydata-profiling 12,687 stars
pyodide/pyodide 12,653 stars
dagster-io/dagster 12,440 stars
PaddlePaddle/PaddleNLP 12,312 stars
matrix-org/synapse 11,857 stars
lucidrains/DALLE2-pytorch 11,207 stars
great-expectations/great_expectations 10,164 stars
modin-project/modin 10,002 stars
aws/serverless-application-model 9,402 stars
sqlfluff/sqlfluff 8,535 stars
replicate/cog 8,344 stars
autogluon/autogluon 8,326 stars
lucidrains/imagen-pytorch 8,164 stars
brycedrennan/imaginAIry 8,050 stars
vitalik/django-ninja 7,685 stars
NVlabs/SPADE 7,632 stars
bridgecrewio/checkov 7,340 stars
bentoml/BentoML 7,322 stars
skypilot-org/skypilot 7,113 stars
apache/iceberg 6,853 stars
deeppavlov/DeepPavlov 6,777 stars
PrefectHQ/marvin 5,454 stars
NVIDIA/NeMo-Guardrails 4,383 stars
microsoft/FLAML 4,035 stars
jina-ai/discoart 3,846 stars
docarray/docarray 3,007 stars
aws-powertools/powertools-lambda-python 2,980 stars
roman-right/beanie 2,172 stars
art049/odmantic 1,096 stars

More libraries using Pydantic can be found at Kludex/awesome-pydantic.

Organisations using Pydantic 

Some notable companies and organisations using Pydantic together with comments on why/how we know they’re using Pydantic.

The organisations below are included because they match one or more of the following criteria:

Using Pydantic as a dependency in a public repository.
Referring traffic to the Pydantic documentation site from an organization-internal domain — specific referrers are not included since they’re generally not in the public domain.
Direct communication between the Pydantic team and engineers employed by the organization about usage of Pydantic within the organization.

We’ve included some extra detail where appropriate and already in the public domain.

Adobe 

adobe/dy-sql uses Pydantic.

Amazon and AWS 
powertools-lambda-python
awslabs/gluonts
AWS sponsored Samuel Colvin $5,000 to work on Pydantic in 2022
Anthropic 

anthropics/anthropic-sdk-python uses Pydantic.

Apple 

(Based on the criteria described above)

ASML 

(Based on the criteria described above)

AstraZeneca 

Multiple repos in the AstraZeneca GitHub org depend on Pydantic.

Cisco Systems 
Pydantic is listed in their report of Open Source Used In RADKit.
cisco/webex-assistant-sdk
Capital One 

(Based on the criteria described above)

Comcast 

(Based on the criteria described above)

Datadog 
Extensive use of Pydantic in DataDog/integrations-core and other repos
Communication with engineers from Datadog about how they use Pydantic.
Facebook 

Multiple repos in the facebookresearch GitHub org depend on Pydantic.

GitHub 

GitHub sponsored Pydantic $750 in 2022

Google 

Extensive use of Pydantic in google/turbinia and other repos.

HSBC 

(Based on the criteria described above)

IBM 

Multiple repos in the IBM GitHub org depend on Pydantic.

Intel 

(Based on the criteria described above)

Intuit 

(Based on the criteria described above)

Intergovernmental Panel on Climate Change 

Tweet explaining how the IPCC use Pydantic.

JPMorgan 

(Based on the criteria described above)

Jupyter 
The developers of the Jupyter notebook are using Pydantic for subprojects
Through the FastAPI-based Jupyter server Jupyverse
FPS’s configuration management.
Microsoft 
DeepSpeed deep learning optimisation library uses Pydantic extensively
Multiple repos in the microsoft GitHub org depend on Pydantic, in particular their
Pydantic is also used in the Azure GitHub org
Comments on GitHub show Microsoft engineers using Pydantic as part of Windows and Office
Molecular Science Software Institute 

Multiple repos in the MolSSI GitHub org depend on Pydantic.

NASA 

Multiple repos in the NASA GitHub org depend on Pydantic.

NASA are also using Pydantic via FastAPI in their JWST project to process images from the James Webb Space Telescope, see this tweet.

Netflix 

Multiple repos in the Netflix GitHub org depend on Pydantic.

NSA 

The nsacyber/WALKOFF repo depends on Pydantic.

NVIDIA 

Multiple repositories in the NVIDIA GitHub org depend on Pydantic.

Their “Omniverse Services” depends on Pydantic according to their documentation.

OpenAI 

OpenAI use Pydantic for their ChatCompletions API, as per this discussion on GitHub.

Anecdotally, OpenAI use Pydantic extensively for their internal services.

Oracle 

(Based on the criteria described above)

Palantir 

(Based on the criteria described above)

Qualcomm 

(Based on the criteria described above)

Red Hat 

(Based on the criteria described above)

Revolut 

Anecdotally, all internal services at Revolut are built with FastAPI and therefore Pydantic.

Robusta 

The robusta-dev/robusta repo depends on Pydantic.

Salesforce 

Salesforce sponsored Samuel Colvin $10,000 to work on Pydantic in 2022.

Starbucks 

(Based on the criteria described above)

Texas Instruments 

(Based on the criteria described above)

Twilio 

(Based on the criteria described above)

Twitter 

Twitter’s the-algorithm repo where they open sourced their recommendation engine uses Pydantic.

UK Home Office 

(Based on the criteria described above)

