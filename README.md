# zerolog-gin

![Unittest status badge](https://github.com/Hochfrequenz/zerolog-gin/workflows/Unittests/badge.svg)
![Coverage status badge](https://github.com/Hochfrequenz/zerolog-gin/workflows/coverage/badge.svg)
![Linter status badge](https://github.com/Hochfrequenz/zerolog-gin/workflows/golangci-lint/badge.svg)
[![Go Report Card](https://goreportcard.com/badge/github.com/Hochfrequenz/zerolog-gin)](https://goreportcard.com/report/github.com/Hochfrequenz/zerolog-gin)
[![Go Reference](https://pkg.go.dev/badge/github.com/hochfrequenz/zerolog-gin.svg)](https://pkg.go.dev/github.com/hochfrequenz/zerolog-gin)
[![license](https://img.shields.io/github/license/hochfrequenz/zerolog-gin)](./LICENSE)

Zerolog logger for gin

## Installation

```bash
go get github.com/hochfrequenz/zerolog-gin
```

## Usage

```go
package main

import (
    "bytes"
    "fmt"
    "github.com/gin-gonic/gin"
    zerologgin "github.com/Hochfrequenz/zerolog-gin"
    "github.com/rs/zerolog"
    "io"
    "net/http"
    "os"
)

func main() {
    // logger to use with gin
    logger := zerolog.New(os.Stdout).With().Timestamp().Logger()

    // Create an instance of gin router
    r := gin.New()
    r.SetTrustedProxies([]string{"::1"})

    // Add zerolog-gin as a middleware
    r.Use(zerologgin.LoggerWithOptions(&zerologgin.Options{Name: "server", Logger: &logger}))

    r.GET("/", func(c *gin.Context) {
        c.String(http.StatusOK, "hello, zerolog-gin example")
    })

    r.GET("/ping", func(c *gin.Context) {
        c.JSON(http.StatusOK, gin.H{"message": "pong"})
    })

    r.POST("/echo", func(c *gin.Context) {
        body, err := io.ReadAll(c.Request.Body)
        c.Request.Body = io.NopCloser(bytes.NewReader(body))
        if err != nil {
            c.JSON(http.StatusInternalServerError, gin.H{"error": "undefined"})
        } else {
            c.String(http.StatusOK, string(body))
        }
    })

    if err := r.Run(":8080"); err != nil {
        fmt.Println(err.Error())
    }
}
```
