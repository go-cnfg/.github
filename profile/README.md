# go-cnfg

**Dead simple zero dependency config parser for Go.**

A plain struct, your defaults, and the sources to read. The last source wins.

[**Website**](https://go-cnfg.github.io/website) · [godoc](https://pkg.go.dev/github.com/go-cnfg/cnfg)

## Repositories

| Repo | What |
|---|---|
| [`cnfg`](https://github.com/go-cnfg/cnfg) | The parser. Files, env vars, flags, drop-in directories, `Require`. Standard library only. |
| [`validator`](https://github.com/go-cnfg/validator) | go-playground/validator as a cnfg source, rules in struct tags. |

## Why cnfg

- **A plain struct.** Field names become `-listen-addr` and `LISTEN_ADDR` on their own.
- **Sources in order.** `File`, `Env`, `Flags` and your own `Func` are all a `cnfg.Source`. The last one wins.
- **Zero dependencies.** A file format is the decoder you pass in, so the format library stays yours.
- **Strict when you want it.** `Strict` twins fail on unknown keys, `Require` on empty required fields.

## Quick start

```go
package main

import (
	"encoding/json"
	"log"
	"time"

	"github.com/go-cnfg/cnfg"
	"github.com/go-cnfg/validator"
)

type Config struct {
	Addr    string        `usage:"address to listen on" validate:"required,hostname_port"`
	Timeout time.Duration `usage:"request timeout" validate:"min=1s"`
	Debug   bool          `usage:"enable debug logging"`
}

func main() {
	defaults := Config{
		Addr:    ":8080",
		Timeout: 5 * time.Second,
	}

	cfg := cnfg.MustParse(defaults,
		cnfg.File(json.Unmarshal, "/etc/app/config.json"),
		cnfg.Env("APP"),
		cnfg.Flags(),
		validator.Validate(),
	)

	log.Printf("%+v", cfg)
}
```

```sh
go get github.com/go-cnfg/cnfg@latest
```

For drop-in directories, a config file named by a flag and strict sources, see the [website](https://go-cnfg.github.io/website).
