# zasm — colored tokenizer output for najd

A small demo built on top of the `najd` string-reader library (macro-based
`NSR_*` variant) that tokenizes a source string and prints it back out with
ANSI color highlighting, similar to how an assembler listing or syntax
highlighter would render mnemonics vs. immediate values.

## Files

| File | Role |
|---|---|
| `najd.h` / `najd.c` | The library itself — unchanged, provided as-is |
| `main.c` | Defines the `zasm` reader table and implements the 5 lifecycle hooks |

## What's in `main.c`

### Reader table (`zasm`)
Three token readers built with the library's `NSR_*` macros:

| id | name | matches |
|---|---|---|
| 1 | `number` | digits (`isdigit`) |
| 2 | `identifier` | letters/underscore start, then alnum/underscore |
| 3 | `space` | whitespace (`isspace`) |

### The 5 hooks
The original code had these as bare comments inside the read loop.
They're now real functions:

- **`on_create()`** — prints a listing header before tokenization starts.
- **`pre_read(str)`** — runs before each token is read. Currently a no-op,
  left as a place to add tracing/counters later.
- **`on_result(result)`** — the core of the coloring. Switches on
  `result->id` and prints the token's value wrapped in the matching
  ANSI color code:
  - `number` → red
  - `identifier` → bold cyan (treated as a mnemonic)
  - `space` → printed as-is, to preserve original spacing
- **`post_read(result)`** — runs after each token, after `on_result`.