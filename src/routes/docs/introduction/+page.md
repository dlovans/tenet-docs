# Introduction

Tenet is a **logic engine** that processes JSON documents according to rules you define. Think of it as a smart form processor that can validate data, compute values, and enforce signature requirements.

## What is Tenet?

Tenet is a declarative VM that standardizes business and regulatory logic into machine-readable JSON that can be executed, validated, and audited.

Instead of embedding compliance rules in application code, you express them declaratively:

```json
{
  "protocol": "Tenet_v1.0",
  "schema_id": "gdpr_breach_report",
  "definitions": {
    "breach_type": {"type": "select", "value": "material", "options": ["minor", "material", "severe"]},
    "reporting_deadline": {"type": "string"}
  },
  "logic_tree": [
    {
      "id": "gdpr_72h_rule",
      "law_ref": "GDPR Art. 33(1)",
      "when": {"==": [{"var": "breach_type"}, "material"]},
      "then": {
        "set": {"reporting_deadline": "72h"},
        "error_msg": "Material breaches must be reported within 72 hours."
      }
    }
  ]
}
```

When you run this through Tenet, it:
1. Evaluates all rules in the `logic_tree`
2. Computes any `derived` values in the `state_model`
3. Validates all `definitions` against their constraints
4. Returns the transformed JSON with `errors`, `status`, and audit trail

## What Tenet is NOT

- **NOT a database** — Tenet doesn't store data. It takes JSON in, processes it, and returns JSON out.
- **NOT a signing service** — Tenet validates that signatures were collected by YOUR application. It doesn't create signatures.
- **NOT a blockchain** — No cryptographic ledger or consensus mechanism.

## Document Status

Every processed document gets one of three statuses:

| Status | Meaning |
|--------|---------|
| `READY` | All validations pass. Document is complete. |
| `INCOMPLETE` | Missing required fields or unsigned attestations. |
| `INVALID` | Type errors, constraint violations, or rule violations. |

## Primary Use Case: Law as Code

Tenet was built to **standardize legal requirements in the digital world**:

- **Regulatory Compliance** — Encode laws (GDPR, NIS2, lending regulations) as executable schemas
- **Legal Audit Trails** — Every rule references its `law_ref` for traceability
- **Temporal Versioning** — Laws change; Tenet handles version routing by effective date
- **Verification** — Prove that document transformations are legal

## Other Applications

Because Tenet is a general-purpose declarative logic engine, developers have found many uses:

- **Dynamic Forms** — Conditional fields, computed values, reactive validation
- **E-commerce** — Pricing rules, discount codes, eligibility checks
- **Configuration Wizards** — Dependency-aware software setup
- **Game Systems** — Character builders, skill trees with constraints

## Key Concepts

| Concept | Description |
|---------|-------------|
| **Definitions** | Typed fields with values and constraints |
| **Logic Tree** | Rules that fire when conditions are met |
| **State Model** | Computed/derived values (marked `readonly`) |
| **Attestations** | Signature requirements with evidence tracking |
| **Temporal Map** | Version logic based on effective dates |

## Installation

```bash
# npm (browser + Node.js)
npm install @dlovans/tenet-core
```

## Next Steps

- [Schema Reference](/docs/schema-reference) — Full definition syntax
- [Operators](/docs/operators) — JSON-logic operators
- [API Reference](/docs/api-reference) — Go, JavaScript, and CLI usage
- [Examples](/docs/examples) — Real-world use cases
