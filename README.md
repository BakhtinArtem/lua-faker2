# Faker2

Faker2 is an enhanced fork of [lua-faker](https://github.com/hector-vido/lua-faker) - a [lua](https://www.lua.org/) library that generates fake data for you.

Whether you need to bootstrap your database, create good-looking XML documents, fill-in your persistence to stress test it, or anonymize data taken from a production service, Faker2 is for you. Faker2 is heavily inspired by PHP Faker, Perl Faker, Ruby Faker and by Python Faker.

This fork extends the original library with many new features including:
- Enhanced string and integer generation with min/max length and pattern matching
- Seed support for reproducible random sequences
- New data type generators (UUID, URI, URL, dates, IP addresses, etc.)
- ID generation with multiple formats
- Comprehensive test suite

Currently supports `en_US` and `pt_BR` locales.

## Installation

```bash
# Using luarocks (if configured)
luarocks install faker-0.0.1-1.rockspec

# Or manually add to your Lua path
export LUA_PATH="./?.lua;./?/init.lua;;"
```

## Basic Usage

The default **locale** is `en_US`. Every function that uses a lazy initialization need to be called with `:`:

```lua
local Faker = require('faker')

local faker = Faker:new()
for i = 1, 10 do
        print(faker.randstring())
        print(faker.randint(10))
        print(faker:name())
        print(faker:email())
        print(faker:country())
        print(faker:state())
        print(faker:city())
        print(faker.ssn())
end

-- bbfchjdhaa
-- 1314612416
-- Paul Simmon
-- jack.mccullough@example.com
-- Pitcairn
-- Vermont
-- Locustdale
-- 983-77-4987
```

> Pay attention because `faker.ssn` only exists when `en_US` is loaded.

### Change Locale

To change `locale`, simply pass a `table` on constructor `new` with this option:

```lua
local Faker = require('faker')

local faker = Faker:new({locale = 'pt_BR'})
for i = 1, 10 do
	print(faker:name())
	print(faker:email())
	print(faker.cpf())
	print(faker.cep())
end

-- Maria da Silva
-- joao.soares@example.com
-- 744.524.429-86
-- 95194-526
```

> Pay attention because `faker.cpf` and `faker.cep` only exist when `pt_BR` is loaded.

## New Features

### Seed Support

Use a seed for reproducible random sequences:

```lua
local faker1 = Faker:new({seed = 12345})
local faker2 = Faker:new({seed = 12345})

-- Both will generate the same sequence
print(faker1:integer({min = 1, max = 100}))  -- Same value
print(faker2:integer({min = 1, max = 100}))  -- Same value
```

### Enhanced String Generation

Generate strings with min/max length and optional pattern matching:

```lua
local faker = Faker:new()

-- Basic string with default length (10)
local str1 = faker:string()

-- String with exact length
local str2 = faker:string({minLength = 20, maxLength = 20})

-- String with length range
local str3 = faker:string({minLength = 5, maxLength = 15})

-- String matching a pattern (Lua pattern)
local str4 = faker:string({minLength = 5, maxLength = 10, pattern = '^[a-z]+$'})

-- Backward compatible: old randstring() still works
local str5 = faker.randstring(15)
```

### Enhanced Integer Generation

Generate integers with min/max and optional pattern matching:

```lua
local faker = Faker:new()

-- Basic integer
local int1 = faker:integer()

-- Integer with range
local int2 = faker:integer({min = 10, max = 100})

-- Integer with min only (max = min + 100)
local int3 = faker:integer({min = 50})

-- Integer with max only (min = 1)
local int4 = faker:integer({max = 50})

-- Integer matching a pattern (on string representation)
local int5 = faker:integer({min = 10, max = 100, pattern = '^%d+0$'})  -- Ends with 0

-- Backward compatible: old randint() still works
local int6 = faker.randint(5)  -- 5-digit number
```

### Email Generation

Generate email addresses with optional pattern matching:

```lua
local faker = Faker:new()

-- Basic email
local email1 = faker:email()

-- Email matching pattern
local email2 = faker:email({pattern = '^[^@]+@example%.com$'})
```

### UUID Generation

Generate UUID v4:

```lua
local faker = Faker:new()
local uuid = faker:uuid()
-- Example: "a1b2c3d4-e5f6-4789-a012-b3c4d5e6f789"
```

### URI and URL Generation

Generate URIs and URLs:

```lua
local faker = Faker:new()

-- URI (various schemes: http, https, ftp, file, data)
local uri = faker:uri()
-- Example: "https://example.com/path"

-- URL (always http or https)
local url = faker:url()
-- Example: "http://example.com/path"

-- With pattern matching
local url2 = faker:url({pattern = '^https://.*$'})
```

### Date and Time Generation

Generate dates in various formats:

```lua
local faker = Faker:new()

-- Date in YYYY-MM-DD format
local date = faker:date()
-- Example: "2023-05-15"

-- Date-time in RFC 3339 format
local datetime = faker:dateTime()
-- Example: "2023-05-15T14:30:45+02:00"

-- Unix timestamp
local timestamp = faker:timestamp()
-- Example: 1684162245

-- Timestamp with range
local ts = faker:timestamp({min = 1000000000, max = 2000000000})
```

### Network Address Generation

Generate IP addresses and hostnames:

```lua
local faker = Faker:new()

-- IPv4 address
local ipv4 = faker:ipv4()
-- Example: "192.168.1.1"

-- IPv6 address
local ipv6 = faker:ipv6()
-- Example: "2001:0db8:85a3:0000:0000:8a2e:0370:7334"

-- Hostname
local hostname = faker:hostname()
-- Example: "example.com"
```

### Binary and Byte Generation

Generate binary data and base64-encoded bytes:

```lua
local faker = Faker:new()

-- Base64-encoded byte string
local byte_str = faker:byte()
-- Example: "SGVsbG8gV29ybGQ="

-- With custom length
local byte_str2 = faker:byte({minLength = 20, maxLength = 30})

-- Binary data (for file upload simulation)
local binary = faker:binary()
-- Default: 100-1000 bytes

-- Binary with custom length
local binary2 = faker:binary({minLength = 500, maxLength = 1000})
```

### Password Generation

Generate secure passwords with required character types:

```lua
local faker = Faker:new()

-- Default password (8-16 characters, includes lowercase, uppercase, number, symbol)
local password = faker:password()

-- Custom length
local password2 = faker:password({minLength = 12, maxLength = 20})
```

Passwords always include at least one of each:
- Lowercase letter (a-z)
- Uppercase letter (A-Z)
- Number (0-9)
- Symbol (!@#$%^&*()_+-=[]{}|;:,.<>?)

### Boolean Generation

Generate random boolean values:

```lua
local faker = Faker:new()
local bool = faker:boolean()
-- Returns true or false
```

### ID Generation

Generate IDs in various formats:

```lua
local faker = Faker:new()

-- Numeric ID (default)
local id1 = faker:id({type = 'numeric'})
-- Example: 123456789

-- String ID
local id2 = faker:id({type = 'string'})
-- Example: "abcdefghijklmn"

-- UUID ID
local id3 = faker:id({type = 'uuid'})
-- Example: "a1b2c3d4-e5f6-4789-a012-b3c4d5e6f789"

-- Alphanumeric ID (default when no type specified)
local id4 = faker:id()
-- Example: "aB3dE5fG7hI"

-- Custom length alphanumeric
local id5 = faker:id({length = 20})
```

## Complete Example

```lua
local Faker = require('faker')

-- Create faker instance with seed for reproducibility
local faker = Faker:new({seed = 42, locale = 'en_US'})

-- Generate various data types
print("Name:", faker:name())
print("Email:", faker:email())
print("UUID:", faker:uuid())
print("URL:", faker:url())
print("Date:", faker:date())
print("IPv4:", faker:ipv4())
print("Password:", faker:password())
print("ID:", faker:id())

-- Generate with constraints
print("String (5-10 chars):", faker:string({minLength = 5, maxLength = 10}))
print("Integer (100-200):", faker:integer({min = 100, max = 200}))
print("Email matching pattern:", faker:email({pattern = '^[a-z]+@example%.com$'}))
```

## Testing

Run the comprehensive test suite:

```bash
LUA_PATH="./?.lua;./?/init.lua;;" lua test.lua
```

All tests should pass, verifying:
- Seed support
- String generation with min/max/pattern
- Integer generation with min/max/pattern
- All new data type generators
- Backward compatibility

## Changes from Original

This fork extends the original [lua-faker](https://github.com/hector-vido/lua-faker) with:

1. **Seed Support**: Reproducible random sequences via `seed` parameter
2. **Enhanced String Generation**: `minLength`, `maxLength`, and `pattern` support
3. **Enhanced Integer Generation**: `min`, `max`, and `pattern` support
4. **New Data Types**: UUID, URI, URL, date, date-time, timestamp, IPv4, IPv6, hostname, byte, binary, password, boolean
5. **ID Generation**: Multiple ID formats (numeric, string, UUID, alphanumeric)
6. **Pattern Matching**: Support for Lua patterns in string and integer generation
7. **Comprehensive Tests**: Full test suite covering all features

All original functionality is preserved for backward compatibility.

## License

MIT (same as original)

## Credits

- Original library: [hector-vido/lua-faker](https://github.com/hector-vido/lua-faker)
- Inspired by: PHP Faker, Perl Faker, Ruby Faker, Python Faker
