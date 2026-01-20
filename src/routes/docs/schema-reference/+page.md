# Schema Reference

This page documents the complete structure of a Tenet schema.

## Root Object

A Tenet schema is a JSON object with these top-level fields:

```json
{
  "$schema": "https://tenet.dev/schema/v1.json",
  "protocol": "Tenet_v1.0",
  "schema_id": "loan_application_v2",
  "version": "2026.01.20",
  "valid_from": "2026-01-01",
  
  "definitions": { },
  "logic_tree": [ ],
  "state_model": { },
  "attestations": { },
  "temporal_map": [ ]
}
```

| Field | Required | Description |
|-------|----------|-------------|
| `$schema` | No | URL for IDE autocompletion |
| `protocol` | No | Version identifier (e.g., "Tenet_v1.0") |
| `schema_id` | No | Unique ID for this schema type |
| `version` | No | Your version number |
| `valid_from` | No | When this schema becomes effective |
| `definitions` | **Yes** | Field definitions |
| `logic_tree` | No | Rules to evaluate |
| `state_model` | No | Computed/derived values |
| `attestations` | No | Signature requirements |
| `temporal_map` | No | Date-based rule versioning |

## Output Fields

These are added by `Run()`:

| Field | Type | Description |
|-------|------|-------------|
| `errors` | array | Accumulated validation errors |
| `status` | string | `READY`, `INCOMPLETE`, or `INVALID` |

---

## Definitions

Each definition is a typed field with properties.

```json
"definitions": {
  "loan_amount": {
    "type": "number",
    "value": 50000,
    "label": "Loan Amount ($)",
    "required": true,
    "min": 1000,
    "max": 500000,
    "step": 1000
  }
}
```

### Field Types

| Type | Description | Example Value |
|------|-------------|---------------|
| `string` | Text | `"John Doe"` |
| `number` | Numeric value | `42`, `3.14` |
| `boolean` | True or false | `true`, `false` |
| `select` | One of predefined options | `"employed"` |
| `date` | ISO date string | `"2026-01-20"` |
| `currency` | Money value | `1500.00` |
| `attestation` | Legacy signature field (prefer top-level attestations) | `true` |

### Field Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `type` | string | — | Required. One of the types above. |
| `value` | any | `null` | Current value of the field. |
| `label` | string | — | Human-readable label for UI. |
| `required` | boolean | `false` | Must have a value for READY status. |
| `visible` | boolean | `true` | Whether field is shown in UI. |
| `readonly` | boolean | `false` | Computed field, cannot be edited by user. |
| `options` | string[] | — | Valid values for `select` type. |

### Numeric Constraints

| Field | Type | Description |
|-------|------|-------------|
| `min` | number | Minimum allowed value |
| `max` | number | Maximum allowed value |
| `step` | number | UI increment (e.g., `0.01` for currency) |

### String Constraints

| Field | Type | Description |
|-------|------|-------------|
| `min_length` | integer | Minimum string length |
| `max_length` | integer | Maximum string length |
| `pattern` | string | Regex pattern for validation |

### UI Metadata

| Field | Type | Description |
|-------|------|-------------|
| `ui_class` | string | CSS class for styling |
| `ui_message` | string | Help text or warning |

---

## Logic Tree

The logic tree contains rules that fire when conditions are met.

```json
"logic_tree": [
  {
    "id": "deny_unemployed",
    "law_ref": "Lending Act §4.2",
    "when": {"==": [{"var": "employment"}, "unemployed"]},
    "then": {
      "set": {"approval": "denied"},
      "ui_modify": {"approval": {"ui_class": "error"}},
      "error_msg": "Unemployed applicants are not eligible."
    },
    "disabled": false
  }
]
```

### Rule Properties

| Property | Required | Description |
|----------|----------|-------------|
| `id` | Yes | Unique identifier for this rule |
| `law_ref` | No | Legal citation (e.g., "26 U.S.C. § 61") |
| `when` | Yes | JSON-logic condition |
| `then` | Yes | Actions to take if condition is true |
| `disabled` | No | Skip this rule if true |
| `logic_version` | No | Temporal branch (optional) |

### Actions (the `then` block)

| Action | Description |
|--------|-------------|
| `set` | Set field values |
| `ui_modify` | Change field visibility or requirements |
| `error_msg` | Add a validation error |

### ui_modify Options

```json
"ui_modify": {
  "field_name": {
    "visible": true,
    "required": true,
    "min": 0,
    "max": 100000,
    "ui_class": "highlight",
    "ui_message": "This field is now required"
  }
}
```

---

## State Model

Computed values that are automatically calculated from other fields.

```json
"state_model": {
  "inputs": ["loan_amount", "annual_income"],
  "derived": {
    "debt_to_income_ratio": {
      "eval": {"/": [{"var": "loan_amount"}, {"var": "annual_income"}]}
    },
    "max_loan_eligible": {
      "eval": {"*": [{"var": "annual_income"}, 4]}
    }
  }
}
```

- `inputs`: Fields that trigger recalculation when changed
- `derived`: Computed fields with their formulas
- Derived fields are automatically marked `readonly: true`

---

## Attestations

**Important:** Tenet does NOT create signatures. It only validates that signatures have been collected by your application.

### How Attestations Work

1. Your application collects a signature (via DocuSign, manual sign, etc.)
2. Your application updates the schema with `signed: true` and evidence
3. Tenet validates that required attestations have evidence

### Attestation Structure

```json
"attestations": {
  "compliance_officer_sign": {
    "law_ref": "OSHA 1910.132(d)",
    "statement": "I certify that all safety equipment has been inspected.",
    "required_role": "Safety_Officer",
    "provider": "DocuSign",
    "required": true,
    "signed": false,
    "evidence": null,
    "on_sign": {
      "set": {"inspection_complete": true}
    }
  }
}
```

### Attestation Fields

| Field | Required | Description |
|-------|----------|-------------|
| `statement` | Yes | What the signer is attesting to |
| `law_ref` | No | Legal citation requiring this signature |
| `required_role` | No | Who can sign (for your app to enforce) |
| `provider` | No | Signing method ("DocuSign", "Manual", etc.) |
| `required` | No | Must be signed for READY status |
| `signed` | — | Set by your app when signature collected |
| `evidence` | — | Proof of signature (set by your app) |
| `on_sign` | No | Actions to run after signing |

### Evidence Object

When a signature is collected, your app should populate:

```json
"evidence": {
  "provider_audit_id": "ds_abc123",
  "timestamp": "2026-01-20T14:30:00Z",
  "signer_id": "john.smith@company.com"
}
```

### Validation

For a required attestation, Tenet checks:

1. `signed` must be `true`
2. `evidence` must exist
3. `evidence.provider_audit_id` must not be empty

If any check fails, the document status is `INCOMPLETE`.

---

## Temporal Map

Version logic based on effective dates.

```json
"temporal_map": [
  {
    "valid_range": ["2025-01-01", "2025-12-31"],
    "logic_version": "v2025",
    "status": "ARCHIVED"
  },
  {
    "valid_range": ["2026-01-01", null],
    "logic_version": "v2026",
    "status": "ACTIVE"
  }
]
```

- `valid_range`: [start_date, end_date] — null means open-ended
- `logic_version`: Identifier for which rules apply
- `status`: "ACTIVE" or "ARCHIVED"

Rules with a matching `logic_version` are enabled; others are disabled.

---

## Document Status

| Status | Meaning |
|--------|---------|
| `READY` | All validations pass |
| `INCOMPLETE` | Missing required fields or unsigned attestations |
| `INVALID` | Type errors or constraint violations |

---

## Validation Errors

```json
{
  "errors": [
    {
      "field_id": "loan_amount",
      "rule_id": "max_limit_rule",
      "message": "Loan amount exceeds maximum of 500000",
      "law_ref": "Lending Act §12.3"
    }
  ]
}
```
