# ualib Specification

A ghost library (specification-only library) for parsing user-agent strings into structured data. This library identifies devices, browsers, operating systems, and other characteristics from HTTP User-Agent headers.

## Core Functions

### 1. `parse(ua_string: string) -> UserAgent`

Parses a user-agent string into a structured object containing browser, OS, device, and engine information.

**Parameters:**

- `ua_string` (string): The user-agent string to parse

**Returns:**
A structured object (UserAgent) with the following properties:

```
{
  "browser": {
    "name": string,      // e.g., "Chrome", "Safari", "Firefox"
    "version": string,   // e.g., "120.0.6099.109"
    "major": string      // e.g., "120"
  },
  "os": {
    "name": string,      // e.g., "Windows", "Mac OS X", "Linux", "iOS", "Android"
    "version": string    // e.g., "10", "14.2", "11.0"
  },
  "device": {
    "type": string,      // e.g., "mobile", "tablet", "desktop", "bot", "wearable", "console", "smarttv", null
    "model": string,     // e.g., "iPhone", "iPad", "Pixel 6", null
    "vendor": string     // e.g., "Apple", "Samsung", "Google", null
  },
  "engine": {
    "name": string,      // e.g., "Blink", "WebKit", "Gecko", null
    "version": string    // e.g., "120.0.6099.109", null
  },
  "cpu": {
    "architecture": string  // e.g., "amd64", "arm64", "arm", null
  }
}
```

**Behavior:**

- Returns default/null values for unrecognized components
- Never throws exceptions on invalid input
- Handles empty strings gracefully
- All fields should be nullable when information is unavailable

**Example:**

```
Input: "Mozilla/5.0 (iPhone; CPU iPhone OS 16_0 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/16.0 Mobile/15E148 Safari/604.1"

Output:
{
  "browser": {"name": "Mobile Safari", "version": "16.0", "major": "16"},
  "os": {"name": "iOS", "version": "16.0"},
  "device": {"type": "mobile", "model": "iPhone", "vendor": "Apple"},
  "engine": {"name": "WebKit", "version": "605.1.15"},
  "cpu": {"architecture": null}
}
```

---

### 2. `is_mobile(ua_string: string) -> boolean`

Determines if the user-agent represents a mobile phone device.

**Parameters:**

- `ua_string` (string): The user-agent string to check

**Returns:**

- `true` if the user-agent is from a mobile phone (iPhone, Android phone, Windows Phone, BlackBerry, etc.)
- `false` otherwise

**Behavior:**

- Tablets should return `false` (use `is_tablet` for tablets)
- Based solely on UA string analysis: if `parse(ua).device.type == "mobile"`, return `true` (e.g., a desktop browser configured to send a mobile UA will return `true`)
- Bots should return `false` (use `is_bot` for bots)

**Example:**

```
Input: "Mozilla/5.0 (iPhone; CPU iPhone OS 16_0 like Mac OS X)"
Output: true

Input: "Mozilla/5.0 (iPad; CPU OS 16_0 like Mac OS X)"
Output: false
```

---

### 3. `is_tablet(ua_string: string) -> boolean`

Determines if the user-agent represents a tablet device.

**Parameters:**

- `ua_string` (string): The user-agent string to check

**Returns:**

- `true` if the user-agent is from a tablet (iPad, Android tablets, Kindle Fire, Surface, etc.)
- `false` otherwise

**Behavior:**

- Mobile phones should return `false`
- Bots should return `false` (use `is_bot` for bots)
- Desktop mode on tablets will return `false` if the UA string doesn't indicate tablet
- Large phones (phablets) should typically return `false` unless explicitly identified as tablets

**Example:**

```
Input: "Mozilla/5.0 (iPad; CPU OS 16_0 like Mac OS X)"
Output: true

Input: "Mozilla/5.0 (iPhone; CPU iPhone OS 16_0 like Mac OS X)"
Output: false
```

---

### 4. `is_pc(ua_string: string) -> boolean`

Determines if the user-agent represents a desktop/PC computer.

**Parameters:**

- `ua_string` (string): The user-agent string to check

**Returns:**

- `true` if the user-agent is from a desktop/laptop computer (Windows, Mac OS X, Linux desktop)
- `false` otherwise

**Behavior:**

- Based solely on UA string analysis: returns `true` only if `parse(ua).device.type == "desktop"`
- Returns `false` for mobile, tablet, bot, and all other device types

**Example:**

```
Input: "Mozilla/5.0 (Windows NT 10.0; Win64; x64) Chrome/120.0"
Output: true

Input: "Mozilla/5.0 (iPhone; CPU iPhone OS 16_0 like Mac OS X)"
Output: false
```

---

### 5. `is_bot(ua_string: string) -> boolean`

Determines if the user-agent represents a bot, crawler, or spider.

**Parameters:**

- `ua_string` (string): The user-agent string to check

**Returns:**

- `true` if the user-agent is from a bot/crawler/spider (Googlebot, Bingbot, search crawlers, monitoring services, etc.)
- `false` otherwise

**Behavior:**

- Should detect common search engine crawlers (Googlebot, Bingbot, Yahoo Slurp, Baiduspider, YandexBot, DuckDuckBot, Applebot)
- Should detect social media crawlers (Facebook, Twitter, LinkedIn, Slack)
- Should detect monitoring services (Pingdom, StatusCake, UptimeRobot)
- Should detect AI crawlers (GPTBot, ClaudeBot, Claude-Web, ChatGPT-User)
- Generic "bot" keywords in UA strings should trigger `true`
- Headless browsers without explicit bot identification should return `false`

**Example:**

```
Input: "Mozilla/5.0 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)"
Output: true

Input: "facebookexternalhit/1.1 (+http://www.facebook.com/externalhit_uatext.php)"
Output: true

Input: "Mozilla/5.0 (Windows NT 10.0; Win64; x64) Chrome/120.0"
Output: false
```

---

## Data Types & Constants

### Device Types

Valid values for `device.type`:

- `"mobile"` - Mobile phones
- `"tablet"` - Tablets and large-screen mobile devices
- `"desktop"` - Desktop and laptop computers
- `"bot"` - Crawlers, spiders, and automated agents
- `"wearable"` - Smartwatches and wearable devices
- `"console"` - Gaming consoles
- `"smarttv"` - Smart TVs and streaming devices
- `null` - Unknown or unclassifiable device

### Common Browsers

- Chrome, Firefox, Safari, Edge, Opera, Internet Explorer
- Mobile Safari, Chrome Mobile, Firefox Mobile, Samsung Internet
- UC Browser, Opera Mini, Brave, Vivaldi

**Note on Samsung Internet**: On Samsung Tizen Smart TVs, the browser is identified as "Samsung Internet" even though the UA string shows `TV Safari/537.36` instead of `SamsungBrowser/`. Detection heuristic: when `SMART-TV` token is present AND `Tizen` OS is detected, report browser as "Samsung Internet" with version from the `Version/` token. This is a special case — normally `Version/` indicates Safari version.

### Common Operating Systems

- Windows (7, 8, 8.1, 10, 11)
- Mac OS X / macOS (reported as "Mac OS X" in user-agent strings)
- Linux (Ubuntu, Debian, Fedora, etc.)
- iOS (iPhone OS)
- Android
- Chrome OS
- Tizen

### Common Engines

- Blink (Chrome, Edge, Opera) — identified by the frozen `AppleWebKit/537.36` token combined with a `Chrome/` version token. The Blink engine version is derived from the `Chrome/` version token
- WebKit (Safari, iOS browsers) — identified by `AppleWebKit/` with a non-frozen version (e.g., `605.1.15`), or when no `Chrome/` token is present
- Gecko (Firefox)
- Trident (Internet Explorer)
- EdgeHTML (legacy Edge) — identified by the `Edge/` token (with trailing 'e'). Chromium-based Edge uses the `Edg/` token (without trailing 'e') and uses Blink. The token name alone distinguishes them — no version comparison needed

### Common CPU Architectures

- amd64 (normalize from x64, x86_64)
- arm64 (normalize from aarch64)
- arm (normalize from armv7l)
- ia32 (normalize from x86)

---

## Edge Cases & Special Handling

### Empty or Invalid Input

- Empty strings should return default values (nulls for unknown data)
- Malformed UA strings should parse whatever information is recognizable
- Functions should never throw exceptions

### Ambiguous Cases

- When device type is ambiguous, prefer the most specific classification
- If both mobile and tablet indicators present, prefer tablet
- If bot-like and device-like indicators present, prefer bot

### Version Parsing

- Version strings should be kept as-is when possible
- `major` version should extract the first numeric segment
- Missing versions should return `null`
- OS versions should be mapped from the UA token to human-readable form (e.g., `Windows NT 10.0` → version `"10"`, `Windows NT 6.3` → version `"8.1"`). Note: NT 10.0 may be Windows 10 or 11; the parser cannot distinguish them
- For gaming consoles, the OS version represents the firmware/system software version extracted from the UA string (e.g., `PlayStation 5 4.03` → os.version `"4.03"`). The OS name may be `null` if no recognizable OS name token is present (e.g., PlayStation UAs contain a firmware version but no OS name like "Windows" or "Linux")

### WebView Detection

- Android WebView is identified by the presence of **both** `Version/4.0` **and** `Chrome/` tokens **on an Android OS**. The `Version/4.0` token alone is not sufficient — it must appear alongside a Chrome version token. Note: iOS also uses `Version/` tokens but for Safari version, not WebView detection
- Android WebView should be reported as `browser.name: "Chrome WebView"`
- iOS WKWebView is identified by the absence of both `Version/` and `Safari/` tokens combined with the presence of `Mobile/` in an iOS UA string
- iOS WKWebView should be reported as `browser.name: "Mobile Safari"` with `browser.version: null` and `browser.major: null` — the "Mobile Safari" name is a fallback for iOS WebKit contexts where no specific browser is identified

### Nullability

- All object properties should support null/undefined values
- Missing information should be explicitly `null`, not empty strings
- Boolean functions (`is_*`) always return definite `true` or `false`

### Encoding & Special Characters

- UA strings may contain special characters and should be handled safely
- URL-encoded characters in UA strings should be decoded if necessary
- Unicode characters should be supported

### Input Validation

- User-agent strings can be up to 2048 characters (practical browser limit)
- Strings exceeding 2048 characters should be truncated to 2048 before parsing
- Control characters (0x00-0x1F) should be stripped before parsing

---

## Implementation Notes

### Performance Considerations

- Implementations should optimize for speed on repeated parsing
- Consider caching parsed results if the same UA is checked multiple times
- Regex patterns should be pre-compiled where applicable
- Use atomic groups or possessive quantifiers in regex to prevent ReDoS attacks
- Set regex timeout limits (e.g., 100ms max per pattern)
- Consider rate-limiting parse() calls in production to prevent DoS

### Testing Requirements

- Must pass all test cases in `tests.yaml`
- Should handle real-world user-agent strings accurately
- **CRITICAL**: Boolean functions MUST maintain strict consistency with parse() results

### Boolean Function Implementation (REQUIRED)

The boolean functions MUST return true if and only if the device type matches:

```
is_mobile(ua) ⟺ parse(ua).device.type == "mobile"
is_tablet(ua) ⟺ parse(ua).device.type == "tablet"
is_pc(ua) ⟺ parse(ua).device.type == "desktop"
is_bot(ua) ⟺ parse(ua).device.type == "bot"
```

**This invariant must hold for ALL inputs, without exception.**

When `device.type` is `null` (unknown/unclassifiable), all four boolean functions return `false`.

**Bot Detection Priority**: When a user-agent is identified as a bot (contains bot signatures like "Googlebot", "bingbot", etc.), it should ALWAYS return `device.type = "bot"`, even if it also contains mobile/tablet indicators. This means:
- Googlebot Smartphone → `device.type = "bot"`, `is_mobile() = false`, `is_bot() = true`
- A bot takes precedence over all other device type classifications

### Language-Specific Adaptations

- Function names should follow the language's naming conventions (e.g., `is_mobile` in Python, `isMobile` in JavaScript)
- Return types should use the language's native types (objects, structs, classes, etc.)
- Null handling should follow language idioms (see below)

### Null Handling by Language

Different languages handle null/missing values differently. Use the appropriate idiom:

- **Python**: Use `None` for null values
- **JavaScript**: Use `null` (not `undefined`) for consistency
- **TypeScript**: Use `null` and mark fields as `string | null`
- **Go**: Use `nil` for pointers, empty string `""` for strings, or use pointer types (`*string`)
- **Rust**: Use `Option<String>` for nullable string fields
- **Java/C#**: Use `null` for reference types
- **Ruby**: Use `nil`
- **PHP**: Use `null`

### CPU Architecture Detection (Limited Availability)

**Important**: Most modern browsers (2020+) do not expose CPU architecture in user-agent strings for privacy reasons. Windows and Linux X11 UAs still include architecture tokens (e.g., `Win64; x64`, `Linux x86_64`), but iOS, Android, and modern macOS UAs do not. Desktop test cases (Windows, Linux X11) show `cpu.architecture: "amd64"`, while mobile, macOS, and most other platforms show `null`.

- Implementations should return `null` unless architecture is explicitly present in the UA string
- **DO NOT** infer architecture from OS (e.g., iOS → arm64) as this is unreliable and violates user privacy
- Only parse architecture if explicitly stated (e.g., "x86_64", "amd64", "arm64")

### Security Considerations

Implementations must handle user-agent strings safely:

- **Never echo raw user-agent strings** in error messages, logs, or responses (XSS risk)
- **Sanitize UA strings before display**: Strip control characters (0x00-0x1F)
- **Validate string length**: Truncate strings longer than 2048 characters before parsing
- **Prevent ReDoS**: Use timeouts on regex operations (max 100ms per pattern)
- **Rate limiting**: Consider limiting parse() calls per client to prevent DoS
- **No eval/exec**: Never execute code from user-agent strings
- **Log safely**: When logging UA strings, truncate to 200 characters and escape special chars

---

## Examples

### Desktop Chrome on Windows

```
UA: "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36"

parse() returns:
{
  "browser": {"name": "Chrome", "version": "120.0.0.0", "major": "120"},
  "os": {"name": "Windows", "version": "10"},
  "device": {"type": "desktop", "model": null, "vendor": null},
  "engine": {"name": "Blink", "version": "120.0.0.0"},
  "cpu": {"architecture": "amd64"}
}

is_mobile() -> false
is_tablet() -> false
is_pc() -> true
is_bot() -> false
```

### Safari on iPhone

```
UA: "Mozilla/5.0 (iPhone; CPU iPhone OS 17_2 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/17.2 Mobile/15E148 Safari/604.1"

parse() returns:
{
  "browser": {"name": "Mobile Safari", "version": "17.2", "major": "17"},
  "os": {"name": "iOS", "version": "17.2"},
  "device": {"type": "mobile", "model": "iPhone", "vendor": "Apple"},
  "engine": {"name": "WebKit", "version": "605.1.15"},
  "cpu": {"architecture": null}
}

is_mobile() -> true
is_tablet() -> false
is_pc() -> false
is_bot() -> false
```

### Chrome on iPad

```
UA: "Mozilla/5.0 (iPad; CPU OS 17_2 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) CriOS/120.0.6099.119 Mobile/15E148 Safari/604.1"

parse() returns:
{
  "browser": {"name": "Chrome Mobile iOS", "version": "120.0.6099.119", "major": "120"},
  "os": {"name": "iOS", "version": "17.2"},
  "device": {"type": "tablet", "model": "iPad", "vendor": "Apple"},
  "engine": {"name": "WebKit", "version": "605.1.15"},
  "cpu": {"architecture": null}
}

is_mobile() -> false
is_tablet() -> true
is_pc() -> false
is_bot() -> false
```

### Googlebot

```
UA: "Mozilla/5.0 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)"

parse() returns:
{
  "browser": {"name": "Googlebot", "version": "2.1", "major": "2"},
  "os": {"name": null, "version": null},
  "device": {"type": "bot", "model": null, "vendor": "Google"},
  "engine": {"name": null, "version": null},
  "cpu": {"architecture": null}
}

is_mobile() -> false
is_tablet() -> false
is_pc() -> false
is_bot() -> true
```

### Samsung Internet on Android

```
UA: "Mozilla/5.0 (Linux; Android 13; SM-S918B) AppleWebKit/537.36 (KHTML, like Gecko) SamsungBrowser/23.0 Chrome/115.0.0.0 Mobile Safari/537.36"

parse() returns:
{
  "browser": {"name": "Samsung Internet", "version": "23.0", "major": "23"},
  "os": {"name": "Android", "version": "13"},
  "device": {"type": "mobile", "model": "SM-S918B", "vendor": "Samsung"},
  "engine": {"name": "Blink", "version": "115.0.0.0"},
  "cpu": {"architecture": null}
}

is_mobile() -> true
is_tablet() -> false
is_pc() -> false
is_bot() -> false
```

---

## Version

Specification Version: 1.0.0
Last Updated: 2026-04-21
