# API Reference

## Go

### Installation

```bash
go get github.com/dlovans/tenet/pkg/tenet
```

### Run

Execute schema logic for a given effective date.

```go
import (
    "time"
    "github.com/dlovans/tenet/pkg/tenet"
)

result, err := tenet.Run(jsonString, time.Now())
if err != nil {
    // Parse or execution error
}

// result is JSON string with computed state, errors, status
```

### Verify

Check that a transformation is legal by replaying the logic.

```go
valid, err := tenet.Verify(newJSON, oldJSON)
if err != nil {
    // Verification failed
}
// valid == true means transformation is legal
```

---

## CLI

### Installation

```bash
go build -o tenet ./cmd/tenet
```

### Run

```bash
# From file
tenet run -file schema.json -date 2026-01-20

# From stdin
cat schema.json | tenet run -date 2026-01-20
```

### Verify

```bash
tenet verify -new completed.json -base schema.json
```

### Lint

Static analysis of schema files (no WASM required).

```bash
tenet lint -file schema.json
```

---

## JavaScript / TypeScript

### Installation

```bash
npm install @dlovans/tenet
```

### Browser Setup

```html
<script src="node_modules/@dlovans/tenet/wasm/wasm_exec.js"></script>
<script type="module">
import { init, run, verify } from '@dlovans/tenet';

await init('/path/to/tenet.wasm');
</script>
```

### Node.js Setup

```javascript
import { init, run, verify, lint } from '@dlovans/tenet';

// Initialize WASM (required for run/verify)
await init('./node_modules/@dlovans/tenet/wasm/tenet.wasm');
```

### Run

```typescript
import { run, TenetResult } from '@dlovans/tenet';

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

### Verify

```typescript
import { verify, VerifyResult } from '@dlovans/tenet';

const result: VerifyResult = verify(newSchema, oldSchema);

console.log(result.valid); // true or false
console.log(result.error); // Error message if failed
```

### Lint

**Note:** Lint is pure TypeScript — no WASM init required!

```typescript
import { lint } from '@dlovans/tenet';

const issues = lint(schema);

for (const issue of issues) {
  console.log(`${issue.severity}: ${issue.message}`);
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
  temporal_map?: TemporalEntry[];
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
```
