# Installing ualib

ualib is a **ghost library** — it contains no code, only a specification and tests. You generate an implementation by giving these files to an AI coding assistant.

## Quick Start

1. **Download the specification files:**

   ```bash
   curl -O https://raw.githubusercontent.com/hasansezertasan/ualib/main/SPEC.md
   curl -O https://raw.githubusercontent.com/hasansezertasan/ualib/main/tests.yaml
   ```

2. **Ask your AI to implement it:**

   Copy and paste this prompt into Claude, Cursor, GitHub Copilot, or any AI coding assistant:

   ```
   I need you to implement the ualib user-agent parsing library based on the provided specification.

   Requirements:
   1. Read SPEC.md to understand the complete behavioral specification
   2. Implement all 5 functions: parse(), is_mobile(), is_tablet(), is_pc(), is_bot()
   3. Ensure boolean functions are strictly consistent with parse().device.type results
   4. Follow the language conventions for [YOUR LANGUAGE]
   5. All functions should handle edge cases gracefully (empty strings, malformed input, etc.)
   6. Make sure all test cases in tests.yaml pass

   Target language: [Python/JavaScript/Ruby/Go/Rust/etc.]

   Create a complete, production-ready implementation with:
   - Type hints/annotations (if the language supports them)
   - Comprehensive error handling
   - Documentation strings/comments
   - A test suite that validates all test cases from tests.yaml

   Let's start with the core data structures and the parse() function.
   ```

3. **Verify with tests:**

   Ask your AI to run all test cases from `tests.yaml` to ensure the implementation is correct.

## For Different Languages

### Python

```
Target language: Python

Additional requirements:
- Use type hints (from typing import Dict, Optional, etc.)
- Follow PEP 8 style guidelines
- Create a class-based or functional API (your choice)
- Support Python 3.8+
```

### JavaScript/TypeScript

```
Target language: TypeScript

Additional requirements:
- Use TypeScript with strict type checking
- Export both ESM and CommonJS formats
- Follow JavaScript naming conventions (isMobile instead of is_mobile)
- Support Node.js 18+ and modern browsers
```

### Go

```
Target language: Go

Additional requirements:
- Use idiomatic Go patterns
- Create a proper package with go.mod
- Follow Go naming conventions (IsMobile instead of is_mobile)
- Include benchmarks
```

### Rust

```
Target language: Rust

Additional requirements:
- Use idiomatic Rust with proper error handling
- Use idiomatic Rust string types (`&str` for input, `String` for owned fields)
- Follow Rust naming conventions
- Include documentation tests
```

### Ruby

```
Target language: Ruby

Additional requirements:
- Follow Ruby style guide
- Create a proper gem structure
- Use Ruby naming conventions (mobile? instead of is_mobile)
- Support Ruby 3.0+
```

### PHP

```
Target language: PHP

Additional requirements:
- Use PHP 8.0+ features (named arguments, union types, etc.)
- Follow PSR-12 coding standards
- Create proper namespace structure
- Include PHPDoc comments
```

### Java

```
Target language: Java

Additional requirements:
- Use Java 17+ features (records, sealed classes, etc.)
- Follow standard Java naming conventions (isMobile instead of is_mobile)
- Create proper package structure
- Include Javadoc comments
```

### C\#

```
Target language: C#

Additional requirements:
- Use C# 11+ / .NET 7+ features (records, nullable reference types, etc.)
- Follow .NET naming conventions (IsMobile instead of is_mobile)
- Create proper namespace structure
- Include XML documentation comments
```

## Manual Implementation

If you prefer to implement it yourself without AI:

1. **Read the specification:** Start with `SPEC.md` to understand all function behaviors
2. **Set up your project:** Create a new project in your language with a testing framework
3. **Implement the data structures:** Create the UserAgent object/struct with browser, os, device, engine, cpu fields
4. **Implement parse():** This is the core function - build regex patterns or use a parsing library
5. **Implement the boolean checks:** These can often delegate to parse() and check the device.type
6. **Run the tests:** Import `tests.yaml` and verify all test cases pass
7. **Handle edge cases:** Make sure empty strings, malformed input, and unusual UAs are handled gracefully

## Validation

Your implementation is correct if:

✅ All functions in SPEC.md are implemented
✅ All test cases in tests.yaml pass
✅ The boolean functions are consistent with parse() results
✅ Edge cases (empty strings, malformed input) are handled gracefully
✅ No exceptions are thrown on invalid input

## Dependencies

You may use existing user-agent parsing libraries as the foundation:

- **Python**: Consider using `user-agents` or `ua-parser` as the underlying parser
- **JavaScript**: Consider using `ua-parser-js` or `bowser` as the underlying parser
- **Ruby**: Consider using `user_agent_parser` gem
- **Go**: Consider using `github.com/mssola/user_agent` or `github.com/ua-parser/uap-go`
- **Rust**: Consider using `woothee` or `uaparser` crates
- **PHP**: Consider using `donatj/phpuseragentparser` or `whichbrowser/parser`

The goal is to provide a **consistent, well-specified API** across languages, even if the underlying implementation varies.

## Examples

After implementation, you should be able to use it like this:

### Python

```python
from ualib import parse, is_mobile, is_tablet, is_pc, is_bot

# Parse a full user-agent string
ua = parse("Mozilla/5.0 (iPhone; CPU iPhone OS 16_0 like Mac OS X)")
print(ua.browser.name)  # "Mobile Safari"
print(ua.os.name)       # "iOS"
print(ua.device.type)   # "mobile"

# Quick boolean checks
if is_mobile(ua_string):
    print("Mobile device detected")
```

### JavaScript

```javascript
import { parse, isMobile, isTablet, isPc, isBot } from "ualib";

// Parse a full user-agent string
const ua = parse("Mozilla/5.0 (iPhone; CPU iPhone OS 16_0 like Mac OS X)");
console.log(ua.browser.name); // "Mobile Safari"
console.log(ua.os.name); // "iOS"
console.log(ua.device.type); // "mobile"

// Quick boolean checks
if (isMobile(uaString)) {
  console.log("Mobile device detected");
}
```

### Go

```go
package main

import (
    "fmt"

    "github.com/hasansezertasan/ualib"
)

func main() {
    // Parse a full user-agent string
    ua := ualib.Parse("Mozilla/5.0 (iPhone; CPU iPhone OS 16_0 like Mac OS X)")
    fmt.Println(ua.Browser.Name)  // "Mobile Safari"
    fmt.Println(ua.OS.Name)       // "iOS"
    fmt.Println(ua.Device.Type)   // "mobile"

    // Quick boolean checks
    if ualib.IsMobile(uaString) {
        fmt.Println("Mobile device detected")
    }
}
```

## Troubleshooting

### Issue: Test cases failing for specific browsers

**Possible causes:**
- Your underlying parsing library is outdated
- Browser version detection differs from expected format
- Missing browser signatures in your parser's database

**Solutions:**
- Update your parsing library to the latest version
- Check if the library maintainers have added support for newer browsers
- Contribute browser signatures to the underlying library
- Adjust version parsing to match the canonical format in tests.yaml

### Issue: Inconsistent results between is_X() and parse().device.type

**This is a critical error!** The specification REQUIRES strict consistency:

```
is_mobile(ua) ⟺ parse(ua).device.type == "mobile"
is_tablet(ua) ⟺ parse(ua).device.type == "tablet"
is_pc(ua) ⟺ parse(ua).device.type == "desktop"
is_bot(ua) ⟺ parse(ua).device.type == "bot"
```

**Solutions:**
- Make sure your boolean functions delegate to `parse()` internally
- OR ensure they use identical logic to determine device type
- Add validation tests to check this invariant for all inputs
- Review SPEC.md section "Boolean Function Implementation (REQUIRED)"

### Issue: Performance degradation on repeated parsing

**Solutions:**
- Implement caching: store parse() results keyed by UA string
- Use a LRU (Least Recently Used) cache with reasonable size (e.g., 1000 entries)
- Consider parsing UA string once per request and storing in request context
- Pre-compile regex patterns (don't recompile on every parse)

### Issue: Null vs empty string confusion

Different languages handle missing values differently. Follow the specification:

- **Use `null`** for missing information (not empty strings `""`)
- **Never use `undefined`** in JavaScript (use `null` instead)
- See SPEC.md "Null Handling by Language" for language-specific guidance

### Issue: Very long or malformed user-agent strings causing crashes

**Solutions:**
- Implement input validation: truncate strings longer than 2048 characters
- Strip control characters (0x00-0x1F) before parsing
- Add regex timeouts to prevent ReDoS attacks
- Return default/null values for unparseable strings (never throw exceptions)

### Issue: Bot detection is too aggressive or too permissive

**Check these points:**
- Review the list of bot signatures in your parser
- Ensure "bot" keywords are case-insensitive
- Remember: bots take precedence (Googlebot Smartphone → device.type = "bot")
- Add custom bot signatures if needed for your use case

### Issue: CPU architecture always returns null

**This is expected!** Modern browsers (2020+) don't expose CPU architecture for privacy reasons. Only parse architecture if explicitly present in the UA string. Don't infer it from OS.

## Contributing

Found an edge case that's not handled correctly? Please:

1. Add a test case to `tests.yaml` demonstrating the issue
2. Update `SPEC.md` if the specification needs clarification
3. Submit a pull request with your improvements

Remember: We don't accept code contributions (it's a ghost library!), but we welcome:

- New test cases
- Specification clarifications
- Documentation improvements
- Real-world user-agent examples

## License

See `LICENSE` file for details.
