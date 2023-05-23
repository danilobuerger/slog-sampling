
# Slog sampling policy

[![tag](https://img.shields.io/github/tag/samber/slog-sampling.svg)](https://github.com/samber/slog-sampling/releases)
![Go Version](https://img.shields.io/badge/Go-%3E%3D%201.20.3-%23007d9c)
[![GoDoc](https://godoc.org/github.com/samber/slog-sampling?status.svg)](https://pkg.go.dev/github.com/samber/slog-sampling)
![Build Status](https://github.com/samber/slog-sampling/actions/workflows/test.yml/badge.svg)
[![Go report](https://goreportcard.com/badge/github.com/samber/slog-sampling)](https://goreportcard.com/report/github.com/samber/slog-sampling)
[![Coverage](https://img.shields.io/codecov/c/github/samber/slog-sampling)](https://codecov.io/gh/samber/slog-sampling)
[![Contributors](https://img.shields.io/github/contributors/samber/slog-sampling)](https://github.com/samber/slog-sampling/graphs/contributors)
[![License](https://img.shields.io/github/license/samber/slog-sampling)](./LICENSE)

A middleware that samples incoming records which caps the CPU and I/O load of logging while attempting to preserve a representative subset of your logs.

Sampling fixes throughput by dropping repetitive log entries.

**See also:**

- [slog-multi](https://github.com/samber/slog-multi): `slog.Handler` chaining, fanout, routing, failover, load balancing...
- [slog-formatter](https://github.com/samber/slog-formatter): `slog` attribute formatting
- [slog-sampling](https://github.com/samber/slog-sampling): `slog` sampling policy
- [slog-gin](https://github.com/samber/slog-gin): Gin middleware for `slog` logger
- [slog-echo](https://github.com/samber/slog-echo): Echo middleware for `slog` logger
- [slog-fiber](https://github.com/samber/slog-fiber): Fiber middleware for `slog` logger
- [slog-datadog](https://github.com/samber/slog-datadog): A `slog` handler for `Datadog`
- [slog-rollbar](https://github.com/samber/slog-rollbar): A `slog` handler for `Rollbar`
- [slog-sentry](https://github.com/samber/slog-sentry): A `slog` handler for `Sentry`
- [slog-syslog](https://github.com/samber/slog-syslog): A `slog` handler for `Syslog`
- [slog-logstash](https://github.com/samber/slog-logstash): A `slog` handler for `Logstash`
- [slog-fluentd](https://github.com/samber/slog-fluentd): A `slog` handler for `Fluentd`
- [slog-graylog](https://github.com/samber/slog-graylog): A `slog` handler for `Graylog`
- [slog-loki](https://github.com/samber/slog-loki): A `slog` handler for `Loki`
- [slog-slack](https://github.com/samber/slog-slack): A `slog` handler for `Slack`
- [slog-telegram](https://github.com/samber/slog-telegram): A `slog` handler for `Telegram`
- [slog-mattermost](https://github.com/samber/slog-mattermost): A `slog` handler for `Mattermost`
- [slog-microsoft-teams](https://github.com/samber/slog-microsoft-teams): A `slog` handler for `Microsoft Teams`
- [slog-webhook](https://github.com/samber/slog-webhook): A `slog` handler for `Webhook`
- [slog-kafka](https://github.com/samber/slog-kafka): A `slog` handler for `Kafka`

## 🚀 Install

```sh
go get github.com/samber/slog-sampling
```

**Compatibility**: go >= 1.20.3

This library is v0 and follows SemVer strictly. On `slog` final release (go 1.21), this library will go v1.

No breaking changes will be made to exported APIs before v1.0.0.

## 💡 Usage

GoDoc: [https://pkg.go.dev/github.com/samber/slog-sampling](https://pkg.go.dev/github.com/samber/slog-sampling)

The sampling middleware can be used standalone or with `slog-multi` helper.

### Handler options

```go
type Option struct {
	// This will log the first `First` log entries with the same level and message
	// in a `Tick` interval as-is. Following that, it will allow through
	// every `Thereafter`th log entry with the same level and message in that interval.
	Tick       time.Duration
	First      uint64
	Thereafter uint64

    // Optional hooks
	OnAccepted func(context.Context, slog.Record)
	OnDropped  func(context.Context, slog.Record)
}
```

If `Thereafter` is zero, the middleware will drop all log entries after the first `First` records in that interval.

### Using `slog-multi`

```go
import (
	slogmulti "github.com/samber/slog-multi"
	slogsampling "github.com/samber/slog-sampling"
	"golang.org/x/exp/slog"
)

// Will print the first 10 entries having the same level+message, then every 10th messages until next interval.
option := slogsampling.Option{
    Tick:       5 * time.Second,
    First:      10,
    Thereafter: 10,

    OnAccepted: func(context.Context, slog.Record) {
        // ...
    },
    OnDropped: func(context.Context, slog.Record) {
        // ...
    },
}

logger := slog.New(
    slogmulti.
        Pipe(option.NewSamplingMiddleware()).
        Handler(slog.NewJSONHandler(os.Stdout)),
)
```

### Standalone

```go
import (
	slogmulti "github.com/samber/slog-multi"
	slogsampling "github.com/samber/slog-sampling"
	"golang.org/x/exp/slog"
)

// Will print the first 10 entries having the same level+message, then every 10th messages until next interval.
option := slogsampling.Option{
    Tick:       5 * time.Second,
    First:      10,
    Thereafter: 10,

    OnAccepted: func(context.Context, slog.Record) {
        // ...
    },
    OnDropped: func(context.Context, slog.Record) {
        // ...
    },
}

logger := slog.New(
    option.NewSamplingMiddleware()(
        slog.NewJSONHandler(os.Stdout),
    ),
)
```

## 🤝 Contributing

- Ping me on twitter [@samuelberthe](https://twitter.com/samuelberthe) (DMs, mentions, whatever :))
- Fork the [project](https://github.com/samber/slog-sampling)
- Fix [open issues](https://github.com/samber/slog-sampling/issues) or request new features

Don't hesitate ;)

```bash
# Install some dev dependencies
make tools

# Run tests
make test
# or
make watch-test
```

## 👤 Contributors

![Contributors](https://contrib.rocks/image?repo=samber/slog-sampling)

## 💫 Show your support

Give a ⭐️ if this project helped you!

[![GitHub Sponsors](https://img.shields.io/github/sponsors/samber?style=for-the-badge)](https://github.com/sponsors/samber)

## 📝 License

Copyright © 2023 [Samuel Berthe](https://github.com/samber).

This project is [MIT](./LICENSE) licensed.
