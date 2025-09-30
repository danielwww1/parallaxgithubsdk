# Playwright SDK Guide

Complete guide for integrating Parallax Systems with Playwright for browser automation.

## Installation

```bash
npm install @parallax/playwright-sdk
# Also install Playwright if not already installed
npm install playwright
```

## Quick Start

```typescript
import { chromium } from 'playwright';
import { ParallaxPlaywright } from '@parallax/playwright-sdk';

const browser = await chromium.launch();
const context = await browser.newContext();

// Initialize Parallax integration
const parallax = new ParallaxPlaywright({
    apiKey: 'YOUR_API_KEY',
    context: context
});

// Navigate with anti-bot bypass
await parallax.navigateWithBypass('https://protected-site.com', {
    site: 'protected-site',
    region: 'us',
    proxy: 'http://user:pass@proxy:port'
});

const page = context.pages()[0];
await page.screenshot({ path: 'result.png' });

await browser.close();
```

## API Reference

### Initialization

```typescript
import { ParallaxPlaywright } from '@parallax/playwright-sdk';
import { BrowserContext } from 'playwright';

const parallax = new ParallaxPlaywright({
    apiKey: string,
    context: BrowserContext,
    apiHost?: string  // Optional custom API host
});
```

**Parameters:**
- `apiKey` (required) - Your Parallax API key
- `context` (required) - Playwright BrowserContext
- `apiHost` (optional) - Custom API host URL

### Navigate with DataDome Bypass

```typescript
await parallax.navigateWithBypass(
    url: string,
    options: NavigateWithBypassOptions
): Promise<Page>
```

**NavigateWithBypassOptions:**
```typescript
interface NavigateWithBypassOptions {
    site: string;        // Target site identifier
    region: string;      // Target region
    proxy?: string;      // Optional proxy URL
    proxyregion?: string;// Optional proxy region
    waitUntil?: 'load' | 'domcontentloaded' | 'networkidle';
}
```

**Example:**
```typescript
const page = await parallax.navigateWithBypass('https://example.com', {
    site: 'example',
    region: 'us',
    proxy: 'http://user:pass@proxy:port',
    proxyregion: 'us',
    waitUntil: 'networkidle'
});
```

### Set Cookies from Parallax

```typescript
await parallax.setDatadomeCookies(
    page: Page,
    site: string,
    region: string,
    proxy?: string,
    proxyregion?: string
): Promise<void>
```

**Example:**
```typescript
import { chromium } from 'playwright';

const browser = await chromium.launch();
const page = await browser.newPage();

await parallax.setDatadomeCookies(page, 'example', 'us',
    'http://user:pass@proxy:port', 'us');

await page.goto('https://example.com');
```

### Set PerimeterX Cookies

```typescript
await parallax.setPerimeterXCookies(
    page: Page,
    site: string,
    region: string,
    proxy?: string,
    proxyregion?: string
): Promise<void>
```

**Example:**
```typescript
await parallax.setPerimeterXCookies(page, 'example', 'com',
    'http://user:pass@proxy:port', 'us');

await page.goto('https://example.com');
```

### Get Cookies Manually

```typescript
const cookies = await parallax.getDatadomeCookies(
    site: string,
    region: string,
    proxy?: string,
    proxyregion?: string
): Promise<DatadomeCookieResponse>
```

**Example:**
```typescript
const cookies = await parallax.getDatadomeCookies('example', 'us');

console.log(`Cookie: ${cookies.message}`);
console.log(`User-Agent: ${cookies.UserAgent}`);

// Set manually if needed
await page.context().addCookies([{
    name: 'datadome',
    value: cookies.message.split('=')[1],
    domain: '.example.com',
    path: '/'
}]);
```

## Browser Integration Patterns

### Complete DataDome Flow

```typescript
import { chromium } from 'playwright';
import { ParallaxPlaywright } from '@parallax/playwright-sdk';

async function scrapeProtectedSite() {
    const browser = await chromium.launch({ headless: true });
    const context = await browser.newContext();

    const parallax = new ParallaxPlaywright({
        apiKey: 'YOUR_API_KEY',
        context: context
    });

    try {
        // Navigate with automatic bypass
        const page = await parallax.navigateWithBypass('https://protected-site.com', {
            site: 'protected-site',
            region: 'us',
            proxy: 'http://user:pass@proxy:port',
            proxyregion: 'us',
            waitUntil: 'networkidle'
        });

        // Your scraping logic
        const data = await page.evaluate(() => {
            return document.querySelector('.data').textContent;
        });

        console.log('Scraped data:', data);

        return data;
    } finally {
        await browser.close();
    }
}

scrapeProtectedSite();
```

### Manual Cookie Management

```typescript
import { chromium } from 'playwright';
import { ParallaxPlaywright } from '@parallax/playwright-sdk';

async function manualCookieFlow() {
    const browser = await chromium.launch();
    const context = await browser.newContext();
    const page = await context.newPage();

    const parallax = new ParallaxPlaywright({
        apiKey: 'YOUR_API_KEY',
        context: context
    });

    // Get cookies from Parallax
    const ddCookies = await parallax.getDatadomeCookies('example', 'us');

    // Set user agent
    await page.setExtraHTTPHeaders({
        'User-Agent': ddCookies.UserAgent
    });

    // Set cookies
    await context.addCookies([{
        name: 'datadome',
        value: ddCookies.message.split('=')[1],
        domain: '.example.com',
        path: '/',
        httpOnly: true,
        secure: true
    }]);

    // Navigate
    await page.goto('https://example.com');

    // Continue with automation
    await page.screenshot({ path: 'result.png' });

    await browser.close();
}
```

### PerimeterX Integration

```typescript
import { chromium } from 'playwright';
import { ParallaxPlaywright } from '@parallax/playwright-sdk';

async function perimeterXFlow() {
    const browser = await chromium.launch();
    const context = await browser.newContext();
    const page = await context.newPage();

    const parallax = new ParallaxPlaywright({
        apiKey: 'YOUR_API_KEY',
        context: context
    });

    // Get PerimeterX cookies
    const pxCookies = await parallax.getPerimeterXCookies('example', 'com',
        'http://user:pass@proxy:port', 'us');

    // Set user agent
    await page.setExtraHTTPHeaders({
        'User-Agent': pxCookies.UserAgent
    });

    // Set all PX cookies
    await context.addCookies([
        {
            name: '_px3',
            value: pxCookies.cookie,
            domain: '.example.com',
            path: '/'
        },
        {
            name: '_pxvid',
            value: pxCookies.vid,
            domain: '.example.com',
            path: '/'
        },
        {
            name: 'pxcts',
            value: pxCookies.cts,
            domain: '.example.com',
            path: '/'
        }
    ]);

    // Navigate
    await page.goto('https://example.com');

    await browser.close();
}
```

### Handle Challenge Detection

```typescript
import { chromium } from 'playwright';
import { ParallaxPlaywright } from '@parallax/playwright-sdk';

async function handleChallenges() {
    const browser = await chromium.launch();
    const context = await browser.newContext();

    const parallax = new ParallaxPlaywright({
        apiKey: 'YOUR_API_KEY',
        context: context
    });

    const page = await parallax.navigateWithBypass('https://protected-site.com', {
        site: 'protected-site',
        region: 'us'
    });

    // Check if challenge is detected
    const isChallenged = await page.evaluate(() => {
        return window.location.href.includes('captcha') ||
               document.querySelector('.challenge-form') !== null;
    });

    if (isChallenged) {
        console.log('Challenge detected, solving...');

        // Get challenge URL
        const challengeUrl = page.url();
        const currentCookie = await page.evaluate(() => {
            return document.cookie.match(/datadome=([^;]+)/)?.[1] || '';
        });

        // Solve challenge
        const solvedCookies = await parallax.solveDatadomeChallenge(
            challengeUrl,
            currentCookie,
            'protected-site',
            'us'
        );

        // Set new cookies
        await context.addCookies([{
            name: 'datadome',
            value: solvedCookies.message.split('=')[1],
            domain: '.protected-site.com',
            path: '/'
        }]);

        // Reload page
        await page.reload({ waitUntil: 'networkidle' });
    }

    await browser.close();
}
```

## Advanced Patterns

### Persistent Context

```typescript
import { chromium } from 'playwright';
import { ParallaxPlaywright } from '@parallax/playwright-sdk';

async function usePersistentContext() {
    const browser = await chromium.launchPersistentContext('./user-data', {
        headless: false
    });

    const parallax = new ParallaxPlaywright({
        apiKey: 'YOUR_API_KEY',
        context: browser
    });

    const page = await parallax.navigateWithBypass('https://example.com', {
        site: 'example',
        region: 'us'
    });

    // Cookies are saved to disk
    await browser.close();
}
```

### Multiple Pages

```typescript
import { chromium } from 'playwright';
import { ParallaxPlaywright } from '@parallax/playwright-sdk';

async function multiplePages() {
    const browser = await chromium.launch();
    const context = await browser.newContext();

    const parallax = new ParallaxPlaywright({
        apiKey: 'YOUR_API_KEY',
        context: context
    });

    // Set cookies once for the context
    await parallax.setDatadomeCookies(
        await context.newPage(),
        'example',
        'us'
    );

    // All pages in this context will have the cookies
    const page1 = await context.newPage();
    const page2 = await context.newPage();

    await Promise.all([
        page1.goto('https://example.com/page1'),
        page2.goto('https://example.com/page2')
    ]);

    await browser.close();
}
```

### Proxy Rotation

```typescript
import { chromium } from 'playwright';
import { ParallaxPlaywright } from '@parallax/playwright-sdk';

async function proxyRotation() {
    const proxies = [
        'http://user:pass@proxy1:port',
        'http://user:pass@proxy2:port',
        'http://user:pass@proxy3:port'
    ];

    for (const proxy of proxies) {
        const browser = await chromium.launch({
            proxy: { server: proxy }
        });

        const context = await browser.newContext();
        const parallax = new ParallaxPlaywright({
            apiKey: 'YOUR_API_KEY',
            context: context
        });

        await parallax.navigateWithBypass('https://example.com', {
            site: 'example',
            region: 'us',
            proxy: proxy,
            proxyregion: 'us'
        });

        // Do work...

        await browser.close();
    }
}
```

## Complete Example

```typescript
import { chromium, Browser, BrowserContext, Page } from 'playwright';
import { ParallaxPlaywright } from '@parallax/playwright-sdk';

async function completeExample() {
    let browser: Browser | null = null;

    try {
        // Launch browser
        browser = await chromium.launch({
            headless: true,
            proxy: { server: 'http://user:pass@proxy:port' }
        });

        // Create context
        const context: BrowserContext = await browser.newContext({
            viewport: { width: 1920, height: 1080 },
            userAgent: 'Mozilla/5.0...' // Will be overridden by Parallax
        });

        // Initialize Parallax
        const parallax = new ParallaxPlaywright({
            apiKey: 'YOUR_API_KEY',
            context: context
        });

        // Navigate with automatic bypass
        console.log('Navigating to protected site...');
        const page: Page = await parallax.navigateWithBypass(
            'https://protected-site.com',
            {
                site: 'protected-site',
                region: 'us',
                proxy: 'http://user:pass@proxy:port',
                proxyregion: 'us',
                waitUntil: 'networkidle'
            }
        );

        console.log('Navigation successful!');

        // Perform automation tasks
        await page.waitForSelector('.content');
        const content = await page.textContent('.content');
        console.log('Content:', content);

        // Take screenshot
        await page.screenshot({
            path: 'result.png',
            fullPage: true
        });

        console.log('Screenshot saved!');

    } catch (error) {
        console.error('Error:', error);
    } finally {
        if (browser) {
            await browser.close();
        }
    }
}

completeExample();
```

## Support

- 💬 [Discord Community](https://discord.com/invite/2QWbHcmWnf)
- 🌐 [Website](https://www.parallaxsystems.io)
- 📧 [Email Support](mailto:support@parallaxsystems.io)
- 📖 [Main Documentation](../README.md)