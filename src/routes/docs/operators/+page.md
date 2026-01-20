# Operators

Tenet uses JSON-logic syntax for expressions. All operators are nil-safe.

## Variable Access

```json
{"var": "field_name"}
{"var": "nested.path.value"}
```

Returns `null` if the path doesn't exist.

---

## Comparison Operators

| Operator | Example | Description |
|----------|---------|-------------|
| `==` | `{"==": [{"var": "status"}, "active"]}` | Equals |
| `!=` | `{"!=": [{"var": "status"}, "deleted"]}` | Not equals |
| `<` | `{"<": [{"var": "age"}, 18]}` | Less than |
| `>` | `{">": [{"var": "amount"}, 1000]}` | Greater than |
| `<=` | `{"<=": [{"var": "score"}, 100]}` | Less or equal |
| `>=` | `{">=": [{"var": "income"}, 30000]}` | Greater or equal |

**Nil behavior:** Comparisons with `null` return `false`.

---

## Logical Operators

| Operator | Example | Description |
|----------|---------|-------------|
| `and` | `{"and": [cond1, cond2]}` | All conditions true |
| `or` | `{"or": [cond1, cond2]}` | Any condition true |
| `not` | `{"not": condition}` | Negate |

```json
{
  "and": [
    {">=": [{"var": "credit_score"}, 700]},
    {"in": [{"var": "employment"}, ["employed", "self_employed"]]}
  ]
}
```

---

## Conditional (if-then-else)

```json
{"if": [condition, value_if_true, value_if_false]}
```

Multi-branch:

```json
{
  "if": [
    {">=": [{"var": "score"}, 800]}, "excellent",
    {">=": [{"var": "score"}, 700]}, "good",
    {">=": [{"var": "score"}, 600]}, "fair",
    "poor"
  ]
}
```

---

## Arithmetic Operators

| Operator | Example | Description |
|----------|---------|-------------|
| `+` | `{"+": [{"var": "a"}, {"var": "b"}]}` | Add |
| `-` | `{"-": [{"var": "total"}, {"var": "discount"}]}` | Subtract |
| `*` | `{"*": [{"var": "price"}, {"var": "quantity"}]}` | Multiply |
| `/` | `{"/": [{"var": "amount"}, 12]}` | Divide |

**Nil behavior:** Operations with `null` return `null`.

---

## Collection Operators

| Operator | Description | Example |
|----------|-------------|---------|
| `in` | Check if value is in array | `{"in": [{"var": "status"}, ["active", "pending"]]}` |
| `some` | True if any element matches | `{"some": [{"var": "items"}, {"==": [{"var": ""}, "special"]}]}` |
| `all` | True if all elements match | `{"all": [{"var": "scores"}, {">=": [{"var": ""}, 50]}]}` |
| `none` | True if no elements match | `{"none": [{"var": "items"}, {"==": [{"var": ""}, "forbidden"]}]}` |

---

## Date Operators

| Operator | Example | Description |
|----------|---------|-------------|
| `before` | `{"before": [{"var": "start"}, {"var": "end"}]}` | Date A before Date B |
| `after` | `{"after": [{"var": "deadline"}, "2025-12-31"]}` | Date A after Date B |

Dates can be ISO 8601 strings (`"2025-01-16"`) or variables.

---

## Complete Example

```json
{
  "id": "approve_qualified_applicant",
  "when": {
    "and": [
      {">=": [{"var": "credit_score"}, 700]},
      {"in": [{"var": "employment"}, ["employed", "self_employed"]]},
      {"<=": [{"var": "debt_ratio"}, 0.43]}
    ]
  },
  "then": {
    "set": {"approval": "approved", "risk": "low"}
  }
}
```

This rule approves applicants with:
- Credit score ≥ 700
- Employment status is "employed" or "self_employed"
- Debt ratio ≤ 43%
