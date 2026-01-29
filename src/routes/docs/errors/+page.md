# Validation Errors

The Tenet VM validates schemas during execution and returns errors in `result.errors` without failing. This allows developers to inspect all issues at once rather than fixing them one at a time.

## Design Principle

> **The VM is robust**: it returns errors in `result.errors` but continues execution where possible. Only unrecoverable errors (e.g., JSON parse failure) cause the entire run to fail.

## Error Structure

```typescript
interface ValidationError {
  field_id?: string;   // The field that caused the error
  rule_id?: string;    // The rule that triggered the error
  message: string;     // Human-readable error description
  law_ref?: string;    // Legal reference (if applicable)
}
```

## Checking for Errors

```typescript
import { run } from '@dlovans/tenet-core';

const result = run(schema, new Date());

if (result.result?.errors?.length > 0) {
  for (const error of result.result.errors) {
    console.log(`Error: ${error.message}`);
    if (error.field_id) console.log(`  Field: ${error.field_id}`);
    if (error.rule_id) console.log(`  Rule: ${error.rule_id}`);
  }
}

// Status reflects overall document state
console.log(result.result?.status); // "READY" | "INCOMPLETE" | "INVALID"
```

---

## Definition Validation Errors

These errors occur when field values don't match their type constraints.

| Check | Error Message | Status |
|-------|---------------|--------|
| Required field missing | `Required field 'x' is missing` | INCOMPLETE |
| Wrong type (string) | `Field 'x' must be a string` | INVALID |
| Wrong type (number/currency) | `Field 'x' must be a number` | INVALID |
| Wrong type (boolean) | `Field 'x' must be a boolean` | INVALID |
| Wrong type (date) | `Field 'x' must be a valid date` | INVALID |
| Number below min | `Field 'x' value ... is below minimum ...` | INVALID |
| Number above max | `Field 'x' value ... exceeds maximum ...` | INVALID |
| String too short | `Field 'x' is too short (minimum ... characters)` | INVALID |
| String too long | `Field 'x' is too long (maximum ... characters)` | INVALID |
| Pattern mismatch | `Field 'x' does not match required pattern` | INVALID |
| Invalid select option | `Field 'x' value 'y' is not a valid option` | INVALID |

### Example

```json
{
  "definitions": {
    "age": {
      "type": "number",
      "value": -5,
      "min": 0,
      "max": 120
    }
  }
}
```

**Error:** `Field 'age' value -5.00 is below minimum 0.00`

---

## Attestation Validation Errors

These errors occur when required attestations are incomplete.

| Check | Error Message | Status |
|-------|---------------|--------|
| Required attestation not signed | `Required attestation 'x' not signed` | INCOMPLETE |
| Signed but missing evidence | `Attestation 'x' signed but missing evidence` | INCOMPLETE |
| Legacy attestation not confirmed | `Required attestation 'x' not confirmed` | INCOMPLETE |

### Example

```json
{
  "attestations": {
    "terms_accepted": {
      "statement": "I accept the terms and conditions",
      "required": true,
      "signed": false
    }
  }
}
```

**Error:** `Required attestation 'terms_accepted' not signed`

---

## Logic Tree Validation Errors

These errors occur during rule evaluation.

| Check | Error Message | Status |
|-------|---------------|--------|
| Field set by multiple rules | `Potential cycle: field 'x' set by rule 'a' and again by rule 'b'` | - |
| Rule's error_msg triggered | Whatever the rule specifies | INVALID |
| Undefined variable | `Undefined variable 'x' in logic expression` | - |
| Unknown operator | `Unknown operator 'x' in logic expression` | - |

### Example: Rule-Triggered Error

```json
{
  "logic_tree": [
    {
      "id": "deny_minors",
      "when": { "<": [{ "var": "age" }, 18] },
      "then": {
        "error_msg": "Applicants must be 18 or older"
      }
    }
  ]
}
```

When `age < 18`, the VM adds the error: `Applicants must be 18 or older`

### Example: Undefined Variable

```json
{
  "logic_tree": [
    {
      "id": "check_income",
      "when": { ">": [{ "var": "annual_income" }, 50000] },
      "then": { "set": { "eligible": true } }
    }
  ]
}
```

If `annual_income` is not defined in `definitions`, the VM adds: `Undefined variable 'annual_income' in logic expression`

### Example: Unknown Operator

```json
{
  "logic_tree": [
    {
      "id": "bad_rule",
      "when": { "contains": [{ "var": "name" }, "test"] },
      "then": {}
    }
  ]
}
```

**Error:** `Unknown operator 'contains' in logic expression`

---

## Temporal Map Validation Errors

These errors occur when `temporal_map` configuration is invalid.

| Check | Error Message |
|-------|---------------|
| Same start/end date | `Temporal branch N has same start and end date 'YYYY-MM-DD' (invalid range)` |
| Overlapping ranges | `Temporal branch N overlaps with branch M (ranges must not overlap)` |

### Example: Invalid Range

```json
{
  "temporal_map": [
    {
      "valid_range": ["2026-01-01", "2026-01-01"],
      "logic_version": "v1",
      "status": "ACTIVE"
    }
  ]
}
```

**Error:** `Temporal branch 0 has same start and end date '2026-01-01' (invalid range)`

### Example: Overlapping Ranges

```json
{
  "temporal_map": [
    {
      "valid_range": ["2026-01-01", "2026-06-30"],
      "logic_version": "v1",
      "status": "ACTIVE"
    },
    {
      "valid_range": ["2026-03-01", "2026-12-31"],
      "logic_version": "v2",
      "status": "ACTIVE"
    }
  ]
}
```

**Error:** `Temporal branch 1 overlaps with branch 0 (ranges must not overlap)`

---

## Document Status

The VM determines the overall document status based on validation errors:

| Status | Meaning |
|--------|---------|
| `READY` | No errors, document is complete and valid |
| `INCOMPLETE` | Missing required fields or attestations |
| `INVALID` | Type errors, constraint violations, or rule-triggered errors |

### Status Determination Logic

```typescript
function determineStatus(errors: ValidationError[]): DocStatus {
  let hasTypeErrors = false;
  let hasMissingRequired = false;

  for (const err of errors) {
    if (err.message.includes('must be a')) {
      hasTypeErrors = true;
    } else if (err.message.includes('missing') || err.message.includes('Required')) {
      hasMissingRequired = true;
    }
  }

  if (hasTypeErrors) return 'INVALID';
  if (hasMissingRequired) return 'INCOMPLETE';
  return 'READY';
}
```

---

## Best Practices

### 1. Always Check Errors

```typescript
const result = run(schema, new Date());

// Don't assume success
if (result.error) {
  // Parse/system error - schema couldn't be processed
  handleFatalError(result.error);
  return;
}

// Check validation errors
if (result.result.errors?.length > 0) {
  displayValidationErrors(result.result.errors);
}
```

### 2. Display Errors by Field

```typescript
function displayValidationErrors(errors: ValidationError[]) {
  const byField = new Map<string, ValidationError[]>();

  for (const err of errors) {
    const fieldId = err.field_id || '_general';
    if (!byField.has(fieldId)) {
      byField.set(fieldId, []);
    }
    byField.get(fieldId)!.push(err);
  }

  // Display field-specific errors next to their inputs
  for (const [fieldId, fieldErrors] of byField) {
    if (fieldId === '_general') {
      showGlobalErrors(fieldErrors);
    } else {
      showFieldErrors(fieldId, fieldErrors);
    }
  }
}
```

### 3. Use Status for Form Submission

```typescript
function canSubmit(result: TenetResult): boolean {
  return result.result?.status === 'READY';
}
```
