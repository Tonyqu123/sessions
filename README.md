# sessions

[![Build Status](https://travis-ci.org/gin-contrib/sessions.svg)](https://travis-ci.org/gin-contrib/sessions)
[![codecov](https://codecov.io/gh/gin-contrib/sessions/branch/master/graph/badge.svg)](https://codecov.io/gh/gin-contrib/sessions)
[![Go Report Card](https://goreportcard.com/badge/github.com/gin-contrib/sessions)](https://goreportcard.com/report/github.com/gin-contrib/sessions)
[![GoDoc](https://godoc.org/github.com/gin-contrib/sessions?status.svg)](https://godoc.org/github.com/gin-contrib/sessions)
[![Join the chat at https://gitter.im/gin-gonic/gin](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/gin-gonic/gin)

Gin middleware for session management with multi-backend support (currently cookie, Redis).

## Examples

#### cookie-based

[embedmd]:# (example_cookie/main.go go)
```go
package main

import (
	"github.com/gin-contrib/sessions"
	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()
	store := sessions.NewCookieStore([]byte("secret"))
	r.Use(sessions.Sessions("mysession", store))

	r.GET("/incr", func(c *gin.Context) {
		session := sessions.Default(c)
		var count int
		v := session.Get("count")
		if v == nil {
			count = 0
		} else {
			count = v.(int)
			count += 1
		}
		session.Set("count", count)
		session.Save()
		c.JSON(200, gin.H{"count": count})
	})
	r.Run(":8000")
}
```

#### Redis

[embedmd]:# (example_redis/main.go go)
```go
package main

import (
	"github.com/gin-contrib/sessions"
	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()
	store, _ := sessions.NewRedisStore(10, "tcp", "localhost:6379", "", []byte("secret"))
	r.Use(sessions.Sessions("mysession", store))

	r.GET("/incr", func(c *gin.Context) {
		session := sessions.Default(c)
		var count int
		v := session.Get("count")
		if v == nil {
			count = 0
		} else {
			count = v.(int)
			count += 1
		}
		session.Set("count", count)
		session.Save()
		c.JSON(200, gin.H{"count": count})
	})
	r.Run(":8000")
}
```