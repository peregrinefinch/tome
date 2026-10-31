# Reading configuration

Tome reads TOML 1.0 into a small family of typed values. It keeps table entries
in source order and returns a `TomlError` when a document cannot be read.
There is no coercion between a string, an integer and a float.

```pooka
import tome

let assert Ok(config) = tome.parse("[server]\nhost = 'localhost'\nport = 8080")
let assert Some(port) = tome.path(config, ["server", "port"])
assert tome.as_int(port) == Some(8080)
```

`get` reads one table key. `path` follows several table keys; an empty path
returns the original value. Use `at` for a zero-based array position. Missing
keys, wrong shapes and out-of-range positions return `None`.

## Reading a file

`parse` accepts text. `parse_bytes` accepts a `BitArray` and checks UTF-8 before
reading the document, including bytes hidden in comments.

```pooka
import pooka/file
import tome

let assert Ok(bytes) = file.read_bytes("settings.toml")
match tome.parse_bytes(bytes) {
  Ok(config) -> echo tome.path(config, ["server", "host"])
  Error(problem) -> echo tome.describe(problem)
}
```

Run the complete example from `examples/config` with `pooka run`.

## The values

A parsed document is always a `TTable`, behind the `Toml` base class.

| Value | Contents |
| --- | --- |
| `TString` | Decoded text |
| `TInt` | An integer, including base-prefixed integers |
| `TFloat` | A finite floating-point number |
| `TInfinity` | Whether infinity is positive |
| `TNan` | TOML's not-a-number value |
| `TBool` | A Boolean |
| `TDateTime`, `TDate`, `TTime` | The validated original spelling |
| `TArray` | A list of `Toml` values |
| `TTable` | A list of key/value pairs in source order |

Dates retain fractional precision and offsets. The permitted space between a
date and time becomes `T`; Tome does not convert time zones or truncate fractions.
Infinity and NaN have explicit classes because the BEAM does not expose them as
ordinary floating-point values.

Arrays of tables use `TArray` containing `TTable` values. Later child headers
belong to the latest table in that array. Inline tables are closed values;
later declarations cannot add fields to them.

## When a document is wrong

`TomlError` holds a one-based line and a message. `describe` joins them into a
sentence, such as `line 3: a value is missing`. LF and CRLF both count as one
newline. A byte input that cannot be decoded reports its encoding error at the
start of the document.

The parser targets [TOML 1.0](https://toml.io/en/v1.0.0). Its Pooka tests include
the upstream TOML 1.0 corpus, with expected values for valid documents, plus
accessor, ordering and error-location tests. TOML 1.1 additions are outside this
version's scope.
