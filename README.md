# redis

> A redis-based session store

[![ReportCard][reportcard-image]][reportcard-url] [![GoDoc][godoc-image]][godoc-url] [![License][license-image]][license-url]

## Quick Start

### Download and install

```bash
$ go get -u -v gopkg.in/go-session/redis.v1
```

### Create file `server.go`

```go
package main

import (
	"fmt"
	"net/http"

	"gopkg.in/go-session/redis.v1"
	"gopkg.in/session.v2"
)

func main() {
	session.InitManager(
		session.SetCookieName("session_id"),
		session.SetSign([]byte("sign")),
		session.SetStore(redis.NewRedisStore(&redis.Options{
			Addr: "127.0.0.1:6379",
			DB:   15,
		})),
	)

	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		store, err := session.Start(w, r)
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
		store, err := session.Start(w, r)
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
$ go build server.go
$ ./server
```

### Open in your web browser

<http://localhost:8080>

    foo:bar

## MIT License

    Copyright (c) 2018 Lyric

[reportcard-url]: https://goreportcard.com/report/gopkg.in/go-session/redis.v1
[reportcard-image]: https://goreportcard.com/badge/gopkg.in/go-session/redis.v1
[godoc-url]: https://godoc.org/gopkg.in/go-session/redis.v1
[godoc-image]: https://godoc.org/gopkg.in/go-session/redis.v1?status.svg
[license-url]: http://opensource.org/licenses/MIT
[license-image]: https://img.shields.io/npm/l/express.svg
