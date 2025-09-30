# TypeScript SDK Guide

Complete API reference for the Parallax Systems TypeScript/JavaScript SDK.

## Installation

```bash
npm install @parallax/sdk
# or
yarn add @parallax/sdk
# or
pnpm add @parallax/sdk
```

## Quick Start

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

## API Reference

### DataDome SDK

#### Initialization

```typescript
import DatadomeSDK from "@parallax/sdk/datadome";

const sdk = new DatadomeSDK({
    apiKey: string,
    apiHost?: string  // Optional custom API host
});
```

**Options:**
- `apiKey` (required) - Your Parallax API key
- `apiHost` (optional) - Custom API host URL

**Example:**
```typescript
const sdk = new DatadomeSDK({
    apiKey: "YOUR_API_KEY",
    apiHost: "https://custom.api.com"  // Optional
});
```

#### Generate DataDome Cookie

```typescript
const response = await sdk.generateCookie(params: GenerateCookieParams): Promise<DatadomeCookieResponse>
```

**GenerateCookieParams:**
```typescript
interface GenerateCookieParams {
    site: string;        // Target site identifier
    region: string;      // Target region (e.g., "us", "eu", "de")
    proxy: string;       // Proxy URL (http://user:pass@host:port)
    proxyregion: string; // Proxy region
    pd: string;          // Product type: "init", "captcha", "interstitial"
    data?: {             // Optional challenge data
        cid?: string;
        e?: string;
        s?: string;
        b?: string;
        initialCid?: string;
    };
}
```

**Response:**
```typescript
interface DatadomeCookieResponse {
    error: boolean;
    message: string;     // Cookie value (format: "datadome=value")
    UserAgent: string;   // Generated user agent
}
```

**Example:**
```typescript
const cookie = await sdk.generateCookie({
    site: "example",
    region: "us",
    proxy: "http://user:pass@proxy:port",
    proxyregion: "us",
    pd: "init",
    data: {}
});

console.log(`Cookie: ${cookie.message}`);
console.log(`User-Agent: ${cookie.UserAgent}`);
```

#### Generate User Agent

```typescript
const response = await sdk.generateUserAgent(params: GenerateUserAgentParams): Promise<UserAgentResponse>
```

**GenerateUserAgentParams:**
```typescript
interface GenerateUserAgentParams {
    site: string;   // Target site identifier
    region: string; // Target region
    pd?: string;    // Optional product type
}
```

**Response:**
```typescript
interface UserAgentResponse {
    error: boolean;
    message: string;
    UserAgent: string;
    secHeader: string;
    secFullVersionList: string;
    secPlatform: string;
    secArch: string;
}
```

**Example:**
```typescript
const ua = await sdk.generateUserAgent({
    site: "example",
    region: "us"
});

console.log(`User-Agent: ${ua.UserAgent}`);
console.log(`sec-ch-ua: ${ua.secHeader}`);
```

#### Parse Challenge URL

```typescript
const [taskData, productType] = sdk.parseChallengeUrl(url: string, currentCookie: string): [TaskData, ProductType]
```

**Parameters:**
- `url` - DataDome challenge URL
- `currentCookie` - Current cookie value

**Returns:** Tuple of `[TaskData, ProductType]`

**Example:**
```typescript
const challengeUrl = "https://geo.captcha-delivery.com/captcha/?cid=xxx&e=yyy&s=zzz";
const currentCookie = "datadome=old_value";

const [taskData, productType] = sdk.parseChallengeUrl(challengeUrl, currentCookie);

// Use extracted data
const cookie = await sdk.generateCookie({
    site: "example",
    region: "us",
    proxy: "http://user:pass@proxy:port",
    proxyregion: "us",
    pd: productType,
    data: taskData
});
```

### PerimeterX SDK

#### Initialization

```typescript
import PerimeterxSDK from "@parallax/sdk/perimeterx";

const sdk = new PerimeterxSDK({
    apiKey: string,
    apiHost?: string  // Optional custom API host
});
```

**Example:**
```typescript
const sdk = new PerimeterxSDK({
    apiKey: "YOUR_API_KEY"
});
```

#### Generate PerimeterX Cookies

```typescript
const response = await sdk.generateCookies(params: GeneratePXCookiesParams): Promise<PxCookieResponse>
```

**GeneratePXCookiesParams:**
```typescript
interface GeneratePXCookiesParams {
    site: string;        // Target site identifier
    region: string;      // Target region (e.g., "com", "us")
    proxy: string;       // Proxy URL
    proxyregion: string; // Proxy region
}
```

**Response:**
```typescript
interface PxCookieResponse {
    error: boolean;
    cookie: string;         // _px3 cookie value
    vid: string;            // _pxvid value
    cts: string;            // pxcts value
    secHeader: string;      // sec-ch-ua header
    isFlagged: boolean;     // Whether request was flagged
    isMaybeFlagged: boolean;// Whether request might be flagged
    UserAgent: string;      // Generated user agent
    data?: string;          // Data for hold captcha (if needed)
}
```

**Example:**
```typescript
const result = await sdk.generateCookies({
    site: "example",
    region: "com",
    proxy: "http://user:pass@proxy:port",
    proxyregion: "us"
});

console.log(`_px3: ${result.cookie}`);
console.log(`_pxvid: ${result.vid}`);
console.log(`pxcts: ${result.cts}`);
console.log(`Flagged: ${result.isFlagged}`);
```

#### Generate Hold Captcha

```typescript
const response = await sdk.generateHoldCaptcha(params: GenerateHoldCaptchaParams): Promise<PxCookieResponse>
```

**GenerateHoldCaptchaParams:**
```typescript
interface GenerateHoldCaptchaParams {
    site: string;
    region: string;
    proxy: string;
    proxyregion: string;
    data: string;         // Data from initial generateCookies response
    POW_PRO?: string;     // Optional POW_PRO value
}
```

**Example:**
```typescript
// First generate initial cookies
const initial = await sdk.generateCookies({
    site: "example",
    region: "com",
    proxy: "http://user:pass@proxy:port",
    proxyregion: "us"
});

// If data is present, generate hold captcha
if (initial.data) {
    const hold = await sdk.generateHoldCaptcha({
        site: "example",
        region: "com",
        proxy: "http://user:pass@proxy:port",
        proxyregion: "us",
        data: initial.data
    });

    console.log(`Hold Captcha Cookie: ${hold.cookie}`);
}
```

## TypeScript Types

### Import Types

```typescript
import type {
    GenerateCookieParams,
    DatadomeCookieResponse,
    GenerateUserAgentParams,
    UserAgentResponse,
    TaskData,
    ProductType
} from "@parallax/sdk/datadome";

import type {
    GeneratePXCookiesParams,
    GenerateHoldCaptchaParams,
    PxCookieResponse
} from "@parallax/sdk/perimeterx";
```

### Product Types

```typescript
type ProductType = "init" | "captcha" | "interstitial";
```

## Async/Await Patterns

### Sequential Execution

```typescript
async function generateMultipleCookies() {
    const sdk = new DatadomeSDK({ apiKey: "YOUR_API_KEY" });

    // Execute sequentially
    const cookie1 = await sdk.generateCookie({ site: "site1", ... });
    const cookie2 = await sdk.generateCookie({ site: "site2", ... });
    const cookie3 = await sdk.generateCookie({ site: "site3", ... });

    return [cookie1, cookie2, cookie3];
}
```

### Parallel Execution

```typescript
async function generateMultipleCookiesParallel() {
    const sdk = new DatadomeSDK({ apiKey: "YOUR_API_KEY" });

    // Execute in parallel
    const [cookie1, cookie2, cookie3] = await Promise.all([
        sdk.generateCookie({ site: "site1", ... }),
        sdk.generateCookie({ site: "site2", ... }),
        sdk.generateCookie({ site: "site3", ... })
    ]);

    return [cookie1, cookie2, cookie3];
}
```

### Error Handling

```typescript
async function generateCookieWithErrorHandling() {
    const sdk = new DatadomeSDK({ apiKey: "YOUR_API_KEY" });

    try {
        const cookie = await sdk.generateCookie({
            site: "example",
            region: "us",
            proxy: "http://user:pass@proxy:port",
            proxyregion: "us",
            pd: "init",
            data: {}
        });

        if (cookie.error) {
            console.error("Cookie generation failed:", cookie.message);
            return null;
        }

        return cookie;
    } catch (error) {
        console.error("Exception:", error);
        return null;
    }
}
```

### Retry Logic

```typescript
async function generateCookieWithRetry(maxRetries = 3) {
    const sdk = new DatadomeSDK({ apiKey: "YOUR_API_KEY" });

    for (let i = 0; i < maxRetries; i++) {
        try {
            const cookie = await sdk.generateCookie({
                site: "example",
                region: "us",
                proxy: "http://user:pass@proxy:port",
                proxyregion: "us",
                pd: "init",
                data: {}
            });

            if (!cookie.error) {
                return cookie;
            }

            console.log(`Attempt ${i + 1} failed, retrying...`);
        } catch (error) {
            console.error(`Attempt ${i + 1} error:`, error);
        }

        // Wait before retry
        await new Promise(resolve => setTimeout(resolve, 1000 * (i + 1)));
    }

    throw new Error("Max retries exceeded");
}
```

## Complete Example

```typescript
import DatadomeSDK from "@parallax/sdk/datadome";
import PerimeterxSDK from "@parallax/sdk/perimeterx";

async function main() {
    // Initialize SDKs
    const ddSDK = new DatadomeSDK({ apiKey: "YOUR_API_KEY" });
    const pxSDK = new PerimeterxSDK({ apiKey: "YOUR_API_KEY" });

    try {
        // Generate DataDome cookie
        console.log("=== DataDome ===");
        const ddCookie = await ddSDK.generateCookie({
            site: "example",
            region: "us",
            proxy: "http://user:pass@proxy:port",
            proxyregion: "us",
            pd: "init",
            data: {}
        });

        console.log(`Cookie: ${ddCookie.message}`);
        console.log(`User-Agent: ${ddCookie.UserAgent}\n`);

        // Generate PerimeterX cookies
        console.log("=== PerimeterX ===");
        const pxCookies = await pxSDK.generateCookies({
            site: "example",
            region: "com",
            proxy: "http://user:pass@proxy:port",
            proxyregion: "us"
        });

        console.log(`_px3: ${pxCookies.cookie}`);
        console.log(`_pxvid: ${pxCookies.vid}`);
        console.log(`pxcts: ${pxCookies.cts}`);
        console.log(`Flagged: ${pxCookies.isFlagged}`);

        // Handle hold captcha if needed
        if (pxCookies.data) {
            console.log("\n=== Hold Captcha Required ===");
            const holdCaptcha = await pxSDK.generateHoldCaptcha({
                site: "example",
                region: "com",
                proxy: "http://user:pass@proxy:port",
                proxyregion: "us",
                data: pxCookies.data
            });

            console.log(`Hold Captcha Cookie: ${holdCaptcha.cookie}`);
        }

    } catch (error) {
        console.error("Error:", error);
    }
}

main();
```

## JavaScript (Non-TypeScript) Usage

The SDK works with plain JavaScript too:

```javascript
const { DatadomeSDK } = require("@parallax/sdk/datadome");

const sdk = new DatadomeSDK({ apiKey: "YOUR_API_KEY" });

sdk.generateCookie({
    site: "example",
    region: "us",
    proxy: "http://user:pass@proxy:port",
    proxyregion: "us",
    pd: "init",
    data: {}
}).then(cookie => {
    console.log(cookie.message);
}).catch(error => {
    console.error(error);
});
```

## Support

- 💬 [Discord Community](https://discord.com/invite/2QWbHcmWnf)
- 🌐 [Website](https://www.parallaxsystems.io)
- 📧 [Email Support](mailto:support@parallaxsystems.io)
- 📖 [Main Documentation](../README.md)