<script>
    import Button from "$lib/components/ui/Button.svelte";
    import { Play, RotateCcw } from "lucide-svelte";

    const defaultSchema = `{
  "protocol": "Tenet_v1.0",
  "schema_id": "loan_example",
  "version": "1.0",
  "valid_from": "2026-01-01",

  "definitions": {
    "income": {"type": "number", "value": 60000, "label": "Annual Income", "required": true},
    "loan_amount": {"type": "number", "value": 25000, "label": "Loan Amount", "required": true},
    "employment": {"type": "select", "value": "employed", "options": ["employed", "unemployed"]},
    "debt_ratio": {"type": "number", "value": null, "readonly": true},
    "approved": {"type": "boolean", "value": false, "readonly": true}
  },

  "logic_tree": [
    {
      "id": "deny_unemployed",
      "law_ref": "Lending Act §4.2",
      "when": {"==": [{"var": "employment"}, "unemployed"]},
      "then": {
        "set": {"approved": false},
        "error_msg": "Unemployed applicants are not eligible."
      }
    },
    {
      "id": "approve_low_dti",
      "when": {"and": [
        {"<": [{"var": "debt_ratio"}, 0.4]},
        {"!=": [{"var": "employment"}, "unemployed"]}
      ]},
      "then": {"set": {"approved": true}}
    }
  ],

  "state_model": {
    "inputs": ["income", "loan_amount"],
    "derived": {
      "debt_ratio": {"eval": {"/": [{"var": "loan_amount"}, {"var": "income"}]}}
    }
  },

  "attestations": {
    "applicant_sign": {
      "statement": "I certify this information is accurate.",
      "required": true,
      "signed": false
    }
  },

  "temporal_map": [
    {"valid_range": ["2026-01-01", null], "logic_version": "v1", "status": "ACTIVE"}
  ]
}`;

    let schema = $state(defaultSchema);
    let output = $state(`// Click "Run" to execute the schema`);

    function resetSchema() {
        schema = defaultSchema;
        output = `// Click "Run" to execute the schema`;
    }

    function runSchema() {
        // Placeholder - will connect to WASM
        output = `{
  "status": "INCOMPLETE",
  "definitions": {
    "income": {"type": "number", "value": 60000},
    "loan_amount": {"type": "number", "value": 25000},
    "employment": {"type": "select", "value": "employed"},
    "debt_ratio": {"type": "number", "value": 0.4167, "readonly": true},
    "approved": {"type": "boolean", "value": true, "readonly": true}
  },
  "errors": [],
  "attestations": {
    "applicant_sign": {
      "statement": "I certify this information is accurate.",
      "required": true,
      "signed": false
    }
  }
}

// Status is INCOMPLETE because attestation is not signed`;
    }
</script>

<div class="flex h-[calc(100vh-3.5rem)] flex-col">
    <!-- Toolbar -->
    <div
        class="mx-auto max-w-screen-2xl w-full flex items-center justify-between border-b py-2 px-4 sm:px-6 lg:px-8"
    >
        <div class="flex items-center gap-2">
            <h2 class="text-lg font-semibold">Playground</h2>
        </div>
        <div class="flex items-center gap-2">
            <Button variant="outline" size="sm" onclick={resetSchema}>
                <RotateCcw class="mr-2 h-4 w-4" />
                Reset
            </Button>
            <Button size="sm" onclick={runSchema}>
                <Play class="mr-2 h-4 w-4" />
                Run
            </Button>
        </div>
    </div>

    <!-- Editor -->
    <div class="flex-1 overflow-hidden">
        <div class="grid h-full grid-cols-1 md:grid-cols-2">
            <!-- Input -->
            <div class="flex h-full flex-col border-r">
                <div
                    class="border-b bg-muted/40 px-4 py-2 text-sm font-medium text-muted-foreground"
                >
                    Schema Input
                </div>
                <textarea
                    class="flex-1 resize-none bg-background p-4 font-mono text-sm leading-relaxed focus:outline-none"
                    spellcheck="false"
                    bind:value={schema}
                ></textarea>
            </div>

            <!-- Output -->
            <div class="flex h-full flex-col bg-muted/10">
                <div
                    class="border-b bg-muted/40 px-4 py-2 text-sm font-medium text-muted-foreground"
                >
                    Output
                </div>
                <div class="flex-1 overflow-auto p-4">
                    <pre
                        class="font-mono text-sm text-muted-foreground">{output}</pre>
                </div>
            </div>
        </div>
    </div>
</div>
