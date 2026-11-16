# tome

[![Package Version](https://img.shields.io/hexpm/v/tome?style=for-the-badge&color=75618E)](https://hex.pm/packages/tome)
[![Hex Docs](https://img.shields.io/website?url=https%3A%2F%2Ftome.hexdocs.pm%2F&style=for-the-badge&label=hex&up_message=docs&up_color=FF891F&down_message=not%20found&down_color=lightgrey)](https://tome.hexdocs.pm/)
[![Tests](https://img.shields.io/github/actions/workflow/status/peregrinefinch/tome/test.yml?style=for-the-badge&label=tests)](https://github.com/peregrinefinch/tome/actions/workflows/test.yml)

A TOML 1.0 parser for Pooka.

```sh
pooka add tome
```

```pooka
module main

import tome

main() {
  let assert Ok(config) = tome.parse("answer = 42")
  assert tome.get(config, "answer") == Some(tome.TInt(42))
}
```

Tables retain their written order. Parse failures include a line number. The TOML 1.0 conformance fixtures retain their upstream MIT licence in `test/fixtures/toml/`.

See the [API documentation](https://tome.hexdocs.pm/) for the rest.

## Licence

Licensed under the [Apache License, Version 2.0](LICENSE).
