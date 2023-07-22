# protoc-gen-go-log-valuer

protoc-gen-go-log-valuer is a protoc plugin for implementing the [slog.LogValuer](https://pkg.go.dev/log/slog@go1.21.0rc2#LogValuer) interface on proto messages.

## Requirements

- Go compiler and tools (Go 1.21 or higher)
- protoc compiler
- protoc-gen-go

See https://github.com/protocolbuffers/protobuf-go

## Installation

```
go install github.com/kei2100/protoc-gen-go-log-valuer/plugin/protoc-gen-go-log-valuer@latest
```

## Usage
Define your proto file

```proto
syntax = "proto3";

package example;

message ExampleMessage {
  string message = 1;
  int32 count= 2;
  string secret = 3 [debug_redact = true]; // debug_redact: https://github.com/protocolbuffers/protobuf/blob/v22.0/src/google/protobuf/descriptor.proto#L632
}
```

Generate the code by protoc

```
protoc --go_out=. --go-log-valuer_out=. example.proto
```

Output results should be:
```
example.pb.go              # auto-generated by protoc-gen-go
example.pb.log_valuer.go   # auto-generated by protoc-gen-go-log-valuer
example.proto              # original proto file
```

`example.pb.log_valuer.go` is generated as follows

```go
// Code generated by protoc-gen-go-log-valuer. DO NOT EDIT.
//
// source: test/example.proto

package example

import (
	slog "log/slog"
)

func (x *ExampleMessage) LogValue() slog.Value {
	if x == nil {
		return slog.AnyValue(nil)
	}
	attrs := make([]slog.Attr, 0, 3)
	attrs = append(attrs, slog.String("message", x.Message))
	attrs = append(attrs, slog.Int64("count", int64(x.Count)))
	attrs = append(attrs, slog.String("secret", "[REDACTED]"))
	return slog.GroupValue(attrs...)
}
```

## Development
```
# fmt 
$ make fmt

# lint
$ make lint

# test
$ make test

# bench
$ make bench
```
