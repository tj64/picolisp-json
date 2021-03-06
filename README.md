# JSON Encoder/Decoder for PicoLisp

[![GitHub release](https://img.shields.io/github/release/aw/picolisp-json.svg)](https://github.com/aw/picolisp-json) [![Build Status](https://travis-ci.org/aw/picolisp-json.svg?branch=master)](https://travis-ci.org/aw/picolisp-json) [![Dependency](https://img.shields.io/badge/[deps] Parson-18761d99ff-ff69b4.svg)](https://github.com/aw/parson) [![Dependency](https://img.shields.io/badge/[deps] picolisp--unit-v1.0.0-ff69b4.svg)](https://github.com/aw/picolisp-unit.git)

This library can be used to parse and serialize (encode/decode) JSON strings in [PicoLisp](http://picolisp.com/).

![picolisp-json](https://cloud.githubusercontent.com/assets/153401/6571543/56e31e44-c701-11e4-99f0-c2c51fd8061b.png)

Please read [EXPLAIN.md](EXPLAIN.md) to learn more about PicoLisp and this JSON library.

  1. [Requirements](#requirements)
  2. [Getting Started](#getting-started)
  3. [Usage](#usage)
  4. [Examples](#examples)
  5. [Testing](#testing)
  6. [Alternatives](#alternatives)
  7. [Contributing](#contributing)
  8. [License](#license)

# Requirements

  * PicoLisp 64-bit v3.1.9+
  * Git
  * UNIX/Linux development/build tools (gcc, make/gmake, etc..)

# Getting Started

These FFI bindings require the [Parson C library](https://github.com/kgabis/parson), compiled as a shared library.

  1. Type `make` to pull and compile the _Parson C Library_.
  2. Include `json.l` in your project
  3. Try the [examples](#examples) below

### Linking and Paths

Once compiled, the shared library is symlinked as:

    .lib/libparson.so -> .modules/parson/HEAD/libparson.so

The `json.l` file searches for `.lib/libparson.so`, relative to its current directory.

### Updating

To keep everything updated, type:

    git pull && make clean && make

# Usage

Only the following functions are exported publicly, and namespaced with `(symbols 'json)` (or the prefix: `json~`):

  * `(decode arg1 arg2)` parses a JSON string or file
    - `arg1` _String_: the JSON string or filename you want to decode
    - `arg2` _Flag (optional)_: a flag (`T` or `NIL`) indicating to parse a file if set
  * `(encode arg1)` serializes a list into a JSON string
    - `arg1` _List_: a PicoLisp list which will be converted to a JSON string

> **Note:** These functions are not namespace [local symbols](http://software-lab.de/doc/refL.html#local), which means they would redefine symbols with the same name in the `'pico` namespace.

### JSON-PicoLisp data type table

| JSON | PicoLisp | Example |
| ---- | -------- | ------- |
| Number | Number | `25 <-> 25` |
| String | String | `"hello" <-> "hello"` |
| Null | Transient _null_ Symbol | `null <-> 'null` |
| Boolean | Transient _true_ or _false_ Symbol | `true <-> 'true` |
| Array | List with T in cdar | `{"array":[1,2,3]} <-> '(("array" T 1 2 3))` |
| Object | Cons pair | `{"hello":"world"} <-> '(("hello" . "world"))` |

### Notes

  * A successful result will return a list. Failures return `NIL`.
  * Keys are in `car`, values are in `cdr`.
  * When the 2nd item in the list is `T`, the rest of the list represents a JSON array.
  * When the 2nd item in the list is a cons pair, it represents a JSON object.

# Examples

### (decode String)

```lisp
pil +

(load "json.l")

(symbols 'json)

(decode "{\"Hello\":\"World\"}")

-> (("Hello" . "World"))
```

### (decode Filename T)

The same function is used for parsing JSON strings and files.
Simply append `T` as the last argument if you want to parse a file.

```lisp
pil +

(load "json.l")

(symbols 'json)

(decode "test.json" T)

-> (("first" . "John")
    ("last" . "Doe")
    ("age" . 25)
    ("registered" . true)
    ("interests" T "Reading" "Mountain Biking")
    ("favorites" ("color" . "blue") ("sport" . "running"))
    ("utf string" . "lorem ipsum")
    ("utf-8 string" . "あいうえお")
    ("surrogate string" . "lorem�ipsum�lorem") )
```

### (encode List)

```lisp

pil +

(load "json.l")

(symbols 'json)

(encode '(("Hello" . "World")))

-> "{\"Hello\":\"World\"}"
```

# Testing

This library now comes with full [unit tests](https://github.com/aw/picolisp-unit). To run the tests, type:

    make check

# Alternatives

The following are alternatives written in pure PicoLisp. They are limited by pipe/read syscalls.

* [JSON reader/writer](http://rosettacode.org/wiki/JSON#PicoLisp) by Alexander Burger.
* [JSON reader/writer](https://bitbucket.org/hsarvell/ext/src/9d6e5a15c5ce7cb47033e0082ef70aee6c4c8dd7/json.l?at=default) by Henrik Sarvell.

# Contributing

If you find any bugs or issues, please [create an issue](https://github.com/aw/picolisp-json/issues/new).

If you want to improve this library, please make a pull-request.

# License

[MIT License](LICENSE)

Copyright (c) 2015 Alexander Williams, Unscramble <license@unscramble.jp>
