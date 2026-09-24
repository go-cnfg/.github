# go-cnfg

**Dead simple zero dependency config parser for Go.**

A plain struct, your defaults, and the sources to read. Sources apply in the order you give them, so the last one wins.

[**Website**](https://go-cnfg.github.io/website) · [godoc](https://pkg.go.dev/github.com/go-cnfg/cnfg)

## Repositories

| Repo | What |
|---|---|
| [`cnfg`](https://github.com/go-cnfg/cnfg) | The parser. Config files, env vars, flags, drop-in directories, `Require`. Standard library only. |
| [`validator`](https://github.com/go-cnfg/validator) | [go-playground/validator](https://github.com/go-playground/validator) as a cnfg source, so rules live in struct tags. |

## Why cnfg

- **A plain struct.** Field names become `-listen-addr`, `LISTEN_ADDR` and `listen-addr` on their own. Nested structs join with a dash. A `usage` tag is all the flag listing needs.
- **Sources in order.** `File`, `Glob`, `Env`, `Flags` and your own `Func` are all a `cnfg.Source`. The last one wins, and a field no source touched keeps its default.
- **Zero dependencies.** A file format is the decoder you pass in, `json.Unmarshal`, `yaml.Unmarshal`, `toml.Unmarshal`, so the format library stays a dependency of your program.
- **Strict when you want it.** Every source has a `Strict` twin that fails on unknown keys. `Require` fails on a required field still at its zero value.

## Quick start

```go
package main

import (
	"encoding/json"
	"log"
	"time"

	"github.com/go-cnfg/cnfg"
)

type Config struct {
	Addr    string        `usage:"address to listen on"`
	Timeout time.Duration `usage:"request timeout"`
	Debug   bool          `usage:"enable debug logging"`
}

func main() {
	cfg := cnfg.MustParse(Config{
		Addr:    ":8080",
		Timeout: 5 * time.Second,
	},
		cnfg.File(json.Unmarshal, "/etc/app/config.json"),
		cnfg.Env("APP"),
		cnfg.Flags(),
	)

	log.Printf("%+v", cfg)
}
```

```sh
go get github.com/go-cnfg/cnfg@latest
```

For drop-in directories, a config file named by a flag, strict sources and validation, see [go-cnfg.github.io/website](https://go-cnfg.github.io/website).
