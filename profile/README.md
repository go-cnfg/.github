## Quick start - More info at [go-cnfg.github.io/website](https://go-cnfg.github.io/website)

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
