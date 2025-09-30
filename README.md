<div align="center">

# Parallax APIs SDK

### Professional Request-Based Anti-Bot Solution

[![Discord](https://img.shields.io/badge/Discord-Join%20Us-5865F2?style=for-the-badge&logo=discord&logoColor=white)](https://discord.com/invite/2QWbHcmWnf)
[![Website](https://img.shields.io/badge/Website-Visit-00D4FF?style=for-the-badge&logo=google-chrome&logoColor=white)](https://www.parallaxsystems.io)

**DataDome • PerimeterX • Request-Based API • Sub-400ms Response Times**

Simple HTTP API for generating valid anti-bot cookies without browser overhead

</div>

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Features](#-features)
- [Installation](#-installation)
- [SDK Languages](#-sdk-languages)
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

Parallax APIs SDK is a professional multi-language SDK for generating valid anti-bot protection cookies. Bypass DataDome and PerimeterX protection layers with ease using our high-performance SDK.

**Available in:**
- 🔷 **Go** - High-performance native implementation
- 🟦 **TypeScript** - Modern JavaScript/Node.js support
- 🐍 **Python** - Python 3.x compatibility
- 🎭 **Playwright** - Browser automation integration

**Key Highlights:**
- ⚡ **Fast Generation**: DataDome cookies in ~200ms, PerimeterX cookies in ~350-400ms
- 🔒 **Secure**: Industry-standard security practices
- 🚀 **Simple API**: Easy integration with minimal code
- 📦 **Production Ready**: Battle-tested and reliable
- 🌍 **Multi-Language**: Choose your preferred programming language

---

## ✨ Features

| Feature | DataDome | PerimeterX |
|---------|----------|------------|
| Cookie Generation | ✅ | ✅ |
| User Agent Generation | ✅ | ❌ |
| Proxy Support | ✅ | ✅ |
| Custom Configuration | ✅ | ✅ |
| Hold Captcha | ❌ | ✅ |
| Average Speed | ~200ms | ~350-400ms |

---

## 📦 Installation

### Go
```bash
go get github.com/yourusername/parallax-sdk
```

### TypeScript/JavaScript
```bash
npm install @parallax/sdk
# or
yarn add @parallax/sdk
```

### Python
```bash
pip install parallax-sdk
```

### Playwright
```bash
npm install @parallax/playwright-sdk
```

---

## 🌐 SDK Languages

| Language | Status | Documentation |
|----------|--------|---------------|
| Go | ✅ Production Ready | [Go Docs](#go-examples) |
| TypeScript | ✅ Production Ready | [TS Docs](#typescript-examples) |
| Python | ✅ Production Ready | [Python Docs](#python-examples) |
| Playwright | ✅ Production Ready | [Playwright Docs](#playwright-examples) |

---

## 🚀 Quick Start

### Go

```go
package main

import (
    "fmt"
    parallax "github.com/yourusername/parallax-sdk"
)

func main() {
    // Initialize SDK
    sdk := parallax.NewSDK("YOUR_API_KEY", "")

    // Generate DataDome cookie
    response, err := sdk.GenerateDatadomeCookie(parallax.TaskDatadomeCookie{
        Site:        "example",
        Region:      "us",
        Proxyregion: "us",
        Proxy:       "http://user:pass@proxy:port",
        Pd:          parallax.PD_Init,
    })

    fmt.Printf("Cookie: %s\n", response.Message)
}
```

### TypeScript

```typescript
import DatadomeSDK from "@parallax/sdk/datadome";

const sdk = new DatadomeSDK({ apiKey: "YOUR_API_KEY" });

const cookie = await sdk.generateCookie({
    site: "example",
    region: "us",
    proxy: "http://user:pass@proxy:port",
    proxyregion: "us",
    pd: "init",
    data: {}
});

console.log(cookie.message); // datadome=cookie_value
```

---

## 🍪 Cookie Generation

### DataDome Cookies

DataDome cookies are generated in approximately **200ms** with full user agent support.

**Demo:**

![DataDome Cookie Generation](demos/datadome-demo.gif)

**Go Example:**

```go
sdk := parallax.NewSDK("YOUR_API_KEY", "")

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

fmt.Printf("🍪 Cookie: %s\n", response.Message)
fmt.Printf("User-Agent: %s\n", response.UserAgent)
```

**TypeScript Example:**

```typescript
const sdk = new DatadomeSDK({ apiKey: "YOUR_API_KEY" });

const cookie = await sdk.generateCookie({
    site: "targetsite",
    region: "us",
    proxy: "http://user:pass@proxy:port",
    proxyregion: "us",
    pd: "init",
    data: {}
});

console.log(`🍪 Cookie: ${cookie.message}`);
console.log(`User-Agent: ${cookie.UserAgent}`);
```

### PerimeterX Cookies

PerimeterX cookies are generated in approximately **350-400ms** with multiple cookie values.

**Demo:**

![PerimeterX Cookie Generation](demos/px-demo.gif)

**Go Example:**

```go
sdk := parallax.NewPerimeterxSDK("YOUR_API_KEY", "")

response, err := sdk.GenerateCookies(parallax.TaskGeneratePXCookies{
    Site:        "targetsite",
    Region:      "com",
    Proxyregion: "us",
    Proxy:       "http://user:pass@proxy:port",
})

if err != nil {
    log.Fatal(err)
}

fmt.Printf("🍪 _px3: %s\n", response.Cookie)
fmt.Printf("🍪 _pxvid: %s\n", response.Vid)
fmt.Printf("🍪 pxcts: %s\n", response.Cts)
```

**TypeScript Example:**

```typescript
const sdk = new PerimeterxSDK({ apiKey: "YOUR_API_KEY" });

const result = await sdk.generateCookies({
    site: "targetsite",
    region: "com",
    proxy: "http://user:pass@proxy:port",
    proxyregion: "us"
});

console.log(`🍪 _px3: ${result.cookie}`);
console.log(`🍪 _pxvid: ${result.vid}`);
console.log(`🍪 pxcts: ${result.cts}`);
```

---

## ⚙️ Configuration

### SDK Initialization

**Go:**
```go
// DataDome
sdk := parallax.NewSDK("YOUR_API_KEY", "optional_host")

// PerimeterX
pxSDK := parallax.NewPerimeterxSDK("YOUR_API_KEY", "optional_host")
```

**TypeScript:**
```typescript
// DataDome
const sdk = new DatadomeSDK({
    apiKey: "YOUR_API_KEY",
    apiHost: "optional_host" // defaults to standard API host
});

// PerimeterX
const pxSDK = new PerimeterxSDK({
    apiKey: "YOUR_API_KEY",
    apiHost: "optional_host"
});
```

### Proxy Configuration

```go
// Format: http://username:password@host:port
proxy := "http://user:pass@proxy.example.com:8080"
```

### Product Types (DataDome)

- `init` - Initial cookie generation
- `captcha` - Captcha challenge resolution
- `interstitial` - Interstitial page resolution

### Region Support

Supported regions: `us`, `eu`, `com`, `de`, `uk`, `fr`, `pl`, and more

---

## 📚 API Reference

### Go SDK

#### DataDome Methods

##### `NewSDK(apiKey, apiHost string) *SDK`
Creates a new SDK instance for DataDome.

##### `GenerateDatadomeCookie(task TaskDatadomeCookie) (*DatadomeCookieResponse, error)`
Generates a DataDome cookie.

**Response Fields:**
- `Message` - The cookie value (format: `datadome=value`)
- `UserAgent` - Generated user agent

##### `GenerateUserAgent(task TaskGenUserAgent) (*UserAgentResponse, error)`
Generates a valid user agent and sec-ch-ua headers.

**Response Fields:**
- `UserAgent` - The user agent string
- `SecHeader` - sec-ch-ua header
- `SecFullVersionList` - sec-ch-ua-full-version-list header
- `SecPlatform` - sec-ch-ua-platform header
- `SecArch` - sec-ch-ua-arch header

##### `ParseChallengeURL(challengeURL, prevCookie string) (*TaskDatadomeCookieData, string, error)`
Helper function to extract challenge data from DataDome URLs.

#### PerimeterX Methods

##### `NewPerimeterxSDK(apiKey, apiHost string) *PerimeterxSDK`
Creates a new PerimeterX SDK instance.

##### `GenerateCookies(task TaskGeneratePXCookies) (*PxCookieResponse, error)`
Generates PerimeterX cookies.

**Response Fields:**
- `Cookie` - _px3 cookie value
- `Vid` - _pxvid value
- `Cts` - pxcts value
- `IsFlagged` - Whether the request was flagged
- `IsMaybeFlagged` - Whether the request might be flagged
- `Data` - Additional data for hold captcha

##### `GenerateHoldCaptcha(task TaskGenerateHoldCaptcha) (*PxCookieResponse, error)`
Generates hold captcha solution for PerimeterX.

### TypeScript SDK

#### DataDome Methods

##### `new DatadomeSDK({ apiKey, apiHost? })`
Creates a new DataDome SDK instance.

##### `generateCookie(params): Promise<DatadomeCookieResponse>`
Generates a DataDome cookie.

##### `generateUserAgent(params): Promise<UserAgentResponse>`
Generates a user agent and headers.

##### `parseChallengeUrl(url, currentCookie): [TaskData, ProductType]`
Parses DataDome challenge URLs.

#### PerimeterX Methods

##### `new PerimeterxSDK({ apiKey, apiHost? })`
Creates a new PerimeterX SDK instance.

##### `generateCookies(params): Promise<PxCookieResponse>`
Generates PerimeterX cookies.

##### `generateHoldCaptcha(params): Promise<PxCookieResponse>`
Generates hold captcha solution.

---

## 💡 Examples

### Go Examples

#### Full DataDome Example with User Agent

```go
package main

import (
    "fmt"
    "log"
    parallax "github.com/yourusername/parallax-sdk"
)

func main() {
    sdk := parallax.NewSDK("YOUR_API_KEY", "")

    // Generate user agent
    uaResp, err := sdk.GenerateUserAgent(parallax.TaskGenUserAgent{
        Site:   "example",
        Region: "us",
    })
    if err != nil {
        log.Fatalf("Failed to generate user agent: %v", err)
    }

    fmt.Printf("User Agent: %s\n", uaResp.UserAgent)
    fmt.Printf("Sec-CH-UA: %s\n", uaResp.SecHeader)

    // Generate cookie
    cookieResp, err := sdk.GenerateDatadomeCookie(parallax.TaskDatadomeCookie{
        Site:        "example",
        Region:      "us",
        Proxyregion: "us",
        Proxy:       "http://user:pass@proxy:port",
        Pd:          parallax.PD_Init,
        Data:        parallax.TaskDatadomeCookieData{},
    })
    if err != nil {
        log.Fatalf("Failed to generate cookie: %v", err)
    }

    fmt.Printf("🍪 Cookie: %s\n", cookieResp.Message)
}
```

#### Full PerimeterX Example with Hold Captcha

```go
package main

import (
    "fmt"
    "log"
    parallax "github.com/yourusername/parallax-sdk"
)

func main() {
    sdk := parallax.NewPerimeterxSDK("YOUR_API_KEY", "")

    // Generate initial cookies
    response, err := sdk.GenerateCookies(parallax.TaskGeneratePXCookies{
        Site:        "example",
        Region:      "com",
        Proxyregion: "us",
        Proxy:       "http://user:pass@proxy:port",
    })
    if err != nil {
        log.Fatalf("Failed to generate cookies: %v", err)
    }

    fmt.Printf("🍪 _px3: %s\n", response.Cookie)
    fmt.Printf("🍪 _pxvid: %s\n", response.Vid)
    fmt.Printf("🍪 pxcts: %s\n", response.Cts)
    fmt.Printf("Flagged: %v, Maybe Flagged: %v\n", response.IsFlagged, response.IsMaybeFlagged)

    // If needed, generate hold captcha
    if response.Data != "" {
        holdResp, err := sdk.GenerateHoldCaptcha(parallax.TaskGenerateHoldCaptcha{
            Site:        "example",
            Region:      "com",
            Proxyregion: "us",
            Proxy:       "http://user:pass@proxy:port",
            Data:        response.Data,
        })
        if err != nil {
            log.Fatalf("Failed to generate hold captcha: %v", err)
        }
        fmt.Printf("🍪 Hold Captcha Cookie: %s\n", holdResp.Cookie)
    }
}
```

### TypeScript Examples

#### DataDome with Challenge URL Parsing

```typescript
import DatadomeSDK from "@parallax/sdk/datadome";

const sdk = new DatadomeSDK({ apiKey: "YOUR_API_KEY" });

// Parse challenge URL
const challengeUrl = "https://geo.captcha-delivery.com/captcha/?initialCid=abc&cid=def&e=xyz&s=123";
const currentCookie = "datadome=current_value";

const [taskData, productType] = sdk.parseChallengeUrl(challengeUrl, currentCookie);

// Generate cookie to solve challenge
const cookie = await sdk.generateCookie({
    site: "example",
    region: "us",
    proxy: "http://user:pass@proxy:port",
    proxyregion: "us",
    pd: productType,
    data: taskData
});

console.log(`🍪 Cookie: ${cookie.message}`);
console.log(`User-Agent: ${cookie.UserAgent}`);
```

#### PerimeterX with Hold Captcha

```typescript
import PerimeterxSDK from "@parallax/sdk/perimeterx";

const sdk = new PerimeterxSDK({ apiKey: "YOUR_API_KEY" });

// Generate initial cookies
const result = await sdk.generateCookies({
    site: "example",
    region: "com",
    proxy: "http://user:pass@proxy:port",
    proxyregion: "us"
});

console.log(`🍪 _px3: ${result.cookie}`);
console.log(`🍪 _pxvid: ${result.vid}`);
console.log(`🍪 pxcts: ${result.cts}`);

// If hold captcha is needed
if (result.data) {
    const holdResult = await sdk.generateHoldCaptcha({
        site: "example",
        region: "com",
        proxy: "http://user:pass@proxy:port",
        proxyregion: "us",
        data: result.data
    });

    console.log(`🍪 Hold Captcha Cookie: ${holdResult.cookie}`);
}
```

---

## 🛠️ Support

Need help? We're here for you!

- 💬 **Discord**: [Join our community](https://discord.com/invite/2QWbHcmWnf)
- 🌐 **Website**: [parallaxsystems.io](https://www.parallaxsystems.io)
- 📧 **Email**: support@parallaxsystems.io
- 📖 **Documentation**: [docs.parallaxsystems.io](https://docs.parallaxsystems.io)

---

<div align="center">

**Built with ❤️ by the Parallax Systems Team**

© 2024 Parallax Systems. All rights reserved.

</div>