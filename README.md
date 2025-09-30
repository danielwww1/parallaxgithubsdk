<div align="center">

<img src="logo.png" alt="Parallax Systems" width="60"/>

# Parallax Systems SDK
### Request-Based Anti-Bot Solution

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
- [Quick Start](#-quick-start)
- [Documentation](#-documentation)
- [Support](#-support)

---

## 🎯 Overview

Parallax Systems SDK is a multi-language SDK for generating valid anti-bot protection cookies through a simple request-based API. Bypass DataDome and PerimeterX protection layers with sub-400ms response times.

**Available in:**
- 🔷 **Go** - High-performance native implementation
- 🟦 **TypeScript** - Modern JavaScript/Node.js support
- 🐍 **Python** - Python 3.x compatibility
- 🎭 **Playwright** - Browser automation integration

**Key Highlights:**
- ⚡ **Fast**: DataDome ~200ms, PerimeterX ~350-400ms
- 🚀 **Simple**: Request-based API, no browser overhead
- 🔒 **Secure**: Industry-standard security practices
- 📦 **Production Ready**: Battle-tested and reliable

---

## ✨ Features

| Feature | DataDome | PerimeterX |
|---------|----------|------------|
| Cookie Generation | ✅ | ✅ |
| User Agent Generation | ✅ | ❌ |
| Proxy Support | ✅ | ✅ |
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

## 🚀 Quick Start

### DataDome Cookie Generation

**Go:**
```go
import parallax "github.com/yourusername/parallax-sdk"

sdk := parallax.NewSDK("YOUR_API_KEY", "")

response, err := sdk.GenerateDatadomeCookie(parallax.TaskDatadomeCookie{
    Site:        "example",
    Region:      "us",
    Proxyregion: "us",
    Proxy:       "http://user:pass@proxy:port",
    Pd:          parallax.PD_Init,
})

fmt.Printf("Cookie: %s\n", response.Message)
```

**TypeScript:**
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

console.log(cookie.message);
```

### PerimeterX Cookie Generation

**Go:**
```go
pxSDK := parallax.NewPerimeterxSDK("YOUR_API_KEY", "")

response, err := pxSDK.GenerateCookies(parallax.TaskGeneratePXCookies{
    Site:        "example",
    Region:      "com",
    Proxyregion: "us",
    Proxy:       "http://user:pass@proxy:port",
})

fmt.Printf("_px3: %s\n_pxvid: %s\npxcts: %s\n",
    response.Cookie, response.Vid, response.Cts)
```

**TypeScript:**
```typescript
import PerimeterxSDK from "@parallax/sdk/perimeterx";

const sdk = new PerimeterxSDK({ apiKey: "YOUR_API_KEY" });

const result = await sdk.generateCookies({
    site: "example",
    region: "com",
    proxy: "http://user:pass@proxy:port",
    proxyregion: "us"
});

console.log(`_px3: ${result.cookie}\n_pxvid: ${result.vid}\npxcts: ${result.cts}`);
```

---

## 📚 Documentation

### Detailed Guides

- **[DataDome Documentation](docs/datadome.md)** - Complete DataDome API reference, examples, and advanced usage
- **[PerimeterX Documentation](docs/perimeterx.md)** - Complete PerimeterX API reference, hold captcha, and examples
- **[Code Examples](docs/examples.md)** - Full working examples for all languages

### SDK-Specific Documentation

| Language | Documentation |
|----------|---------------|
| Go | [Go SDK Guide](docs/go.md) |
| TypeScript | [TypeScript SDK Guide](docs/typescript.md) |
| Python | [Python SDK Guide](docs/python.md) |
| Playwright | [Playwright SDK Guide](docs/playwright.md) |

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