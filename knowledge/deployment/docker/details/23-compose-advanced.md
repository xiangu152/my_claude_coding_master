---
title: "Compose 高级特性"
source: "https://docs.docker.com/reference/compose-file/merge/"
version: "latest"
---

# Compose 高级特性

> 原始文档来源：https://docs.docker.com/reference/compose-file/merge/

---

Compose file reference

Merge
Merge Compose files

Compose lets you define a Compose application model through multiple Compose files. When doing so, Compose follows certain rules to merge Compose files.

These rules are outlined below.

Mapping

A YAML mapping gets merged by adding missing entries and merging the conflicting ones.

Merging the following example YAML trees:

services:

  foo:

    key1: value1

    key2: value2
services:

  foo:

    key2: VALUE

    key3: value3

Results in a Compose application model equivalent to the YAML tree:

services:

  foo:

    key1: value1

    key2: VALUE

    key3: value3
Sequence

A YAML sequence is merged by appending values from the overriding Compose file to the previous one.

Merging the following example YAML trees:

services:

  foo:

    DNS:

      - 1.1.1.1
services:

  foo:

    DNS: 

      - 8.8.8.8

Results in a Compose application model equivalent to the YAML tree:

services:

  foo:

    DNS:

      - 1.1.1.1

      - 8.8.8.8
Exceptions
Shell commands

When merging Compose files that use the services attributes command, entrypoint and healthcheck: test, the value is overridden by the latest Compose file, and not appended.

Merging the following example YAML trees:

services:

  foo:

    command: ["echo", "foo"]
services:

  foo:

    command: ["echo", "bar"]

Results in a Compose application model equivalent to the YAML tree:

services:

  foo:

    command: ["echo", "bar"]
Unique resources

Applies to the ports, volumes, secrets and configs services attributes. While these types are modeled in a Compose file as a sequence, they have special uniqueness requirements:

Attribute	Unique key
volumes	target
secrets	target
configs	target
ports	{ip, target, published, protocol}

When merging Compose files, Compose appends new entries that do not violate a uniqueness constraint and merge entries that share a unique key.

Merging the following example YAML trees:

services:

  foo:

    volumes:

      - foo:/work
services:

  foo:

    volumes:

      - bar:/work

Results in a Compose application model equivalent to the YAML tree:

services:

  foo:

    volumes:

      - bar:/work
Reset value

In addition to the previously described mechanism, an override Compose file can also be used to remove elements from your application model. For this purpose, the custom YAML tag !reset can be set to override a value set by the overridden Compose file. A valid value for attribute must be provided, but will be ignored and target attribute will be set with type's default value or null.

For readability, it is recommended to explicitly set the attribute value to the null (null) or empty array [] (with !reset null or !reset []) so that it is clear that resulting attribute will be cleared.

A base compose.yaml file:

services:

  app:

    image: myapp

    ports:

      - "8080:80" 

    environment:

      FOO: BAR           

And a compose.override.yaml file:

services:

  app:

    image: myapp

    ports: !reset []

    environment:

      FOO: !reset null

Results in:

services:

  app:

    image: myapp
Replace value
Requires:
Docker Compose 2.24.4 and later

While !reset can be used to remove a declaration from a Compose file using an override file, !override allows you to fully replace an attribute, bypassing the standard merge rules. A typical example is to fully replace a resource definition, to rely on a distinct model but using the same name.

A base compose.yaml file:

services:

  app:

    image: myapp

    ports:

      - "8080:80"

To remove the original port, but expose a new one, the following override file is used:

services:

  app:

    ports: !override

      - "8443:443" 

This results in:

services:

  app:

    image: myapp

    ports:

      - "8443:443" 

If !override had not been used, both 8080:80 and 8443:443 would be exposed as per the merging rules outlined above.

Additional resources

For more information on how merge can be used to create a composite Compose file, see Working with multiple Compose files

---

Compose file reference

Fragments
Fragments

With Compose, you can use built-in YAML features to make your Compose file neater and more efficient. Anchors and aliases let you create reusable blocks. This is useful if you start to find common configurations that span multiple services. Having reusable blocks minimizes potential mistakes.

Anchors are created using the & sign. The sign is followed by an alias name. You can use this alias with the * sign later to reference the value following the anchor. Make sure there is no space between the & and the * characters and the following alias name.

You can use more than one anchor and alias in a single Compose file.

Example 1
volumes:

  db-data: &default-volume

    driver: default

  metrics: *default-volume

In the example above, a default-volume anchor is created based on the db-data volume. It is later reused by the alias *default-volume to define the metrics volume.

Anchor resolution takes place before variables interpolation, so variables can't be used to set anchors or aliases.

Example 2
services:

  first:

    image: my-image:latest

    environment: &env

      - CONFIG_KEY

      - EXAMPLE_KEY

      - DEMO_VAR

  second:

    image: another-image:latest

    environment: *env

If you have an anchor that you want to use in more than one service, use it in conjunction with an extension to make your Compose file easier to maintain.

Example 3

You may want to partially override values. Compose follows the rule outlined by YAML merge type.

In the following example, metrics volume specification uses alias to avoid repetition but overrides name attribute:

services:

  backend:

    image: example/database

    volumes:

      - db-data

      - metrics

volumes:

  db-data: &default-volume

    driver: default

    name: "data"

  metrics:

    <<: *default-volume

    name: "metrics"
Example 4

You can also extend the anchor to add additional values.

services:

  first:

    image: my-image:latest

    environment: &env

      FOO: BAR

      ZOT: QUIX

  second:

    image: another-image:latest

    environment:

      <<: *env

      YET_ANOTHER: VARIABLE
Note

YAML merge only applies to mappings, and can't be used with sequences.

In example above, the environment variables must be declared using the FOO: BAR mapping syntax, while the sequence syntax - FOO=BAR is only valid when no fragments are involved.

---

Compose file reference

Extensions
Extensions

Extensions can be used to make your Compose file more efficient and easier to maintain.

Use the prefix x- as a top-level element to modularize configurations that you want to reuse. Compose ignores any fields that start with x-, this is the sole exception where Compose silently ignores unrecognized fields.

Extensions can also be used with anchors and aliases.

They also can be used within any structure in a Compose file where user-defined keys are not expected. Compose uses those to enable experimental features, the same way browsers add support for custom CSS features

Example 1
x-custom:

  foo:

    - bar

    - zot

services:

  webapp:

    image: example/webapp

    x-foo: bar
service:

  backend:

    deploy:

      placement:

        x-aws-role: "arn:aws:iam::XXXXXXXXXXXX:role/foo"

        x-aws-region: "eu-west-3"

        x-azure-region: "france-central"
Example 2
x-env: &env

  environment:

    - CONFIG_KEY

    - EXAMPLE_KEY

 

services:

  first:

    <<: *env

    image: my-image:latest

  second:

    <<: *env

    image: another-image:latest

In this example, the environment variables do not belong to either of the services. They’ve been lifted out completely into the x-env extension field. This defines a new node which contains the environment field. The &env YAML anchor is used so both services can reference the extension field’s value as *env.

Example 3
x-function: &function

 labels:

   function: "true"

 depends_on:

   - gateway

 networks:

   - functions

 deploy:

   placement:

     constraints:

       - 'node.platform.os == linux'

services:

 # Node.js gives OS info about the node (Host)

 nodeinfo:

   <<: *function

   image: functions/nodeinfo:latest

   environment:

     no_proxy: "gateway"

     https_proxy: $https_proxy

 # Uses `cat` to echo back response, fastest function to execute.

 echoit:

   <<: *function

   image: functions/alpine:health

   environment:

     fprocess: "cat"

     no_proxy: "gateway"

     https_proxy: $https_proxy

The nodeinfo and echoit services both include the x-function extension via the &function anchor, then set their specific image and environment.

Example 4

Using YAML merge it is also possible to use multiple extensions and share and override additional attributes for specific needs:

x-environment: &default-environment

  FOO: BAR

  ZOT: QUIX

x-keys: &keys

  KEY: VALUE

services:

  frontend:

    image: example/webapp

    environment: 

      << : [*default-environment, *keys]

      YET_ANOTHER: VARIABLE
Note

YAML merge only applies to mappings, and can't be used with sequences.

In the example above, the environment variables are declared using the FOO: BAR mapping syntax, while the sequence syntax - FOO=BAR is only valid when no fragments are involved.

Informative historical notes

This section is informative. At the time of writing, the following prefixes are known to exist:

Prefix	Vendor/Organization
docker	Docker
kubernetes	Kubernetes
Specifying byte values

Values express a byte value as a string in {amount}{byte unit} format: The supported units are b (bytes), k or kb (kilo bytes), m or mb (mega bytes) and g or gb (giga bytes).

    2b

    1024kb

    2048k

    300m

    1gb
Specifying durations

Values express a duration as a string in the form of {value}{unit}. The supported units are us (microseconds), ms (milliseconds), s (seconds), m (minutes) and h (hours). Values can combine multiple values without separator.

  10ms

  40s

  1m30s

  1h5m30s20ms

---
