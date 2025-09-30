# Python SDK Guide

Complete API reference for the Parallax Systems Python SDK.

## Installation

```bash
pip install parallax-sdk
# or
poetry add parallax-sdk
```

## Quick Start

```python
from parallax_sdk import DatadomeSDK

sdk = DatadomeSDK(api_key="YOUR_API_KEY")

response = sdk.generate_cookie(
    site="example",
    region="us",
    proxy="http://user:pass@proxy:port",
    proxyregion="us",
    pd="init"
)

print(response.message)
```

## API Reference

### DataDome SDK

#### Initialization

```python
from parallax_sdk import DatadomeSDK

sdk = DatadomeSDK(
    api_key: str,
    api_host: str = None  # Optional custom API host
)
```

**Parameters:**
- `api_key` (required) - Your Parallax API key
- `api_host` (optional) - Custom API host URL

**Example:**
```python
# Basic initialization
sdk = DatadomeSDK(api_key="YOUR_API_KEY")

# With custom host
sdk = DatadomeSDK(
    api_key="YOUR_API_KEY",
    api_host="https://custom.api.com"
)
```

#### Generate DataDome Cookie

```python
response = sdk.generate_cookie(
    site: str,
    region: str,
    proxy: str,
    proxyregion: str,
    pd: str,
    data: dict = None
) -> DatadomeCookieResponse
```

**Parameters:**
- `site` - Target site identifier
- `region` - Target region (e.g., "us", "eu", "de")
- `proxy` - Proxy URL (http://user:pass@host:port)
- `proxyregion` - Proxy region
- `pd` - Product type: "init", "captcha", "interstitial"
- `data` - Optional challenge data dictionary

**Response Attributes:**
- `error` (bool) - Whether an error occurred
- `message` (str) - Cookie value or error message
- `user_agent` (str) - Generated user agent

**Example:**
```python
response = sdk.generate_cookie(
    site="example",
    region="us",
    proxy="http://user:pass@proxy:port",
    proxyregion="us",
    pd="init",
    data={}
)

print(f"Cookie: {response.message}")
print(f"User-Agent: {response.user_agent}")
```

#### Generate DataDome Cookie with Challenge Data

```python
response = sdk.generate_cookie(
    site="example",
    region="us",
    proxy="http://user:pass@proxy:port",
    proxyregion="us",
    pd="captcha",
    data={
        "cid": "current_cookie_id",
        "e": "challenge_e",
        "s": "challenge_s",
        "b": "challenge_b",
        "initialCid": "initial_cookie_id"
    }
)
```

#### Generate User Agent

```python
response = sdk.generate_user_agent(
    site: str,
    region: str,
    pd: str = None
) -> UserAgentResponse
```

**Parameters:**
- `site` - Target site identifier
- `region` - Target region
- `pd` - Optional product type

**Response Attributes:**
- `error` (bool)
- `message` (str)
- `user_agent` (str)
- `sec_header` (str)
- `sec_full_version_list` (str)
- `sec_platform` (str)
- `sec_arch` (str)

**Example:**
```python
ua = sdk.generate_user_agent(
    site="example",
    region="us"
)

print(f"User-Agent: {ua.user_agent}")
print(f"sec-ch-ua: {ua.sec_header}")
```

#### Parse Challenge URL

```python
task_data, product_type = sdk.parse_challenge_url(
    url: str,
    current_cookie: str
) -> tuple[dict, str]
```

**Parameters:**
- `url` - DataDome challenge URL
- `current_cookie` - Current cookie value

**Returns:** Tuple of (task_data, product_type)

**Example:**
```python
challenge_url = "https://geo.captcha-delivery.com/captcha/?cid=xxx&e=yyy&s=zzz"
current_cookie = "datadome=old_value"

task_data, product_type = sdk.parse_challenge_url(challenge_url, current_cookie)

# Use extracted data
response = sdk.generate_cookie(
    site="example",
    region="us",
    proxy="http://user:pass@proxy:port",
    proxyregion="us",
    pd=product_type,
    data=task_data
)
```

### PerimeterX SDK

#### Initialization

```python
from parallax_sdk import PerimeterxSDK

sdk = PerimeterxSDK(
    api_key: str,
    api_host: str = None
)
```

**Example:**
```python
sdk = PerimeterxSDK(api_key="YOUR_API_KEY")
```

#### Generate PerimeterX Cookies

```python
response = sdk.generate_cookies(
    site: str,
    region: str,
    proxy: str,
    proxyregion: str
) -> PxCookieResponse
```

**Parameters:**
- `site` - Target site identifier
- `region` - Target region (e.g., "com", "us")
- `proxy` - Proxy URL
- `proxyregion` - Proxy region

**Response Attributes:**
- `error` (bool)
- `cookie` (str) - _px3 cookie value
- `vid` (str) - _pxvid value
- `cts` (str) - pxcts value
- `sec_header` (str)
- `is_flagged` (bool)
- `is_maybe_flagged` (bool)
- `user_agent` (str)
- `data` (str) - Data for hold captcha if needed

**Example:**
```python
result = sdk.generate_cookies(
    site="example",
    region="com",
    proxy="http://user:pass@proxy:port",
    proxyregion="us"
)

print(f"_px3: {result.cookie}")
print(f"_pxvid: {result.vid}")
print(f"pxcts: {result.cts}")
print(f"Flagged: {result.is_flagged}")
```

#### Generate Hold Captcha

```python
response = sdk.generate_hold_captcha(
    site: str,
    region: str,
    proxy: str,
    proxyregion: str,
    data: str,
    pow_pro: str = None
) -> PxCookieResponse
```

**Parameters:**
- `site` - Target site identifier
- `region` - Target region
- `proxy` - Proxy URL
- `proxyregion` - Proxy region
- `data` - Data from initial generate_cookies response
- `pow_pro` - Optional POW_PRO value

**Example:**
```python
# First generate initial cookies
initial = sdk.generate_cookies(
    site="example",
    region="com",
    proxy="http://user:pass@proxy:port",
    proxyregion="us"
)

# If data is present, generate hold captcha
if initial.data:
    hold = sdk.generate_hold_captcha(
        site="example",
        region="com",
        proxy="http://user:pass@proxy:port",
        proxyregion="us",
        data=initial.data
    )

    print(f"Hold Captcha Cookie: {hold.cookie}")
```

## Async Support

The SDK supports asyncio for concurrent operations:

```python
import asyncio
from parallax_sdk import DatadomeSDK

async def generate_multiple_cookies():
    sdk = DatadomeSDK(api_key="YOUR_API_KEY")

    tasks = [
        sdk.generate_cookie_async(
            site=f"site{i}",
            region="us",
            proxy="http://user:pass@proxy:port",
            proxyregion="us",
            pd="init"
        )
        for i in range(5)
    ]

    results = await asyncio.gather(*tasks)
    return results

# Run async function
results = asyncio.run(generate_multiple_cookies())
```

## Error Handling

### Basic Error Handling

```python
try:
    response = sdk.generate_cookie(
        site="example",
        region="us",
        proxy="http://user:pass@proxy:port",
        proxyregion="us",
        pd="init"
    )

    if response.error:
        print(f"Error: {response.message}")
    else:
        print(f"Success: {response.message}")

except Exception as e:
    print(f"Exception: {e}")
```

### Retry Logic

```python
import time

def generate_cookie_with_retry(sdk, max_retries=3):
    for attempt in range(max_retries):
        try:
            response = sdk.generate_cookie(
                site="example",
                region="us",
                proxy="http://user:pass@proxy:port",
                proxyregion="us",
                pd="init"
            )

            if not response.error:
                return response

            print(f"Attempt {attempt + 1} failed: {response.message}")

        except Exception as e:
            print(f"Attempt {attempt + 1} error: {e}")

        # Wait before retry
        time.sleep(1 * (attempt + 1))

    raise Exception("Max retries exceeded")
```

### Custom Exception Handling

```python
from parallax_sdk.exceptions import (
    AuthenticationError,
    RateLimitError,
    NetworkError,
    InvalidParameterError
)

try:
    response = sdk.generate_cookie(...)
except AuthenticationError:
    print("Invalid API key")
except RateLimitError:
    print("Rate limit exceeded, wait before retrying")
except NetworkError as e:
    print(f"Network error: {e}")
except InvalidParameterError as e:
    print(f"Invalid parameter: {e}")
```

## Context Manager

Use the SDK with a context manager for automatic resource cleanup:

```python
with DatadomeSDK(api_key="YOUR_API_KEY") as sdk:
    response = sdk.generate_cookie(
        site="example",
        region="us",
        proxy="http://user:pass@proxy:port",
        proxyregion="us",
        pd="init"
    )
    print(response.message)
```

## Complete Example

```python
from parallax_sdk import DatadomeSDK, PerimeterxSDK

def main():
    # Initialize SDKs
    dd_sdk = DatadomeSDK(api_key="YOUR_API_KEY")
    px_sdk = PerimeterxSDK(api_key="YOUR_API_KEY")

    try:
        # Generate DataDome cookie
        print("=== DataDome ===")
        dd_response = dd_sdk.generate_cookie(
            site="example",
            region="us",
            proxy="http://user:pass@proxy:port",
            proxyregion="us",
            pd="init",
            data={}
        )

        print(f"Cookie: {dd_response.message}")
        print(f"User-Agent: {dd_response.user_agent}\n")

        # Generate PerimeterX cookies
        print("=== PerimeterX ===")
        px_response = px_sdk.generate_cookies(
            site="example",
            region="com",
            proxy="http://user:pass@proxy:port",
            proxyregion="us"
        )

        print(f"_px3: {px_response.cookie}")
        print(f"_pxvid: {px_response.vid}")
        print(f"pxcts: {px_response.cts}")
        print(f"Flagged: {px_response.is_flagged}")

        # Handle hold captcha if needed
        if px_response.data:
            print("\n=== Hold Captcha Required ===")
            hold_response = px_sdk.generate_hold_captcha(
                site="example",
                region="com",
                proxy="http://user:pass@proxy:port",
                proxyregion="us",
                data=px_response.data
            )
            print(f"Hold Captcha Cookie: {hold_response.cookie}")

    except Exception as e:
        print(f"Error: {e}")

if __name__ == "__main__":
    main()
```

## Type Hints

The SDK is fully typed for better IDE support:

```python
from parallax_sdk import DatadomeSDK
from parallax_sdk.types import DatadomeCookieResponse, UserAgentResponse

def process_cookie(response: DatadomeCookieResponse) -> str:
    if response.error:
        raise ValueError(response.message)
    return response.message

sdk: DatadomeSDK = DatadomeSDK(api_key="YOUR_API_KEY")
response: DatadomeCookieResponse = sdk.generate_cookie(...)
cookie: str = process_cookie(response)
```

## Support

- 💬 [Discord Community](https://discord.com/invite/2QWbHcmWnf)
- 🌐 [Website](https://www.parallaxsystems.io)
- 📧 [Email Support](mailto:support@parallaxsystems.io)
- 📖 [Main Documentation](../README.md)