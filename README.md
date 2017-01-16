echo-session
======

### NOTIFICATION

This package was forked and fixed to run with Echo v3.0.3

Middleware echo-session is a session support for [echo](https://github.com/labstack/echo/).

### Installation

	go get github.com/niggywizzywoz/echo-session

## Example

```go
package main

import (
	"github.com/niggywizzywoz/echo-session"
	"github.com/labstack/echo"
	"github.com/labstack/echo/middleware"
)

func main() {
	serv := echo.New()
	serv.Use(middleware.Logger())
	serv.Use(middleware.Recover())
	// store := session.NewCookieStore([]byte("secret"))
	store, err := session.NewRedisStore(32, "tcp", "localhost:6379", "", []byte("secret"))
	if err != nil {
		panic(err)
	}
	// Use Echo-session middleware
	serv.Use(session.Sessions("GSESSION", store))
	serv.GET("/", testHandler)
	if err := serv.Start(":80"); err != nil {
		panic(err)
	}
}

func testHandler (c echo.Context) error {
		session := session.Default(c)
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
		return c.JSON(200, map[string]interface{}{
			"visit": count,
		})
		return nil
	})
}
```

## License

This project is under Apache v2 License. See the [LICENSE](LICENSE) file for the full license text.
