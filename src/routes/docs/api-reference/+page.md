# API Reference

## JavaScript / TypeScript

### Installation

```bash
npm install @dlovans/tenet-core
```

### Setup

The TypeScript package is pure TypeScript with no WASM dependencies. It works in both browser and Node.js environments without any initialization.

```typescript
import { run, verify, isReady } from '@dlovans/tenet-core';

// No initialization needed - ready to use immediately
console.log(isReady()); // Always true
```

### Run

Execute the schema logic for a given effective date.

```typescript
import { run, TenetResult } from '@dlovans/tenet-core';

const schema = {
  definitions: {
    amount: { type: 'number', value: 500 }
  }
};

const result: TenetResult = run(schema, new Date());

if (result.error) {
  console.error(result.error);
} else {
  console.log(result.result.status);     // "READY" | "INCOMPLETE" | "INVALID"
  console.log(result.result.errors);     // ValidationError[]
  console.log(result.result.definitions); // Updated definitions
}
```

**Parameters:**
- `schema` - TenetSchema object or JSON string
- `date` - Effective date (Date object or ISO 8601 string). Defaults to current date.

**Returns:** `TenetResult` with either `result` (the transformed schema) or `error` (parse/system error).

### Verify

Verify that a completed document was correctly derived from a base schema.

```typescript
import { verify, TenetVerifyResult } from '@dlovans/tenet-core';

const result: TenetVerifyResult = verify(newSchema, oldSchema);

console.log(result.valid); // true or false
console.log(result.error); // Error message if invalid
```

**Parameters:**
- `newSchema` - The submitted/completed schema
- `oldSchema` - The original base schema

**Returns:** `TenetVerifyResult` with `valid` boolean and optional `error` message.

### Utility Functions

```typescript
import { isReady } from '@dlovans/tenet-core';

// Check if the VM is ready (always true for pure TypeScript)
if (isReady()) {
  const result = run(schema);
}
```

### Reactive UI Pattern

```typescript
function onFieldChange(fieldId: string, newValue: any) {
  // Update the schema
  schema.definitions[fieldId].value = newValue;

  // Re-run the VM
  const result = run(schema, new Date());

  if (!result.error) {
    updateUI(result.result);
  }
}

function updateUI(schema: TenetSchema) {
  for (const [id, def] of Object.entries(schema.definitions)) {
    const element = document.getElementById(id);

    // Show/hide based on visibility
    element.hidden = !def.visible;

    // Disable computed fields
    if (def.readonly) {
      element.setAttribute('disabled', 'true');
    }

    // Update constraints
    if (def.min !== undefined) element.min = def.min;
    if (def.max !== undefined) element.max = def.max;
  }

  // Display errors
  for (const error of schema.errors || []) {
    showError(error.field_id, error.message);
  }
}
```

---

## Go

### Installation

```bash
go get github.com/dlovans/tenet/pkg/tenet
```

### Run

```go
import (
    "time"
    tenet "github.com/dlovans/tenet/pkg/tenet"
)

jsonSchema := `{
  "definitions": {
    "amount": {"type": "number", "value": 500}
  }
}`

result, err := tenet.Run(jsonSchema, time.Now())
if err != nil {
    log.Fatal(err)
}

fmt.Println(result) // JSON string with computed state
```

### Verify

```go
valid, err := tenet.Verify(newJson, oldJson)
if err != nil {
    log.Fatal(err)
}
fmt.Println(valid) // true or false
```

---

## TypeScript Types

```typescript
interface TenetSchema {
  protocol?: string;
  schema_id?: string;
  version?: string;
  definitions: Record<string, Definition>;
  logic_tree?: Rule[];
  state_model?: StateModel;
  attestations?: Record<string, Attestation>;
  temporal_map?: TemporalBranch[];
  errors?: ValidationError[];
  status?: 'READY' | 'INCOMPLETE' | 'INVALID';
}

interface Definition {
  type: string;
  value?: any;
  label?: string;
  required?: boolean;
  readonly?: boolean;
  visible?: boolean;
  options?: string[];
  min?: number;
  max?: number;
  min_length?: number;
  max_length?: number;
  pattern?: string;
  ui_class?: string;
  ui_message?: string;
}

interface Attestation {
  statement: string;
  law_ref?: string;
  required_role?: string;
  provider?: string;
  required?: boolean;
  signed?: boolean;
  evidence?: {
    provider_audit_id: string;
    timestamp: string;
    signer_id?: string;
  };
  on_sign?: {
    set?: Record<string, any>;
  };
}

interface ValidationError {
  field_id?: string;
  rule_id?: string;
  message: string;
  law_ref?: string;
}

interface TenetResult {
  result?: TenetSchema;
  error?: string;
}

interface TenetVerifyResult {
  valid: boolean;
  error?: string;
}

interface TemporalBranch {
  valid_range: [string | null, string | null];
  logic_version: string;
  status: 'ACTIVE' | 'ARCHIVED';
}
```

---

## Migration from v0.1.x

If you were using the WASM-based version:

```typescript
// OLD (v0.1.x with WASM)
import { init, run } from '@dlovans/tenet-core';
await init('/path/to/tenet.wasm');
const result = run(schema);

// NEW (v0.2.x pure TypeScript)
import { run } from '@dlovans/tenet-core';
const result = run(schema);  // No init required!
```

The `init()` function still exists for backwards compatibility but is a no-op.
