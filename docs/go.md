# Go SDK Guide

Complete API reference for the Parallax Systems Go SDK.

## Installation

```bash
go get github.com/yourusername/parallax-sdk
```

## Quick Start

```go
package main

import (
    "fmt"
    "log"
    parallax "github.com/yourusername/parallax-sdk"
)

func main() {
    // Initialize SDK
    sdk := parallax.NewSDK("YOUR_API_KEY", "")

    // Generate cookie
    response, err := sdk.GenerateDatadomeCookie(parallax.TaskDatadomeCookie{
        Site:        "example",
        Region:      "us",
        Proxyregion: "us",
        Proxy:       "http://user:pass@proxy:port",
        Pd:          parallax.PD_Init,
    })

    if err != nil {
        log.Fatal(err)
    }

    fmt.Printf("Cookie: %s\n", response.Message)
}
```

## API Reference

### DataDome SDK

#### Initialization

```go
sdk := parallax.NewSDK(apiKey string, apiHost string) *SDK
```

**Parameters:**
- `apiKey` - Your Parallax API key
- `apiHost` - Optional custom API host (use empty string for default)

#### Generate DataDome Cookie

```go
response, err := sdk.GenerateDatadomeCookie(task TaskDatadomeCookie) (*DatadomeCookieResponse, error)
```

**TaskDatadomeCookie Structure:**
```go
type TaskDatadomeCookie struct {
    Site        string                 // Target site identifier
    Region      string                 // Target region (e.g., "us", "eu")
    Proxyregion string                 // Proxy region
    Proxy       string                 // Proxy URL (http://user:pass@host:port)
    Pd          string                 // Product type: PD_Init, PD_Captcha, PD_Interstitial
    Data        TaskDatadomeCookieData // Challenge data (optional)
}

type TaskDatadomeCookieData struct {
    Cid        string // Current cookie ID
    E          string // Challenge parameter E
    S          string // Challenge parameter S
    B          string // Challenge parameter B
    InitialCid string // Initial cookie ID
}
```

**Response Structure:**
```go
type DatadomeCookieResponse struct {
    Message   string // Cookie value (format: "datadome=value")
    UserAgent string // Generated user agent
}
```

**Example:**
```go
response, err := sdk.GenerateDatadomeCookie(parallax.TaskDatadomeCookie{
    Site:        "targetsite",
    Region:      "us",
    Proxyregion: "us",
    Proxy:       "http://user:pass@proxy:port",
    Pd:          parallax.PD_Init,
    Data:        parallax.TaskDatadomeCookieData{},
})

if err != nil {
    log.Fatal(err)
}

fmt.Printf("Cookie: %s\n", response.Message)
fmt.Printf("User-Agent: %s\n", response.UserAgent)
```

#### Generate User Agent

```go
response, err := sdk.GenerateUserAgent(task TaskGenUserAgent) (*UserAgentResponse, error)
```

**TaskGenUserAgent Structure:**
```go
type TaskGenUserAgent struct {
    Site   string // Target site identifier
    Region string // Target region
}
```

**Response Structure:**
```go
type UserAgentResponse struct {
    Message            string // Status message
    UserAgent          string // User agent string
    SecHeader          string // sec-ch-ua header
    SecFullVersionList string // sec-ch-ua-full-version-list header
    SecPlatform        string // sec-ch-ua-platform header
    SecArch            string // sec-ch-ua-arch header
}
```

**Example:**
```go
uaResp, err := sdk.GenerateUserAgent(parallax.TaskGenUserAgent{
    Site:   "example",
    Region: "us",
})

if err != nil {
    log.Fatal(err)
}

fmt.Printf("User-Agent: %s\n", uaResp.UserAgent)
fmt.Printf("sec-ch-ua: %s\n", uaResp.SecHeader)
```

#### Parse Challenge URL

```go
data, productType, err := parallax.ParseChallengeURL(challengeURL string, prevCookie string) (*TaskDatadomeCookieData, string, error)
```

**Parameters:**
- `challengeURL` - DataDome challenge URL
- `prevCookie` - Previous cookie value

**Returns:**
- `*TaskDatadomeCookieData` - Extracted challenge data
- `string` - Product type (PD_Captcha or PD_Interstitial)
- `error` - Error if parsing fails

**Example:**
```go
challengeURL := "https://geo.captcha-delivery.com/captcha/?cid=xxx&e=yyy&s=zzz"
prevCookie := "datadome=old_value"

data, productType, err := parallax.ParseChallengeURL(challengeURL, prevCookie)
if err != nil {
    log.Fatal(err)
}

// Use extracted data in cookie generation
response, err := sdk.GenerateDatadomeCookie(parallax.TaskDatadomeCookie{
    Site:        "example",
    Region:      "us",
    Proxyregion: "us",
    Proxy:       "http://user:pass@proxy:port",
    Pd:          productType,
    Data:        *data,
})
```

### PerimeterX SDK

#### Initialization

```go
pxSDK := parallax.NewPerimeterxSDK(apiKey string, apiHost string) *PerimeterxSDK
```

**Parameters:**
- `apiKey` - Your Parallax API key
- `apiHost` - Optional custom API host (use empty string for default)

#### Generate PerimeterX Cookies

```go
response, err := pxSDK.GenerateCookies(task TaskGeneratePXCookies) (*PxCookieResponse, error)
```

**TaskGeneratePXCookies Structure:**
```go
type TaskGeneratePXCookies struct {
    Site        string // Target site identifier
    Region      string // Target region (e.g., "com", "us")
    Proxyregion string // Proxy region
    Proxy       string // Proxy URL
}
```

**Response Structure:**
```go
type PxCookieResponse struct {
    Message        string // Status message
    Cookie         string // _px3 cookie value
    Vid            string // _pxvid value
    Cts            string // pxcts value
    IsFlagged      bool   // Whether request was flagged
    IsMaybeFlagged bool   // Whether request might be flagged
    UserAgent      string // Generated user agent
    Data           string // Data for hold captcha (if needed)
}
```

**Example:**
```go
response, err := pxSDK.GenerateCookies(parallax.TaskGeneratePXCookies{
    Site:        "example",
    Region:      "com",
    Proxyregion: "us",
    Proxy:       "http://user:pass@proxy:port",
})

if err != nil {
    log.Fatal(err)
}

fmt.Printf("_px3: %s\n", response.Cookie)
fmt.Printf("_pxvid: %s\n", response.Vid)
fmt.Printf("pxcts: %s\n", response.Cts)
fmt.Printf("Flagged: %v\n", response.IsFlagged)
```

#### Generate Hold Captcha

```go
response, err := pxSDK.GenerateHoldCaptcha(task TaskGenerateHoldCaptcha) (*PxCookieResponse, error)
```

**TaskGenerateHoldCaptcha Structure:**
```go
type TaskGenerateHoldCaptcha struct {
    Site        string // Target site identifier
    Region      string // Target region
    Proxyregion string // Proxy region
    Proxy       string // Proxy URL
    Data        string // Data from initial GenerateCookies response
    PowPro      string // Optional POW_PRO value
}
```

**Example:**
```go
// First generate initial cookies
initialResp, _ := pxSDK.GenerateCookies(parallax.TaskGeneratePXCookies{
    Site:        "example",
    Region:      "com",
    Proxyregion: "us",
    Proxy:       "http://user:pass@proxy:port",
})

// If data is present, generate hold captcha
if initialResp.Data != "" {
    holdResp, err := pxSDK.GenerateHoldCaptcha(parallax.TaskGenerateHoldCaptcha{
        Site:        "example",
        Region:      "com",
        Proxyregion: "us",
        Proxy:       "http://user:pass@proxy:port",
        Data:        initialResp.Data,
    })

    if err != nil {
        log.Fatal(err)
    }

    fmt.Printf("Hold Captcha Cookie: %s\n", holdResp.Cookie)
}
```

## Constants

### Product Types (DataDome)

```go
const (
    PD_Captcha      = "captcha"      // Captcha challenge
    PD_Interstitial = "interstitial" // Interstitial challenge
    PD_Init         = "init"         // Initial cookie generation
)
```

### Default API Host

```go
const DefaultAPIHost = "https://dd.parallaxsystems.io"
```

## Error Handling

The SDK returns standard Go errors. Always check for errors:

```go
response, err := sdk.GenerateDatadomeCookie(task)
if err != nil {
    // Handle error
    log.Printf("Error generating cookie: %v", err)
    return
}
```

Common errors:
- Invalid API key
- Network timeouts
- Invalid proxy configuration
- Rate limit exceeded

## Best Practices

### 1. Reuse SDK Instances

```go
// Good: Create once, use many times
sdk := parallax.NewSDK(apiKey, "")
for _, site := range sites {
    response, _ := sdk.GenerateDatadomeCookie(...)
}
```

### 2. Handle Errors Gracefully

```go
response, err := sdk.GenerateDatadomeCookie(task)
if err != nil {
    log.Printf("Failed to generate cookie: %v", err)
    // Implement retry logic or fallback
    return
}
```

### 3. Use Timeouts

```go
// Set timeout for HTTP requests
sdk.SetTimeout(30 * time.Second)
```

### 4. Concurrent Requests

```go
var wg sync.WaitGroup
results := make(chan *parallax.DatadomeCookieResponse, len(sites))

for _, site := range sites {
    wg.Add(1)
    go func(s string) {
        defer wg.Done()
        resp, err := sdk.GenerateDatadomeCookie(...)
        if err == nil {
            results <- resp
        }
    }(site)
}

wg.Wait()
close(results)
```

## Complete Example

```go
package main

import (
    "fmt"
    "log"
    parallax "github.com/yourusername/parallax-sdk"
)

func main() {
    // Initialize SDKs
    ddSDK := parallax.NewSDK("YOUR_API_KEY", "")
    pxSDK := parallax.NewPerimeterxSDK("YOUR_API_KEY", "")

    // Generate DataDome cookie
    fmt.Println("=== DataDome ===")
    ddResp, err := ddSDK.GenerateDatadomeCookie(parallax.TaskDatadomeCookie{
        Site:        "example",
        Region:      "us",
        Proxyregion: "us",
        Proxy:       "http://user:pass@proxy:port",
        Pd:          parallax.PD_Init,
    })

    if err != nil {
        log.Fatal(err)
    }

    fmt.Printf("Cookie: %s\n", ddResp.Message)
    fmt.Printf("User-Agent: %s\n\n", ddResp.UserAgent)

    // Generate PerimeterX cookies
    fmt.Println("=== PerimeterX ===")
    pxResp, err := pxSDK.GenerateCookies(parallax.TaskGeneratePXCookies{
        Site:        "example",
        Region:      "com",
        Proxyregion: "us",
        Proxy:       "http://user:pass@proxy:port",
    })

    if err != nil {
        log.Fatal(err)
    }

    fmt.Printf("_px3: %s\n", pxResp.Cookie)
    fmt.Printf("_pxvid: %s\n", pxResp.Vid)
    fmt.Printf("pxcts: %s\n", pxResp.Cts)
}
```

## Support

- 💬 [Discord Community](https://discord.com/invite/2QWbHcmWnf)
- 🌐 [Website](https://www.parallaxsystems.io)
- 📧 [Email Support](mailto:support@parallaxsystems.io)
- 📖 [Main Documentation](../README.md)