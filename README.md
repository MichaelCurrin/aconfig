# aconfig
> Simple, useful and opinionated config loader

[![build-img]][build-url]
[![pkg-img]][pkg-url]
[![reportcard-img]][reportcard-url]
[![coverage-img]][coverage-url]

## Rationale

There are many solutions regarding configuration loading in Go. 

This simple config loader was created to be as easy to use and understand as possible. 

The goal was to load config values from 4 places: defaults (in the code), files, environment variables, and command-line flags. This library works with all of these sources.

## Features

* Simple API.
* Clean and tested code.
* Automatic fields mapping.
* Supports different sources:
  * defaults in the code
  * config files (JSON, YAML, TOML, DotENV, HCL)
  * environment variables
  * command-line flags
* Dependency-free (file parsers are optional).
* Ability to walk over configuration fields.

## Requirements

- Go version 1.14+

## Install

```sh
go get github.com/cristalhq/aconfig
```

## Example

```go
type MyConfig struct {
	Port int `default: "1111" usage: "Port number to serve on"`
	Auth struct {
		User string `default: "def-user"`
		Pass string `default: "def-pass"`
	}
	Pass string `default:"" env: "SECRET" flag: "secret"`
}

var cfg MyConfig
loader := aconfig.LoaderFor(&cfg, aconfig.Config{
	// feel free to skip some steps :)
	// SkipDefaults: true,
	// SkipFiles:    true,
	// SkipEnv:      true,
	// SkipFlags:    true,
	EnvPrefix:       "APP",
	FlagPrefix:      "app",
	Files:           []string{"/var/opt/myapp/config.json", "ouch.yaml"},
	FileDecoders: map[string]aconfig.FileDecoder{
		// from `aconfigyaml` submodule
		// see submodules in repo for more formats
		".yaml": aconfigyaml.New(),
	},
})

// IMPORTANT: define your own flags with `flagSet`
flagSet := loader.Flags()

if err := loader.Load(); err != nil {
	panic(err)
}

// configuration fields will be loaded from (in order):
//
// 1. defaults set in structure tags (see MyConfig defenition)
// 2. loaded from files `file.json` if not `ouch.yaml` will be used
// 3. from corresponding environment variables with the prefix `APP_`
// 4. command-line flags with the prefix `app.` if they are 
```

Also see examples: [examples_test.go](https://github.com/cristalhq/aconfig/blob/master/example_test.go).

Integration with `spf13/cobra` [playground](https://play.golang.org/p/OsCR8qTCN0H).

## Documentation

See the [online docs][pkg-url].

## License

Licensed under [MIT License](LICENSE).

[build-img]: https://github.com/cristalhq/aconfig/workflows/build/badge.svg
[build-url]: https://github.com/cristalhq/aconfig/actions
[pkg-img]: https://pkg.go.dev/badge/cristalhq/aconfig
[pkg-url]: https://pkg.go.dev/github.com/cristalhq/aconfig
[reportcard-img]: https://goreportcard.com/badge/cristalhq/aconfig
[reportcard-url]: https://goreportcard.com/report/cristalhq/aconfig
[coverage-img]: https://codecov.io/gh/cristalhq/aconfig/branch/master/graph/badge.svg
[coverage-url]: https://codecov.io/gh/cristalhq/aconfig
