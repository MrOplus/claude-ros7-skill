# RouterOS Scripting Language Reference

---

## Global Script Commands (prefix `:`)

| Command | Description |
|---------|-------------|
| `:put` | Print value to console |
| `:log` | Write to system log (info, warning, error) |
| `:local` | Declare local variable |
| `:global` | Declare global variable |
| `:set` | Assign value to variable |
| `:if` | Conditional: `:if (cond) do={...} else={...}` |
| `:for` | For loop: `:for i from=1 to=10 step=1 do={...}` |
| `:foreach` | Iterate: `:foreach k,v in=$array do={...}` |
| `:while` | While loop: `:while (cond) do={...}` |
| `:do` | Do-while or error handling: `:do {...} while=(cond)` |
| `:onerror` | Error handler: `:onerror e in={...} do={...}` |
| `:parse` | Parse string as script, return function |
| `:execute` | Execute script in background |
| `:return` | Return value from function |
| `:error` | Throw an error |
| `:nothing` | Do nothing (empty value) |
| `:beep` | Produce beep sound |
| `:delay` | Pause execution (e.g., `:delay 5s`) |
| `:len` | String/array length |
| `:pick` | Extract substring or array element |
| `:find` | Find substring position |
| `:toarray` | Convert to array |
| `:toip` | Convert to IP address |
| `:toip6` | Convert to IPv6 address |
| `:tonum` | Convert to number |
| `:tostr` | Convert to string |
| `:totime` | Convert to time value |
| `:tobool` | Convert to boolean |
| `:toid` | Convert to internal ID |
| `:tonsec` | Convert to nanoseconds |
| `:tocrlf` | Convert line endings to CR+LF |
| `:tolf` | Convert line endings to LF |
| `:typeof` | Get variable type |
| `:resolve` | DNS resolve hostname |
| `:time` | Measure execution time |
| `:timestamp` | Get current timestamp |
| `:rndnum` | Generate random number |
| `:rndstr` | Generate random string |
| `:convert` | Convert between encodings (base64, hex, etc.) |
| `:serialize` | Serialize value to string |
| `:deserialize` | Deserialize string to value |
| `:retry` | Retry command on failure |
| `:range` | Generate number range array |
| `:grep` | Filter array by pattern |
| `:environment` | List script environment |
| `:jobname` | Get current script job name |
| `:exit` | Exit script |
| `:break` | Break out of loop |
| `:continue` | Skip to next loop iteration |

---

## Data Types

| Type | Description | Example |
|------|-------------|---------|
| `num` | 64-bit integer | `42`, `-100` |
| `bool` | Boolean | `true`, `false`, `yes`, `no` |
| `str` | String | `"hello"` |
| `ip` | IPv4 address | `192.168.1.1` |
| `ip-prefix` | IPv4 + CIDR | `192.168.1.0/24` |
| `ip6` | IPv6 address | `::1` |
| `ip6-prefix` | IPv6 + prefix | `2001:db8::/32` |
| `id` | Internal item ID | `*1A` |
| `time` | Duration | `1d2h3m4s`, `00:05:00` |
| `array` | Key-value array | `{1; 2; "key"="val"}` |
| `nil` | No value | (undefined) |

---

## Operators

| Category | Operators |
|----------|----------|
| Arithmetic | `+`, `-`, `*`, `/`, `%` |
| Relational | `<`, `>`, `=`, `<=`, `>=`, `!=` |
| Logical | `!`, `&&` / `and`, `\|\|` / `or`, `in` |
| Bitwise | `~`, `\|`, `^`, `&`, `<<`, `>>` |
| String | `.` (concatenate) |
| Array | `,` (append) |
| Other | `[]` (command substitution), `()` (grouping), `$` (variable), `~` (regex match), `->` (array key access) |

---

## Common Patterns

### Variables
```routeros
:local myVar "hello"
:global myGlobal 42
:set myVar ($myVar . " world")
```

### Conditionals
```routeros
:if ($x > 10) do={
    :put "big"
} else={
    :put "small"
}
```

### Loops
```routeros
:for i from=1 to=10 do={ :put $i }

:foreach k,v in=$myArray do={
    :put "$k = $v"
}

:while ($running) do={
    :delay 1s
}
```

### Command Substitution
```routeros
:local count [/ip/address/print count-only]
:local myIp [/ip/address/get [find interface=ether1] address]
```

### Error Handling
```routeros
:onerror e in={
    /tool/fetch url="https://example.com"
} do={
    :log error "Fetch failed: $e"
}
```

### Functions
```routeros
:global myFunc do={
    :return ($1 + $2)
}
:put [$myFunc 3 4]  # outputs 7
```

### Arrays
```routeros
:local arr [:toarray ""]
:set ($arr->"key1") "value1"
:set ($arr->"key2") "value2"

:foreach k,v in=$arr do={
    :put "$k=$v"
}
```

### Working with Interfaces/Firewall
```routeros
# Find and disable
:foreach i in=[/ip/firewall/filter/find where chain=forward] do={
    :local comment [/ip/firewall/filter/get $i comment]
    :if ($comment ~ "temp") do={
        /ip/firewall/filter/disable $i
    }
}
```

### Fetch and Email
```routeros
/tool/fetch url="https://api.example.com/data" mode=https output=user as-value
/tool/email send to="admin@example.com" subject="Alert" body="Router rebooted"
```

### Scheduler + Script Pattern
```routeros
# Create script
/system/script add name=backup-daily source={
    /system/backup/save name=daily-backup
    /tool/email send to="admin@example.com" subject="Backup" body="Done"
}

# Schedule it
/system/scheduler add name=daily-backup interval=1d start-time=02:00:00 on-event=backup-daily
```
