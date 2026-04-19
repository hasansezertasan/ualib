# ualib

**A user-agent parsing library with no code.**

ualib is a "ghost library" — it contains no implementation code, only a complete specification and comprehensive test suite. You use an AI coding assistant to generate an implementation in any programming language.

## Why?

Modern AI coding assistants like Claude, Cursor, and GitHub Copilot can reliably implement well-specified utilities. Instead of distributing code across multiple languages, ualib provides:

- **SPEC.md** - Complete behavioral specification for all functions
- **tests.yaml** - 80+ language-agnostic test cases
- **INSTALL.md** - Instructions for AI-assisted implementation

This approach works well for focused utilities with clear specifications and no complex dependencies.

## What Does It Do?

ualib parses HTTP User-Agent strings to identify browsers, operating systems, devices, and bots. It provides five core functions:

```python
# Parse a complete user-agent string
ua = parse("Mozilla/5.0 (iPhone; CPU iPhone OS 17_2 like Mac OS X)...")
# Returns: UserAgent object with browser, os, device, engine, cpu details

# Quick device type checks
is_mobile(ua_string)   # True for phones
is_tablet(ua_string)   # True for tablets
is_pc(ua_string)       # True for desktop/laptop
is_bot(ua_string)      # True for crawlers/bots
```

## Features

✅ **Parse complete user-agent strings** into structured data (browser, OS, device, engine, CPU)
✅ **Detect device types** - mobile phones, tablets, desktops, bots, consoles, wearables, TVs
✅ **Identify browsers** - Chrome, Firefox, Safari, Edge, Opera, and 30+ others
✅ **Recognize operating systems** - Windows, macOS, Linux, iOS, Android, Chrome OS
✅ **Detect bots & crawlers** - Search engines, social media, AI crawlers, monitoring services
✅ **Handle edge cases** - Empty strings, malformed input, unicode, very long strings
✅ **80+ comprehensive test cases** covering real-world user-agent strings

## Quick Start

### 1. Get the specification files

```bash
# Clone the repository
git clone https://github.com/hasansezertasan/ualib.git
cd ualib

# Or download just the essential files
curl -O https://raw.githubusercontent.com/hasansezertasan/ualib/main/SPEC.md
curl -O https://raw.githubusercontent.com/hasansezertasan/ualib/main/tests.yaml
```

### 2. Ask your AI to implement it

Copy this prompt to Claude, Cursor, GitHub Copilot, or any AI coding assistant:

```
I need you to implement the ualib user-agent parsing library based on the provided specification.

Requirements:
1. Read SPEC.md to understand the complete behavioral specification
2. Implement all 5 functions: parse(), is_mobile(), is_tablet(), is_pc(), is_bot()
3. Follow the language conventions for [YOUR LANGUAGE]
4. All functions should handle edge cases gracefully (empty strings, malformed input, etc.)
5. Make sure all test cases in tests.yaml pass

Target language: [Python/JavaScript/Ruby/Go/Rust/etc.]

Create a complete, production-ready implementation with:
- Type hints/annotations (if the language supports them)
- Comprehensive error handling
- Documentation strings/comments
- A test suite that validates all test cases from tests.yaml

Let's start with the core data structures and the parse() function.
```

### 3. Use it in your project

After implementation, you'll have a consistent API across any language:

**Python**

```python
from ualib import parse, is_mobile, is_tablet, is_pc, is_bot

# Parse full details
ua = parse("Mozilla/5.0 (iPhone; CPU iPhone OS 17_2 like Mac OS X)...")
print(ua.browser.name)    # "Mobile Safari"
print(ua.os.name)         # "iOS"
print(ua.device.type)     # "mobile"
print(ua.device.vendor)   # "Apple"

# Quick checks
if is_mobile(request.headers['User-Agent']):
    return mobile_template()
```

**JavaScript/TypeScript**

```javascript
import { parse, isMobile, isTablet, isPc, isBot } from "ualib";

// Parse full details
const ua = parse("Mozilla/5.0 (iPhone; CPU iPhone OS 17_2 like Mac OS X)...");
console.log(ua.browser.name); // "Mobile Safari"
console.log(ua.os.name); // "iOS"
console.log(ua.device.type); // "mobile"

// Quick checks
if (isMobile(req.headers["user-agent"])) {
  return mobileView();
}
```

**Go**

```go
import "github.com/yourusername/ualib"

// Parse full details
ua := ualib.Parse("Mozilla/5.0 (iPhone; CPU iPhone OS 17_2 like Mac OS X)...")
fmt.Println(ua.Browser.Name)    // "Mobile Safari"
fmt.Println(ua.OS.Name)         // "iOS"
fmt.Println(ua.Device.Type)     // "mobile"

// Quick checks
if ualib.IsMobile(r.Header.Get("User-Agent")) {
    return mobileHandler(w, r)
}
```

## What's Included

### Files in This Repository

- **README.md** (this file) - Project overview and quick start
- **SPEC.md** - Complete behavioral specification with examples
- **tests.yaml** - 80+ language-agnostic test cases
- **INSTALL.md** - Detailed implementation instructions
- **LICENSE** - MIT License

### No Code!

This repository intentionally contains **no implementation code**. Instead, you generate a production-ready implementation in your language of choice using an AI coding assistant.

## Supported Detections

### Device Types

- **Mobile phones** - iPhone, Android phones, Windows Phone, BlackBerry
- **Tablets** - iPad, Android tablets, Kindle Fire, Surface
- **Desktops** - Windows, macOS, Linux PCs
- **Bots** - Search engines, social media crawlers, AI agents, monitoring services
- **Consoles** - PlayStation, Xbox, Nintendo Switch
- **Wearables** - Apple Watch, Android Wear
- **Smart TVs** - Samsung, LG, Roku, Fire TV

### Browsers

Chrome, Firefox, Safari, Edge, Opera, Internet Explorer, Brave, Vivaldi, Samsung Internet, UC Browser, Opera Mini, Mobile Safari, Chrome Mobile, Firefox Mobile, and more.

### Operating Systems

Windows (7, 8, 8.1, 10, 11), macOS, Linux, iOS, Android, Chrome OS, Windows Phone, BlackBerry OS, Tizen, Watch OS, and more.

### Bots & Crawlers

Googlebot, Bingbot, Yahoo Slurp, Baiduspider, Yandex Bot, DuckDuckBot, Facebook, Twitter, LinkedIn, Slack, ChatGPT, GPTBot, Claude-Web, Pingdom, UptimeRobot, and more.

## Use Cases

### Web Analytics

```python
# Segment visitors by device type
ua = parse(request.headers.get('User-Agent'))
analytics.track('page_view', {
    'device_type': ua.device.type,
    'browser': ua.browser.name,
    'os': ua.os.name
})
```

### Responsive Design

```javascript
// Serve different views based on device
if (isMobile(userAgent)) {
  return renderMobileApp();
} else if (isTablet(userAgent)) {
  return renderTabletApp();
} else {
  return renderDesktopApp();
}
```

### Bot Detection

```python
# Block or rate-limit bots
if is_bot(request.headers.get('User-Agent')):
    return rate_limited_response()
```

### Feature Detection

```javascript
// Enable/disable features based on browser
const ua = parse(userAgent);
if (ua.browser.name === "IE" && parseInt(ua.browser.major) < 11) {
  showBrowserUpgradeMessage();
}
```

### Security & Fraud Prevention

```python
# Flag suspicious user-agent patterns
ua = parse(user_agent)
if ua.device.type == 'bot' and not is_verified_bot(ua):
    security_log.warning('Suspicious bot activity')
```

## Testing Your Implementation

Your implementation should pass all 80+ test cases in `tests.yaml`. The tests cover:

- ✅ Desktop browsers (Windows, macOS, Linux)
- ✅ Mobile phones (iOS, Android)
- ✅ Tablets (iPad, Android tablets)
- ✅ Various browsers (Chrome, Firefox, Safari, Edge, Opera, etc.)
- ✅ Modern browsers (Arc, Brave, DuckDuckGo, Tor, Opera GX)
- ✅ International browsers (Yandex, QQ Browser)
- ✅ Search engine bots (Google, Bing, Yahoo, Baidu, Yandex)
- ✅ Social media crawlers (Facebook, Twitter, LinkedIn, Slack)
- ✅ AI crawlers (ChatGPT, GPTBot, Claude-Web)
- ✅ Monitoring services (Pingdom, UptimeRobot)
- ✅ Gaming consoles (PlayStation, Nintendo Switch)
- ✅ Smart TVs and wearables
- ✅ Legacy devices (BlackBerry, Windows Phone)
- ✅ WebView contexts (Android WebView, iOS WKWebView)
- ✅ Headless browsers (Puppeteer, Playwright, Selenium)
- ✅ Edge cases (empty strings, malformed input, unicode, very long strings)

## Quick Validation

Verify your implementation is working correctly with these quick tests:

### Python

```python
from ualib import parse, is_mobile, is_tablet, is_pc, is_bot

# Test 1: Mobile detection
assert is_mobile("Mozilla/5.0 (iPhone; CPU iPhone OS 17_2 like Mac OS X)") == True
assert parse("Mozilla/5.0 (iPhone; CPU iPhone OS 17_2 like Mac OS X)").device.type == "mobile"

# Test 2: Tablet detection
assert is_tablet("Mozilla/5.0 (iPad; CPU OS 17_2 like Mac OS X)") == True
assert parse("Mozilla/5.0 (iPad; CPU OS 17_2 like Mac OS X)").device.type == "tablet"

# Test 3: Desktop detection
assert is_pc("Mozilla/5.0 (Windows NT 10.0; Win64; x64) Chrome/120.0") == True
assert parse("Mozilla/5.0 (Windows NT 10.0; Win64; x64) Chrome/120.0").device.type == "desktop"

# Test 4: Bot detection
assert is_bot("Mozilla/5.0 (compatible; Googlebot/2.1)") == True
assert parse("Mozilla/5.0 (compatible; Googlebot/2.1)").device.type == "bot"

# Test 5: Consistency check (CRITICAL)
ua = "Mozilla/5.0 (iPhone; CPU iPhone OS 17_2 like Mac OS X)"
assert is_mobile(ua) == (parse(ua).device.type == "mobile")
assert is_tablet(ua) == (parse(ua).device.type == "tablet")
assert is_pc(ua) == (parse(ua).device.type == "desktop")
assert is_bot(ua) == (parse(ua).device.type == "bot")

print("✅ All validation tests passed!")
```

### JavaScript/TypeScript

```javascript
import { parse, isMobile, isTablet, isPc, isBot } from "ualib";

// Test 1: Mobile detection
console.assert(isMobile("Mozilla/5.0 (iPhone; CPU iPhone OS 17_2 like Mac OS X)") === true);
console.assert(parse("Mozilla/5.0 (iPhone; CPU iPhone OS 17_2 like Mac OS X)").device.type === "mobile");

// Test 2: Tablet detection
console.assert(isTablet("Mozilla/5.0 (iPad; CPU OS 17_2 like Mac OS X)") === true);
console.assert(parse("Mozilla/5.0 (iPad; CPU OS 17_2 like Mac OS X)").device.type === "tablet");

// Test 3: Desktop detection
console.assert(isPc("Mozilla/5.0 (Windows NT 10.0; Win64; x64) Chrome/120.0") === true);
console.assert(parse("Mozilla/5.0 (Windows NT 10.0; Win64; x64) Chrome/120.0").device.type === "desktop");

// Test 4: Bot detection
console.assert(isBot("Mozilla/5.0 (compatible; Googlebot/2.1)") === true);
console.assert(parse("Mozilla/5.0 (compatible; Googlebot/2.1)").device.type === "bot");

// Test 5: Consistency check (CRITICAL)
const ua = "Mozilla/5.0 (iPhone; CPU iPhone OS 17_2 like Mac OS X)";
console.assert(isMobile(ua) === (parse(ua).device.type === "mobile"));
console.assert(isTablet(ua) === (parse(ua).device.type === "tablet"));
console.assert(isPc(ua) === (parse(ua).device.type === "desktop"));
console.assert(isBot(ua) === (parse(ua).device.type === "bot"));

console.log("✅ All validation tests passed!");
```

### Go

```go
package main

import (
    "fmt"
    "github.com/yourusername/ualib"
)

func main() {
    // Test 1: Mobile detection
    if !ualib.IsMobile("Mozilla/5.0 (iPhone; CPU iPhone OS 17_2 like Mac OS X)") {
        panic("Mobile detection failed")
    }

    // Test 2: Consistency check (CRITICAL)
    ua := "Mozilla/5.0 (iPhone; CPU iPhone OS 17_2 like Mac OS X)"
    parsed := ualib.Parse(ua)
    if ualib.IsMobile(ua) != (parsed.Device.Type == "mobile") {
        panic("Consistency check failed")
    }

    fmt.Println("✅ All validation tests passed!")
}
```

**Important**: The consistency check (Test 5) is **critical**. The boolean functions MUST always match the `device.type` from `parse()`. If this test fails, your implementation violates the specification.

## Implementation Status

Community implementations in various languages:

| Language              | Status     | Repository | Maintainer |
| --------------------- | ---------- | ---------- | ---------- |
| Python                | ⏳ Planned | -          | -          |
| JavaScript/TypeScript | ⏳ Planned | -          | -          |
| Go                    | ⏳ Planned | -          | -          |
| Rust                  | ⏳ Planned | -          | -          |
| Ruby                  | ⏳ Planned | -          | -          |
| PHP                   | ⏳ Planned | -          | -          |
| Java                  | ⏳ Planned | -          | -          |
| C#                    | ⏳ Planned | -          | -          |

> **Want to contribute an implementation?** Create a repository (e.g., `ualib-python`, `ualib-js`) and submit a PR to add it here!

## When Should You Use This?

**✅ Good fit:**

- Need user-agent parsing in a specific language
- Want a consistent API across multiple languages
- Building a simple API or web application
- Learning about user-agent parsing
- Trust AI to implement well-specified utilities

**❌ Not recommended:**

- Need maximum parsing accuracy (use battle-tested libraries like ua-parser)
- Performance is absolutely critical
- Can't review AI-generated code
- Need guaranteed long-term support and updates

For production use cases requiring maximum accuracy, consider using established libraries that are actively maintained and tested against millions of real-world user-agent strings.

## Underlying Libraries

Your AI assistant may use existing parsing libraries as the foundation:

- **Python**: [user-agents](https://github.com/selwin/python-user-agents), [ua-parser](https://github.com/ua-parser/uap-python)
- **JavaScript**: [ua-parser-js](https://github.com/faisalman/ua-parser-js), [bowser](https://github.com/lancedikson/bowser)
- **Go**: [user_agent](https://github.com/mssola/user_agent), [uap-go](https://github.com/ua-parser/uap-go)
- **Ruby**: [user_agent_parser](https://github.com/ua-parser/uap-ruby)
- **Rust**: [woothee](https://github.com/woothee/woothee-rust), [uaparser](https://github.com/ua-parser/uap-rust)
- **PHP**: [phpuseragentparser](https://github.com/donatj/PhpUserAgent), [whichbrowser](https://github.com/WhichBrowser/Parser-PHP)

The goal is to provide a **consistent, well-specified API** across languages, even if the underlying implementation varies.

## Contributing

We welcome contributions to improve the **specification and tests**:

### What We Accept

✅ **New test cases** - Real-world user-agent strings that should be parsed correctly
✅ **Specification improvements** - Clarifications, edge cases, better examples
✅ **Documentation updates** - Better explanations, use cases, examples
✅ **Bug reports** - Issues with the specification or test cases
✅ **Implementation links** - Share your language implementation to be listed above

### What We Don't Accept

❌ **Code implementations** - This is a ghost library! Create a separate repo for implementations
❌ **Dependencies** - The spec should remain language-agnostic

### How to Contribute

1. Fork this repository
2. Make your changes to `SPEC.md`, `tests.yaml`, or documentation
3. Test that your changes are clear and unambiguous
4. Submit a pull request with a clear description

## Inspiration & Acknowledgements

ualib is inspired by the "ghost library" concept pioneered by:

- **[whenwords](https://github.com/dbreunig/whenwords)** by [@dbreunig](https://github.com/dbreunig) - The original spec-only library for relative time formatting, which introduced the ghost library pattern and demonstrated how well-specified utilities can be reliably implemented by AI assistants.

- **[padlib](https://github.com/hasansezertasan/padlib)** by [@hasansezertasan](https://github.com/hasansezertasan) - A spec-only string padding library that further validated the ghost library approach for simple, focused utilities.

- **[Woothee](https://github.com/woothee/woothee)** - A multi-language user-agent parser (Ruby, Perl, Python, PHP, Node.js, Go, Rust, Lua) that demonstrated the value of consistent APIs across languages for user-agent parsing.

The ghost library approach recognizes that modern AI coding assistants can reliably implement tightly-specified utilities, eliminating the need to distribute and maintain code across multiple languages. Instead, we provide:

1. **A complete specification** that defines exact behavior
2. **Comprehensive test cases** that validate correctness
3. **Clear examples** that demonstrate usage

This works particularly well for focused utilities like user-agent parsing, where the specification is stable and the implementation is straightforward.

### Further Reading

- [A Software Library with No Code](https://www.dbreunig.com/2026/01/08/a-software-library-with-no-code.html) - Drew Breunig's original blog post about whenwords and the ghost library concept

## Philosophy

From Drew Breunig's whenwords README:

> "Do we still need 3rd party code libraries?" when AI can reliably implement tightly specified utilities on demand.

Ghost libraries work best for:

- **Simple, focused utilities** with clear specifications
- **Stable APIs** that don't require frequent updates
- **Language-agnostic behavior** that should work identically everywhere
- **Well-understood domains** with predictable edge cases

They complement, rather than replace, traditional libraries:

- **Use ghost libraries** for simple utilities you want consistent across languages
- **Use traditional libraries** for complex systems, performance-critical code, security-sensitive operations, or code requiring active maintenance and community support

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Frequently Asked Questions

### Why no code?

Modern AI assistants can reliably implement well-specified utilities. By providing just the specification and tests, we:

- Eliminate cross-language maintenance burden
- Let developers use their preferred language
- Allow customization to specific needs
- Reduce security concerns (you review the generated code)

### Is this production-ready?

The **specification** is production-ready. Your **implementation** is as production-ready as you make it. We recommend:

- Reviewing all AI-generated code
- Running the complete test suite
- Adding your own edge cases
- Using battle-tested underlying libraries

### Can I modify the specification for my needs?

Absolutely! Fork the repository and adapt it. You might want to:

- Add more device types
- Include additional browser properties
- Change return types for your language
- Add custom detection logic

### What if I find a bug in the spec?

Please open an issue or submit a pull request! The specification should be:

- Clear and unambiguous
- Cover all common cases
- Handle edge cases gracefully
- Include test cases for all behaviors

### How is this different from ua-parser?

[ua-parser](https://github.com/ua-parser) is a mature, battle-tested library with implementations across many languages. It's:

- ✅ More accurate (millions of test cases)
- ✅ Actively maintained with updates for new devices/browsers
- ✅ Used in production by major companies

ualib is:

- ✅ Simpler specification (easier to understand and customize)
- ✅ AI-generated (implement in any language instantly)
- ✅ Focused API (5 functions vs. extensive API)

**For production use**: Consider ua-parser or similar established libraries
**For learning/prototyping**: ualib offers a simpler, more accessible approach

---

**Ready to implement?** See [INSTALL.md](INSTALL.md) for detailed instructions.

**Questions or feedback?** Open an issue or start a discussion.

**Built with ualib?** Share your implementation or use case!
