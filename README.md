[![Go Actions Status](https://github.com/etam-pro/gnostic/workflows/Go/badge.svg)](https://github.com/etam-pro/gnostic/actions)

# ⨁ gnostic

This repository contains a Go command line tool which converts JSON and YAML
[OpenAPI](https://github.com/OAI/OpenAPI-Specification) descriptions to and
from equivalent Protocol Buffer representations.

[Protocol Buffers](https://developers.google.com/protocol-buffers/) provide a
language-neutral, platform-neutral, extensible mechanism for serializing
structured data. **gnostic**'s Protocol Buffer models for the OpenAPI
Specification can be used to generate code that includes data structures with
explicit fields for the elements of an OpenAPI description. This makes it
possible for developers to work with OpenAPI descriptions in type-safe ways,
which is particularly useful in strongly-typed languages like Go and
[Dart](https://dart.dev/).

**gnostic** reads OpenAPI descriptions into these generated data structures,
reports errors, resolves internal dependencies, and writes the results in a
binary form that can be used in any language that is supported by the Protocol
Buffer tools. A plugin interface simplifies integration with API tools written
in a variety of different languages, and when necessary, Protocol Buffer
OpenAPI descriptions can be reexported as JSON or YAML.

**gnostic** compilation code and OpenAPI Protocol Buffer models are
automatically generated from an
[OpenAPI JSON Schema](https://github.com/OAI/OpenAPI-Specification/blob/master/schemas/v2.0/schema.json).
Source code for the generator is in the [generate-gnostic](generate-gnostic)
directory.

## Related Repositories

[google/gnostic-models](https://github.com/etam-pro/gnostic-models) contains a
lightweight distribution of the protobuf models generated by this project.
Where a low-dependency integration of just these models is needed, Go projects
can import packages from `gnostic-models` instead of `gnostic`.

[google/gnostic-grpc](https://github.com/etam-pro/gnostic-grpc) contains a
gnostic plugin that can generate an annotated Protocol Buffer description of an
API that, when transcode, produces an API that conforms to a specified OpenAPI
document. To go from protobuf to OpenAPI, see the
[protoc-gen-openapi](cmd/protoc-gen-openapi) tool in this project.

[google/gnostic-go-generator](https://github.com/etam-pro/gnostic-go-generator)
contains an experimental gnostic plugin that generates a Go client for an API
described by a specified OpenAPI document.

## Disclaimer

Feedback and contributions are welcome! Until there is a 1.0 release, please
consider this prerelease software and work in progress. To ensure stable
builds, we request that dependent projects always refer to tagged releases of
**gnostic**.

## Requirements

**gnostic** can be run in any environment that supports [Go](http://golang.org)
and the
[Protocol Buffer Compiler](https://github.com/protocolbuffers/protobuf).

## Installation and Getting Started

The following instructions are for installing **gnostic** using
[Go modules](https://blog.golang.org/using-go-modules), supported by Go 1.11
and later.

1.  Get this package by downloading it with `git clone`.

        git clone https://github.com/etam-pro/gnostic
        cd gnostic

2.  Verify that you have a local installation of `protoc`. You can get protoc
    [here](https://github.com/protocolbuffers/protobuf).

3.  Build **gnostic** with `make`. This uses
    [go generate](https://blog.golang.org/generate) to build support code
    including code generated by `protoc` and the Go protoc plugin, which is
    automatically downloaded from
    [github.com/golang/protobuf](https://github.com/golang/protobuf) by the
    [COMPILE-PROTOS.sh](COMPILE-PROTOS.sh) script. This also builds all plugins
    and associated tools in this repo.

4.  Verify **gnostic** with `make test`. These tests are run by **gnostic**'s
    continuous integration, so you should expect them to pass for all release
    versions.

5.  Run **gnostic**. This sample invocation creates a file in the current
    directory named `petstore.pb` that contains a binary Protocol Buffer
    description of a sample API.

            gnostic --pb-out=. examples/v2.0/json/petstore.json

6.  You can also compile files that you specify with a URL. Here's another way
    to compile the previous example. This time we're creating `petstore.text`,
    which contains a textual representation of the Protocol Buffer description.
    This is mainly for use in testing and debugging.

            gnostic --text-out=petstore.text https://raw.githubusercontent.com/google/gnostic/master/examples/v2.0/json/petstore.json

7.  For a sample application, see apps/report. This reads a binary Protocol
    Buffer encoding created by **gnostic**.

        go install ./apps/report ## automatically installed by the top-level Makefile
        report petstore.pb

8.  **gnostic** also supports plugins. **gnostic**'s plugin interface is
    modeled on `protoc`'s
    [plugin.proto](https://github.com/protocolbuffers/protobuf/blob/master/src/google/protobuf/compiler/plugin.proto)
    and is described in [plugins/plugin.proto](plugins/plugin.proto). Several
    plugins are implemented in the `plugins` directory. Others, like
    [gnostic-grpc](https://github.com/etam-pro/gnostic-grpc) and
    [gnostic-go-generator](https://github.com/etam-pro/gnostic-go-generator),
    are published in their own repositories. One such plugin is
    [gnostic-vocabulary](plugins/gnostic-vocabulary), which produces a summary
    of the word usage in an APIs interfaces. You can run `gnostic-vocabulary`
    with the following:

            gnostic examples/v2.0/json/petstore.json --vocabulary_out=.

    This will produce files named `vocabulary.pb` and `vocabulary.json` in
    `examples/v2.0/json`. For the format of `vocabulary.pb`, see
    [metrics/vocabulary.proto](metrics/vocabulary.proto).

9.  [Optional] A large part of **gnostic** is automatically-generated by the
    [generate-gnostic](generate-gnostic) tool. This uses JSON schemas to
    generate Protocol Buffer language files that describe supported API
    specification formats and Go-language files of code that will read JSON or
    YAML API descriptions into the generated protocol buffer models.
    Pre-generated versions of these files are checked into the
    [openapiv2](openapiv2), [openapiv3](openapiv3), and [discovery](discovery)
    directories. You can regenerate this code with the following:

        go install ./generate-gnostic
        generate-gnostic --v2
        generate-gnostic --v3
        generate-gnostic --discovery

## Copyright

Copyright 2017-2020, Google LLC.

## License

Released under the Apache 2.0 license.
