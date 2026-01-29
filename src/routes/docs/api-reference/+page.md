# API Reference

## JavaScript / TypeScript

### Installation

```bash
npm install @dlovans/tenet-core
```

### Browser Setup

```html
<script src="node_modules/@dlovans/tenet-core/wasm/wasm_exec.js"></script>
<script type="module">
import { init, run, verify } from '@dlovans/tenet-core';

await init('/path/to/tenet.wasm');
</script>
```

### Node.js Setup

```javascript
import { init, run, verify, lint } from '@dlovans/tenet-core';

// Initialize WASM (required for run/verify)
await init('./node_modules/@dlovans/tenet-core/wasm/tenet.wasm');
```

### Run

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

### Verify

```typescript
import { verify, TenetVerifyResult } from '@dlovans/tenet-core';

const result: TenetVerifyResult = verify(newSchema, oldSchema);

console.log(result.valid); // true or false
console.log(result.error); // Error message if invalid
```

### Lint

**Note:** Lint is pure TypeScript — no WASM init required!

```typescript
import { lint, LintResult } from '@dlovans/tenet-core';

const result: LintResult = lint(schema);

console.log(result.valid); // true if no errors

for (const issue of result.issues) {
  console.log(`${issue.severity}: ${issue.message}`);
  // severity: 'error' | 'warning' | 'info'
}
```

The linter performs 5 static checks:
1. **Schema identification** — Suggests adding `protocol` or `$schema`
2. **Undefined variables** — Detects `{ "var": "x" }` where `x` isn't defined
3. **Potential cycles** — Warns when multiple rules set the same field
4. **Temporal validation** — Checks `temporal_map` entries have `logic_version`
5. **Missing types** — Warns when definitions lack a `type`

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

interface LintResult {
  valid: boolean;
  issues: LintIssue[];
}

interface LintIssue {
  severity: 'error' | 'warning' | 'info';
  field?: string;
  rule?: string;
  message: string;
}

interface TenetVerifyResult {
  valid: boolean;
  error?: string;
}
```
