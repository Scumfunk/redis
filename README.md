# Redis store for [Session](https://github.com/go-session/session)

[![Build][build-status-image]][build-status-url] [![Codecov][codecov-image]][codecov-url] [![ReportCard][reportcard-image]][reportcard-url] [![GoDoc][godoc-image]][godoc-url] [![License][license-image]][license-url]

## Quick Start

### Download and install

```bash
go get -v github.com/Scumfunk/redis/v3
```

### Create file `server.go`

```go
package main

import (
	"context"
	"fmt"
	"net/http"

	"github.com/Scumfunk/session/v3"
	"github.com/Scumfunk/session/v3"
)

func main() {
	session.InitManager(
		session.SetStore(redis.NewRedisStore(&redis.Options{
			Addr: "127.0.0.1:6379",
			DB:   15,
		})),
	)

	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		store, err := session.Start(context.Background(), w, r)
		if err != nil {
			fmt.Fprint(w, err)
			return
		}

		store.Set("foo", "bar")
		err = store.Save()
		if err != nil {
			fmt.Fprint(w, err)
			return
		}

		http.Redirect(w, r, "/foo", 302)
	})

	http.HandleFunc("/foo", func(w http.ResponseWriter, r *http.Request) {
		store, err := session.Start(context.Background(), w, r)
		if err != nil {
			fmt.Fprint(w, err)
			return
		}

		foo, ok := store.Get("foo")
		if ok {
			fmt.Fprintf(w, "foo:%s", foo)
			return
		}
		fmt.Fprint(w, "does not exist")
	})

	http.ListenAndServe(":8080", nil)
}
```

### Build and run

```bash
go run server.go
```

### Open in your web browser

<http://localhost:8080>

    foo:bar

## MIT License

    Copyright (c) 2021 Lyric

[build-status-url]: https://travis-ci.org/go-session/redis
[build-status-image]: https://travis-ci.org/go-session/redis.svg?branch=master
[codecov-url]: https://codecov.io/gh/go-session/redis
[codecov-image]: https://codecov.io/gh/go-session/redis/branch/master/graph/badge.svg
[reportcard-url]: https://goreportcard.com/report/github.com/go-session/redis
[reportcard-image]: https://goreportcard.com/badge/github.com/go-session/redis
[godoc-url]: https://godoc.org/github.com/go-session/redis
[godoc-image]: https://godoc.org/github.com/go-session/redis?status.svg
[license-url]: http://opensource.org/licenses/MIT
[license-image]: https://img.shields.io/npm/l/express.svg
