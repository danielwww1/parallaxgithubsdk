<div align="center">

# 🛡️ Parallax APIs SDK

**Professional Anti-Bot Cookie Generation SDK**

[![Discord](https://img.shields.io/badge/Discord-Join%20Us-5865F2?style=for-the-badge&logo=discord&logoColor=white)](https://discord.gg/parallax)
[![Website](https://img.shields.io/badge/Website-Visit-00D4FF?style=for-the-badge&logo=google-chrome&logoColor=white)](https://parallax.systems)

*Fast, reliable, and simple anti-bot bypass solutions for DataDome and PerimeterX*

</div>

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Features](#-features)
- [Installation](#-installation)
- [Quick Start](#-quick-start)
- [Cookie Generation](#-cookie-generation)
  - [DataDome Cookies](#datadome-cookies)
  - [PerimeterX Cookies](#perimeterx-cookies)
- [Configuration](#-configuration)
- [API Reference](#-api-reference)
- [Examples](#-examples)
- [Support](#-support)

---

## 🎯 Overview

Parallax APIs SDK is a professional Go library for generating valid anti-bot protection cookies. Bypass DataDome and PerimeterX protection layers with ease using our high-performance SDK.

**Key Highlights:**
- ⚡ **Fast Generation**: DataDome cookies in ~200ms, PerimeterX cookies in ~350-400ms
- 🔒 **Secure**: Industry-standard security practices
- 🚀 **Simple API**: Easy integration with minimal code
- 📦 **Production Ready**: Battle-tested and reliable

---

## ✨ Features

| Feature | DataDome | PerimeterX |
|---------|----------|------------|
| Cookie Generation | ✅ | ✅ |
| User Agent Generation | ✅ | ❌ |
| Proxy Support | ✅ | ✅ |
| Custom Configuration | ✅ | ✅ |
| Average Speed | ~200ms | ~350-400ms |

---

## 📦 Installation

```bash
go get github.com/yourusername/parallax-sdk
```

---

## 🚀 Quick Start

```go
package main

import (
    "fmt"
    parallax "github.com/yourusername/parallax-sdk"
)

func main() {
    // Initialize SDK with your API key
    sdk := parallax.NewDatadomeSDK("YOUR_API_KEY", "")

    // Generate DataDome cookie
    task := parallax.TaskDatadomeCookie{
        Site:   "example",
        Region: "us",
        Proxy:  "http://user:pass@proxy:port",
    }

    response, err := sdk.GenerateDatadomeCookie(task)
    if err != nil {
        panic(err)
    }

    fmt.Printf("Cookie: %s\n", response.Message)
}
```

---

## 🍪 Cookie Generation

### DataDome Cookies

DataDome cookies are generated in approximately **200ms** with full user agent support.

**Demo:**

![DataDome Cookie Generation](demos/datadome-demo.gif)

**Code Example:**

```go
sdk := parallax.NewDatadomeSDK("YOUR_API_KEY", "")

cookieTask := parallax.TaskDatadomeCookie{
    Site:        "targetsite",
    Region:      "us",
    Proxyregion: "us",
    Proxy:       "http://user:pass@proxy:port",
    Pd:          parallax.PD_Init,
}

response, err := sdk.GenerateDatadomeCookie(cookieTask)
if err != nil {
    log.Fatal(err)
}

fmt.Printf("🍪 Cookie: %s\n", response.Message)
```

### PerimeterX Cookies

PerimeterX cookies are generated in approximately **350-400ms** with multiple cookie types.

**Demo:**

![PerimeterX Cookie Generation](demos/px-demo.gif)

**Code Example:**

```go
sdk := parallax.NewPerimeterXSDK("YOUR_API_KEY", "")

cookieTask := parallax.TaskPerimeterXCookie{
    Site:   "targetsite",
    Region: "us",
    Proxy:  "http://user:pass@proxy:port",
}

response, err := sdk.GeneratePerimeterXCookie(cookieTask)
if err != nil {
    log.Fatal(err)
}

fmt.Printf("🍪 _px3: %s\n", response.Px3)
fmt.Printf("🍪 _pxvid: %s\n", response.Pxvid)
fmt.Printf("🍪 pxcts: %s\n", response.Pxcts)
```

---

## ⚙️ Configuration

### SDK Initialization

```go
// DataDome SDK
datadomeSDK := parallax.NewDatadomeSDK("YOUR_API_KEY", "optional_proxy")

// PerimeterX SDK
perimeterxSDK := parallax.NewPerimeterXSDK("YOUR_API_KEY", "optional_proxy")
```

### Proxy Configuration

```go
// Format: http://username:password@host:port
proxy := "http://user:pass@proxy.example.com:8080"
```

### Region Support

Supported regions: `us`, `eu`, `asia`, `de`, `uk`, `fr`

---

## 📚 API Reference

### DataDome SDK

#### `NewDatadomeSDK(apiKey, proxy string) *DatadomeSDK`
Creates a new DataDome SDK instance.

#### `GenerateDatadomeCookie(task TaskDatadomeCookie) (*DatadomeCookieResponse, error)`
Generates a DataDome cookie based on the provided task configuration.

#### `GenerateUserAgent(task TaskGenUserAgent) (*UserAgentResponse, error)`
Generates a valid user agent for the specified site and region.

### PerimeterX SDK

#### `NewPerimeterXSDK(apiKey, proxy string) *PerimeterXSDK`
Creates a new PerimeterX SDK instance.

#### `GeneratePerimeterXCookie(task TaskPerimeterXCookie) (*PerimeterXCookieResponse, error)`
Generates PerimeterX cookies (_px3, _pxvid, pxcts) based on the provided task configuration.

---

## 💡 Examples

### Full DataDome Example with Error Handling

```go
package main

import (
    "fmt"
    "log"
    parallax "github.com/yourusername/parallax-sdk"
)

func main() {
    sdk := parallax.NewDatadomeSDK("YOUR_API_KEY", "")

    // Generate user agent
    uaTask := parallax.TaskGenUserAgent{
        Site:   "example",
        Region: "us",
    }

    uaResp, err := sdk.GenerateUserAgent(uaTask)
    if err != nil {
        log.Fatalf("Failed to generate user agent: %v", err)
    }

    fmt.Printf("User Agent: %s\n", uaResp.UserAgent)

    // Generate cookie
    cookieTask := parallax.TaskDatadomeCookie{
        Site:        "example",
        Region:      "us",
        Proxyregion: "us",
        Proxy:       "http://user:pass@proxy:port",
        Pd:          parallax.PD_Init,
    }

    cookieResp, err := sdk.GenerateDatadomeCookie(cookieTask)
    if err != nil {
        log.Fatalf("Failed to generate cookie: %v", err)
    }

    fmt.Printf("🍪 Cookie: %s\n", cookieResp.Message)
}
```

### Full PerimeterX Example

```go
package main

import (
    "fmt"
    "log"
    parallax "github.com/yourusername/parallax-sdk"
)

func main() {
    sdk := parallax.NewPerimeterXSDK("YOUR_API_KEY", "")

    task := parallax.TaskPerimeterXCookie{
        Site:   "example",
        Region: "us",
        Proxy:  "http://user:pass@proxy:port",
    }

    response, err := sdk.GeneratePerimeterXCookie(task)
    if err != nil {
        log.Fatalf("Failed to generate cookies: %v", err)
    }

    fmt.Printf("🍪 Generated PerimeterX Cookies:\n")
    fmt.Printf("   _px3: %s\n", response.Px3)
    fmt.Printf("   _pxvid: %s\n", response.Pxvid)
    fmt.Printf("   pxcts: %s\n", response.Pxcts)
}
```

---

## 🛠️ Support

Need help? We're here for you!

- 💬 **Discord**: [Join our community](https://discord.gg/parallax)
- 🌐 **Website**: [parallax.systems](https://parallax.systems)
- 📧 **Email**: support@parallax.systems
- 📖 **Documentation**: [docs.parallax.systems](https://docs.parallax.systems)

---

<div align="center">

**Built with ❤️ by the Parallax Systems Team**

© 2024 Parallax Systems. All rights reserved.

</div>