# What
Limitmap is Go library providing map of semaphores to limit concurrency against some string keys.

- Blocking `Acquire/Release` interface.
- Separate limit value for each key.
- Code errors such as `Release` before `Acquire` result in `panic()`.
- Semaphore is implemented using `sync.Cond`.
- Map access is guarded by shared `sync.Mutex`.


# Example
Example use cases:
- Limit simultaneous connections to services.
- Limit IO per storage device.

```
limits := limitmap.NewLimitMap()
process := func(url *url.URL) {
	// At most 2 concurrent requests to each host.
	limits.Acquire(url.Host, 2)
	defer limits.Release(url.Host)
	defer limits.Release("all")
	r, err := http.Get(url.String())
	// do something with response/error
}
for url := range urlChan {
	// Limit max goroutines only as safety net againist exhausting computing resources, this number may seem big.
	limits.Acquire("all", 200000)
	go process(url)
}
limits.Wait() // until all tasks finished
```


# Install

`go get -u github.com/temoto/limitmap`


# Flair

[![Build status](https://travis-ci.org/temoto/limitmap.svg?branch=master)](https://travis-ci.org/temoto/limitmap)
[![Coverage](https://codecov.io/gh/temoto/limitmap/branch/master/graph/badge.svg)](https://codecov.io/gh/temoto/limitmap)
[![Go Report Card](https://goreportcard.com/badge/github.com/temoto/limitmap)](https://goreportcard.com/report/github.com/temoto/limitmap)
[![License: CC0](https://licensebuttons.net/p/zero/1.0/88x31.png)](https://creativecommons.org/publicdomain/zero/1.0/)
