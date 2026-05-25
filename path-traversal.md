# Path Traversal / Directory Traversal

**Category:** Web  
**Difficulty:** Easy  
**Date:** 2026-05-26

## Overview

A web app reads files based on user input without proper path validation. By using `../` sequences, you can escape the intended directory and read arbitrary files from the server filesystem.

## Walkthrough

### Step 1: Identify the Vulnerable Endpoint
The application has a file reading feature, typically something like:
```
GET /read?file=report.pdf
```

### Step 2: Probe with Directory Traversal
Send a request with `../` to navigate up directories:
```
GET /read?file=../../etc/passwd
```

### Step 3: Understand the Path
Each `..` goes up one directory level:
- Starting from web root `/var/www/html/`
- `../../etc/passwd` → `/var/www/` → `/var/` → `/etc/passwd`
- The server shows the contents of `/etc/passwd`!

### Step 4: Common Targets
| File | Purpose |
|------|---------|
| `/etc/passwd` | User accounts |
| `../../config.php` or `../../.env` | Database credentials, secrets |
| `../../proc/self/environ` | Environment variables, tokens |
| `../../proc/self/fd/1` | Open file descriptors |

### Step 5: Defeat URL Encoding
Sometimes `../` gets blocked but URL-encoded versions work:
- `%2e%2e%2f` → `../`
- `%2e%2e/` → `../`
- `..%2f` → `../`

## Key Insight
> Never trust user input. Any file path derived from user input must be validated, canonicalized (e.g., with `realpath()`), and sandboxed.

## Prevention
- **Whitelist approach:** Only allow specific safe filenames
- **Canonicalize paths:** Use `realpath()` to resolve the final path and verify it's within the allowed directory
- **chroot/Jail:** Run the application in an isolated environment
- **Disable directory listing:** Don't expose directory traversal vectors
