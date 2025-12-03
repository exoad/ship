# "Ship It!" DSL

## Overview

**Ship** is a DSL for forming simple scripts. It uses brace-based syntax `{}` for blocks, supports conditional execution with `if/elif/else`, and distinguishes between built-in tags and custom tasks using the `$` prefix.

To use, simply make sure to install Python and just copy the root `ship.py` over to your project root and start using it!

**Extension:** `.ship`

## Syntax

### Basic Structure

```ship
ship {
    title: "My Build"

    var {
        KEY = "value"
        ANOTHER = true
    }

    run { command: "flutter build" }

    if CONDITION {
        // tasks when true
    } elif OTHER {
        // alternative
    } else {
        // fallback
    }
}
```

### Tag Types

| Prefix | Type     | Example                     | Description                   |
| ------ | -------- | --------------------------- | ----------------------------- |
| (none) | Built-in | `run {}`, `delete {}`       | Special compiler-handled tags |
| `$`    | Custom   | `$myTask {}`, `$cleanup {}` | User-defined task markers     |

### Built-in Tags

-   `ship {}` - Root container (optional)
-   `title:` - Build title
-   `var {}` - Variable declarations
-   `if`, `elif`, `else` - Conditional execution
-   `run {}` - Execute shell command
-   `delete {}` - Delete file/directory
-   `mkdir {}` - Create directory
-   `copy {}` - Copy file
-   `move {}` - Move file
-   `move_all {}` - Move directory contents
-   `zip {}` - Create ZIP archive

### Variable Usage

```ship
var {
    DIST = "./dist"
    VERSION = "1.0.0"
}

# Use the variable directly (no ${} needed). For complex strings, define a var with the full path.
mkdir { path: DIST }
```

### Conditional Execution

```ship
if BUILD_RELEASE {
    run { command: "flutter build --release" }
} elif BUILD_DEBUG {
    run { command: "flutter build --debug" }
} else {
    $skip_build {}
}
```

### Comments

```ship
// Single-line comment
# Hash-style comment

/*
   Multi-line
   comment
*/
```

## Available Actions

### `run` - Execute shell commands

```ship
run {
    command: "flutter build windows --release"
    verbose: true
}
```

### `delete` - Delete files or directories

```ship
delete {
    path: "./dist/old_build"
    forgive_missing: true
}
```

### `mkdir` - Create directory

```ship
mkdir { path: "./dist/windows" }
```

### `copy` - Copy file

```ship
copy {
    src: "./LICENSE.txt"
    dst: "./dist/LICENSE.txt"
}
```

### `move` - Move file or directory

```ship
move {
    src: "./old/location"
    dst: "./new/location"
}
```

### `move_all` - Move all contents

```ship
move_all {
    src: "./build/artifacts"
    dst: "./dist"
}
```

### `zip` - Create ZIP archive

```ship
zip {
    src: "./dist/windows"
    zip_path: "./dist/release.zip"
}
```

## Usage

### From Python

You can still invoke scripts programmatically:

```python
import ship_it as builder

# Execute a script without CLI variable overrides
builder.run_ship("build_windows.ship", dry_run=True)
```

### From Command Line

```bash
# Run build
python ship_it.py build_windows.ship

# Dry run (show what would execute)
python ship_it.py build_windows.ship --dry-run
```

## Example: Windows Build Script

See `build_windows.ship` for a complete working example that:

1. Installs dependencies
2. Runs tests
3. Builds the Flutter Windows release
4. Cleans up unnecessary files
5. Conditionally creates a distribution ZIP

## Design Philosophy

Ship is designed with these principles:

-   **Kotlin-like braces** - Familiar syntax for developers
-   **Explicit blocks** - Clear visual structure with `{}`
-   **Custom tasks** - `$` prefix distinguishes user tags
-   **Conditional logic** - Full `if/elif/else` support
-   **Variable injection** - CLI overrides script defaults
-   **Simple and readable** - Easy to understand at a glance

## Legacy Support

Note: Legacy `.script` (YAML-like) support has been removed. Please migrate to `.ship` scripts.
