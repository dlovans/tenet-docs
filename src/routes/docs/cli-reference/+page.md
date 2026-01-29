# CLI Reference

Tenet provides a command-line interface for processing schemas, verification, and linting.

## Installation

```bash
# Build from source
go build -o tenet ./cmd/tenet
```

## Commands

### run

Execute a schema and return the processed result.

```bash
tenet run [-file input.json] [-date YYYY-MM-DD]
```

**Arguments:**
| Flag | Description | Default |
|------|-------------|---------|
| `-file` | Input JSON file | stdin |
| `-date` | Effective date (ISO 8601) | Current date |

**Examples:**
```bash
# From file
tenet run -file schema.json

# With specific date
tenet run -file schema.json -date 2026-01-20

# From stdin
cat schema.json | tenet run

# Pipe with date
cat schema.json | tenet run -date 2026-01-20
```

---

### verify

Verify that a document transformation is legal.

```bash
tenet verify -new <completed.json> -base <original.json>
```

**Arguments:**
| Flag | Required | Description |
|------|----------|-------------|
| `-new` | Yes | Completed document to verify |
| `-base` | Yes | Original base schema |

**Example:**
```bash
tenet verify -new completed.json -base schema.json
```

**Output:**
- Success: `✓ Document verified: transformation is legal`
- Failure: `✗ Document verification failed` (exit code 1)

---

### lint

Perform static analysis on a schema without executing it.

```bash
tenet lint [-file schema.json]
```

**Arguments:**
| Flag | Description | Default |
|------|-------------|---------|
| `-file` | JSON schema file | stdin |

**Example:**
```bash
tenet lint -file schema.json
```

**Output:**
- No issues: `✓ No issues found`
- Issues found: Lists each issue with severity icon (`✗` for errors, `⚠` for warnings)

---

## Go API

For programmatic usage in Go applications:

```go
import "github.com/dlovans/tenet/pkg/tenet"

// Execute schema
result, err := tenet.Run(jsonString, time.Now())

// Verify transformation
valid, err := tenet.Verify(newJson, baseJson)
```

See [API Reference](/docs/api-reference) for JavaScript/TypeScript usage.
