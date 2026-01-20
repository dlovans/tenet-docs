# Examples

Real-world examples demonstrating Tenet's capabilities.

## Loan Application

A loan approval system with credit checks, employment validation, and debt ratio calculation.

```json
{
  "protocol": "Tenet_v1.0",
  "schema_id": "loan_application",
  "version": "2026.01",

  "definitions": {
    "applicant_name": {
      "type": "string",
      "value": null,
      "label": "Full Legal Name",
      "required": true
    },
    "annual_income": {
      "type": "number",
      "value": null,
      "label": "Annual Income ($)",
      "required": true,
      "min": 0
    },
    "loan_amount": {
      "type": "number",
      "value": null,
      "label": "Requested Loan Amount ($)",
      "required": true,
      "min": 1000,
      "max": 500000
    },
    "employment_status": {
      "type": "select",
      "value": null,
      "label": "Employment Status",
      "required": true,
      "options": ["employed", "self_employed", "retired", "unemployed"]
    },
    "debt_to_income": {
      "type": "number",
      "value": null,
      "label": "Debt-to-Income Ratio",
      "readonly": true
    },
    "approval_status": {
      "type": "select",
      "value": "pending",
      "options": ["pending", "approved", "denied", "review"],
      "readonly": true
    }
  },

  "logic_tree": [
    {
      "id": "deny_unemployed",
      "law_ref": "Fair Lending Act § 12.3",
      "when": {"==": [{"var": "employment_status"}, "unemployed"]},
      "then": {
        "set": {"approval_status": "denied"},
        "error_msg": "Applicant must have income source per Fair Lending Act § 12.3"
      }
    },
    {
      "id": "auto_approve_low_dti",
      "when": {"and": [
        {"<": [{"var": "debt_to_income"}, 0.3]},
        {"!=": [{"var": "employment_status"}, "unemployed"]}
      ]},
      "then": {
        "set": {"approval_status": "approved"}
      }
    }
  ],

  "state_model": {
    "inputs": ["loan_amount", "annual_income"],
    "derived": {
      "debt_to_income": {
        "eval": {"/": [{"var": "loan_amount"}, {"var": "annual_income"}]}
      }
    }
  },

  "attestations": {
    "applicant_signature": {
      "statement": "I certify that all information provided is true and accurate.",
      "required": true,
      "signed": false,
      "evidence": null
    }
  }
}
```

**Key features:**
- `debt_to_income` is computed from other fields
- Rules reference the derived value
- Law references enable audit trails
- Attestation required for completion

---

## GDPR Breach Reporting

A compliance form with deadline enforcement based on breach severity.

```json
{
  "protocol": "Tenet_v1.0",
  "schema_id": "gdpr_breach_report",
  "version": "2026.01.20",
  "valid_from": "2026-01-01",

  "definitions": {
    "impact_level": {
      "type": "select",
      "options": ["Low", "Material", "Severe"],
      "value": null,
      "required": true
    },
    "breach_date": {
      "type": "date",
      "value": null,
      "required": true
    },
    "reporting_deadline": {
      "type": "string",
      "value": null,
      "readonly": true
    }
  },

  "logic_tree": [
    {
      "id": "rule_72_hour_limit",
      "law_ref": "GDPR Art. 33(1)",
      "when": {"==": [{"var": "impact_level"}, "Material"]},
      "then": {
        "set": {"reporting_deadline": "72h"},
        "error_msg": "Material impact requires reporting within 72 hours per GDPR Art. 33."
      }
    },
    {
      "id": "rule_severe_escalation",
      "law_ref": "GDPR Art. 34(1)",
      "when": {"==": [{"var": "impact_level"}, "Severe"]},
      "then": {
        "set": {"reporting_deadline": "24h"},
        "error_msg": "Severe impact requires immediate notification per GDPR Art. 34."
      }
    }
  ],

  "attestations": {
    "officer_assertion": {
      "law_ref": "GDPR Art. 33(1)",
      "statement": "I confirm this assessment was made with reasonable effort.",
      "required_role": "Data_Protection_Officer",
      "required": true,
      "signed": false,
      "on_sign": {
        "set": {"can_submit": true}
      }
    }
  }
}
```

**Key features:**
- Different deadlines based on severity
- Legal citations for each rule
- Officer attestation required before submission
- `on_sign` action enables submission

---

## Conditional Survey

Questions that appear based on previous answers.

```json
{
  "protocol": "Tenet_v1.0",
  "schema_id": "onboarding_survey",

  "definitions": {
    "has_children": {"type": "boolean", "value": false, "label": "Do you have children?"},
    "children_count": {"type": "number", "min": 0, "visible": false, "label": "How many?"},
    "employment": {"type": "select", "options": ["employed", "student", "retired"]},
    "company_name": {"type": "string", "visible": false, "label": "Company Name"}
  },

  "logic_tree": [
    {
      "id": "show_children_count",
      "when": {"==": [{"var": "has_children"}, true]},
      "then": {
        "ui_modify": {"children_count": {"visible": true, "required": true}}
      }
    },
    {
      "id": "show_company_name",
      "when": {"==": [{"var": "employment"}, "employed"]},
      "then": {
        "ui_modify": {"company_name": {"visible": true, "required": true}}
      }
    }
  ]
}
```

**Key features:**
- Fields hidden by default (`visible: false`)
- Rules dynamically show and require fields
- No derived state needed — pure UI logic

---

## Safety Compliance with Attestation

A safety inspection form with cryptographic signature verification.

```json
{
  "protocol": "Tenet_v1.0",
  "schema_id": "safety_compliance",
  "version": "2026.01.17",

  "definitions": {
    "employee_count": {"type": "number", "value": 50, "required": true},
    "training_completed": {"type": "boolean", "value": true},
    "safety_certified": {"type": "boolean", "value": false, "readonly": true},
    "can_submit": {"type": "boolean", "value": false, "readonly": true}
  },

  "attestations": {
    "officer_safety_attestation": {
      "law_ref": "OSHA Section 1910.12",
      "statement": "I certify that all employees have completed safety training for the 2026 period.",
      "required_role": "Compliance_Officer",
      "provider": "DocuSign",
      "required": true,
      "signed": false,
      "evidence": null,
      "on_sign": {
        "set": {"safety_certified": true, "can_submit": true}
      }
    },
    "manager_approval": {
      "law_ref": "Internal Policy §3.2",
      "statement": "I approve this safety report for submission.",
      "required_role": "Manager",
      "provider": "Manual",
      "required": false,
      "signed": false
    }
  },

  "logic_tree": [
    {
      "id": "large_workforce_extra_review",
      "when": {">": [{"var": "employee_count"}, 100]},
      "then": {
        "ui_modify": {"manager_approval": {"required": true}},
        "error_msg": "Workforces over 100 require manager approval."
      }
    }
  ]
}
```

**Key features:**
- Two attestations with different requirements
- `on_sign` triggers computed state changes
- Rules can modify attestation requirements dynamically
- Evidence object tracks signature proof

---

## Tax Calculation with Temporal Versioning

A tax form where rates change based on the effective date, demonstrating temporal_map with versioned rules.

```json
{
  "protocol": "Tenet_v1.0",
  "schema_id": "income_tax",
  "version": "2026.01",

  "definitions": {
    "gross_income": {"type": "number", "value": 85000, "label": "Gross Income", "required": true},
    "deductions": {"type": "number", "value": 12000, "label": "Deductions", "min": 0},
    "taxable_income": {"type": "number", "value": null, "readonly": true},
    "tax_rate": {"type": "number", "value": null, "readonly": true},
    "tax_owed": {"type": "number", "value": null, "readonly": true}
  },

  "logic_tree": [
    {
      "id": "tax_rate_2025",
      "logic_version": "v2025",
      "law_ref": "Tax Code § 1.1 (2025)",
      "when": {">=": [{"var": "taxable_income"}, 50000]},
      "then": {"set": {"tax_rate": 0.25}}
    },
    {
      "id": "tax_rate_2025_low",
      "logic_version": "v2025",
      "law_ref": "Tax Code § 1.1 (2025)",
      "when": {"<": [{"var": "taxable_income"}, 50000]},
      "then": {"set": {"tax_rate": 0.15}}
    },
    {
      "id": "tax_rate_2026",
      "logic_version": "v2026",
      "law_ref": "Tax Code § 1.1 (2026 Amendment)",
      "when": {">=": [{"var": "taxable_income"}, 50000]},
      "then": {"set": {"tax_rate": 0.28}}
    },
    {
      "id": "tax_rate_2026_low",
      "logic_version": "v2026",
      "law_ref": "Tax Code § 1.1 (2026 Amendment)",
      "when": {"<": [{"var": "taxable_income"}, 50000]},
      "then": {"set": {"tax_rate": 0.12}}
    }
  ],

  "state_model": {
    "inputs": ["gross_income", "deductions", "tax_rate"],
    "derived": {
      "taxable_income": {
        "eval": {"-": [{"var": "gross_income"}, {"var": "deductions"}]}
      },
      "tax_owed": {
        "eval": {"*": [{"var": "taxable_income"}, {"var": "tax_rate"}]}
      }
    }
  },

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
}
```

**Key features:**
- Different tax rates for 2025 vs 2026
- Rules tagged with `logic_version` to match temporal branches
- Running with `date = 2025-06-01` uses 25% rate
- Running with `date = 2026-06-01` uses 28% rate
- `law_ref` citations for each version
