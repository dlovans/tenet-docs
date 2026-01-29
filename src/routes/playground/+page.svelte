<script>
    import { onMount } from "svelte";
    import Button from "$lib/components/ui/Button.svelte";
    import { Play, RotateCcw, Loader2, Code, PanelLeft, ChevronDown } from "lucide-svelte";
    import { init, run, isReady } from "@dlovans/tenet-core";

    const examples = {
        loan: {
            name: "Loan Application",
            description: "Simple loan approval with debt-to-income calculation",
            schema: `{
  "protocol": "Tenet_v1.0",
  "schema_id": "loan_example",
  "version": "1.0",
  "valid_from": "2026-01-01",

  "definitions": {
    "income": {"type": "number", "value": 60000, "label": "Annual Income", "required": true},
    "loan_amount": {"type": "number", "value": 25000, "label": "Loan Amount", "required": true},
    "employment": {"type": "select", "value": "employed", "options": ["employed", "unemployed"]},
    "debt_ratio": {"type": "number", "value": null, "readonly": true, "label": "Debt Ratio"},
    "approved": {"type": "boolean", "value": false, "readonly": true, "label": "Approved"}
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
      "statement": "I acknowledge this is a preliminary assessment only.",
      "required": false,
      "signed": false
    }
  },

  "temporal_map": [
    {"valid_range": ["2026-01-01", null], "logic_version": "v1", "status": "ACTIVE"}
  ]
}`
        },
        recipe: {
            name: "Recipe Scaler",
            description: "Scale recipe ingredients based on servings",
            schema: `{
  "protocol": "Tenet_v1.0",
  "schema_id": "recipe_scaler",
  "version": "1.0",
  "valid_from": "2025-01-01",

  "definitions": {
    "base_servings": {"type": "number", "value": 4, "label": "Original Servings", "readonly": true},
    "target_servings": {"type": "number", "value": 8, "label": "Target Servings", "required": true, "min": 1, "max": 100},
    "flour_base": {"type": "number", "value": 200, "label": "Flour (g) - base", "readonly": true},
    "sugar_base": {"type": "number", "value": 100, "label": "Sugar (g) - base", "readonly": true},
    "eggs_base": {"type": "number", "value": 2, "label": "Eggs - base", "readonly": true},
    "flour_scaled": {"type": "number", "value": null, "label": "Flour (g) - scaled", "readonly": true},
    "sugar_scaled": {"type": "number", "value": null, "label": "Sugar (g) - scaled", "readonly": true},
    "eggs_scaled": {"type": "number", "value": null, "label": "Eggs - scaled", "readonly": true},
    "scale_factor": {"type": "number", "value": null, "label": "Scale Factor", "readonly": true}
  },

  "logic_tree": [
    {
      "id": "warn_large_batch",
      "when": {">": [{"var": "target_servings"}, 20]},
      "then": {
        "ui_modify": {"target_servings": {"ui_class": "warning", "ui_message": "Large batch - consider mixing in parts"}}
      }
    }
  ],

  "state_model": {
    "inputs": ["target_servings", "base_servings", "flour_base", "sugar_base", "eggs_base"],
    "derived": {
      "scale_factor": {"eval": {"/": [{"var": "target_servings"}, {"var": "base_servings"}]}},
      "flour_scaled": {"eval": {"*": [{"var": "flour_base"}, {"var": "scale_factor"}]}},
      "sugar_scaled": {"eval": {"*": [{"var": "sugar_base"}, {"var": "scale_factor"}]}},
      "eggs_scaled": {"eval": {"*": [{"var": "eggs_base"}, {"var": "scale_factor"}]}}
    }
  },

  "temporal_map": [
    {"valid_range": ["2025-01-01", null], "logic_version": "v1", "status": "ACTIVE"}
  ]
}`
        },
        tax: {
            name: "Tax Rate Calculator",
            description: "Different tax rules for 2025 vs 2026 (2 temporal branches)",
            schema: `{
  "protocol": "Tenet_v1.0",
  "schema_id": "tax_calculator",
  "version": "2.0",
  "valid_from": "2025-01-01",

  "definitions": {
    "gross_income": {"type": "currency", "value": 75000, "label": "Gross Income", "required": true, "min": 0},
    "filing_status": {"type": "select", "value": "single", "label": "Filing Status", "options": ["single", "married", "head_of_household"]},
    "deductions": {"type": "currency", "value": 13850, "label": "Deductions", "min": 0},
    "taxable_income": {"type": "currency", "value": null, "label": "Taxable Income", "readonly": true},
    "tax_rate": {"type": "number", "value": null, "label": "Effective Tax Rate (%)", "readonly": true},
    "estimated_tax": {"type": "currency", "value": null, "label": "Estimated Tax", "readonly": true}
  },

  "logic_tree": [
    {
      "id": "bracket_10",
      "logic_version": "v2025",
      "when": {"<=": [{"var": "taxable_income"}, 11000]},
      "then": {"set": {"tax_rate": 10}}
    },
    {
      "id": "bracket_12",
      "logic_version": "v2025",
      "when": {"and": [
        {">": [{"var": "taxable_income"}, 11000]},
        {"<=": [{"var": "taxable_income"}, 44725]}
      ]},
      "then": {"set": {"tax_rate": 12}}
    },
    {
      "id": "bracket_22",
      "logic_version": "v2025",
      "when": {">": [{"var": "taxable_income"}, 44725]},
      "then": {"set": {"tax_rate": 22}}
    },
    {
      "id": "bracket_10_2026",
      "logic_version": "v2026",
      "when": {"<=": [{"var": "taxable_income"}, 11600]},
      "then": {"set": {"tax_rate": 10}}
    },
    {
      "id": "bracket_12_2026",
      "logic_version": "v2026",
      "when": {"and": [
        {">": [{"var": "taxable_income"}, 11600]},
        {"<=": [{"var": "taxable_income"}, 47150]}
      ]},
      "then": {"set": {"tax_rate": 12}}
    },
    {
      "id": "bracket_22_2026",
      "logic_version": "v2026",
      "when": {">": [{"var": "taxable_income"}, 47150]},
      "then": {"set": {"tax_rate": 22}}
    }
  ],

  "state_model": {
    "inputs": ["gross_income", "deductions", "tax_rate"],
    "derived": {
      "taxable_income": {"eval": {"-": [{"var": "gross_income"}, {"var": "deductions"}]}},
      "estimated_tax": {"eval": {"*": [{"var": "taxable_income"}, {"/": [{"var": "tax_rate"}, 100]}]}}
    }
  },

  "attestations": {
    "accuracy": {
      "statement": "I confirm the income figures provided are accurate.",
      "required": false,
      "signed": false
    }
  },

  "temporal_map": [
    {"valid_range": ["2025-01-01", "2025-12-31"], "logic_version": "v2025", "status": "ACTIVE"},
    {"valid_range": ["2026-01-01", null], "logic_version": "v2026", "status": "ACTIVE"}
  ]
}`
        },
        survey: {
            name: "Conditional Survey",
            description: "Show/hide questions based on previous answers",
            schema: `{
  "protocol": "Tenet_v1.0",
  "schema_id": "conditional_survey",
  "version": "1.0",
  "valid_from": "2025-01-01",

  "definitions": {
    "has_pet": {"type": "boolean", "value": false, "label": "Do you have a pet?"},
    "pet_type": {"type": "select", "value": null, "label": "What type of pet?", "options": ["dog", "cat", "bird", "fish", "other"], "visible": false},
    "pet_name": {"type": "string", "value": "", "label": "Pet's name", "visible": false, "max_length": 50},
    "dog_breed": {"type": "string", "value": "", "label": "Dog breed", "visible": false},
    "walks_per_day": {"type": "number", "value": null, "label": "Walks per day", "visible": false, "min": 0, "max": 10},
    "has_allergies": {"type": "boolean", "value": false, "label": "Do you have pet allergies?"},
    "allergy_note": {"type": "string", "value": "", "label": "Allergy details", "visible": false, "ui_message": "Please describe your allergies"}
  },

  "logic_tree": [
    {
      "id": "show_pet_questions",
      "when": {"==": [{"var": "has_pet"}, true]},
      "then": {
        "ui_modify": {
          "pet_type": {"visible": true, "required": true},
          "pet_name": {"visible": true}
        }
      }
    },
    {
      "id": "show_dog_questions",
      "when": {"and": [
        {"==": [{"var": "has_pet"}, true]},
        {"==": [{"var": "pet_type"}, "dog"]}
      ]},
      "then": {
        "ui_modify": {
          "dog_breed": {"visible": true},
          "walks_per_day": {"visible": true, "required": true}
        }
      }
    },
    {
      "id": "show_allergy_details",
      "when": {"==": [{"var": "has_allergies"}, true]},
      "then": {
        "ui_modify": {
          "allergy_note": {"visible": true, "required": true}
        }
      }
    }
  ],

  "temporal_map": [
    {"valid_range": ["2025-01-01", null], "logic_version": "v1", "status": "ACTIVE"}
  ]
}`
        }
    };

    let selectedExample = $state("loan");
    let schema = $state(examples.loan.schema);
    let output = $state(`// Initializing...`);
    let isInitializing = $state(true);
    let initError = $state("");
    let isRunning = $state(false);
    let autoRun = $state(true);
    let viewMode = $state("code"); // "code" or "ui"

    // UI mode state
    let uiSchema = $state(null);
    let uiErrors = $state([]);

    let textareaEl = $state(null);
    let lineNumbersEl = $state(null);
    let debounceTimer = null;

    let lineCount = $derived(schema.split("\n").length);

    // Parse schema for UI mode
    function parseSchemaForUI() {
        try {
            return JSON.parse(schema);
        } catch {
            return null;
        }
    }

    // Run schema and update UI state
    function runSchemaForUI() {
        if (!isReady()) return;

        try {
            const result = run(schema);
            if (result.error) {
                uiErrors = [{ message: result.error }];
                uiSchema = parseSchemaForUI();
            } else if (result.result) {
                uiSchema = result.result;
                uiErrors = result.result.errors || [];
            }
        } catch (err) {
            uiErrors = [{ message: err instanceof Error ? err.message : "Unknown error" }];
        }
    }

    // Update field value and re-run
    function updateFieldValue(fieldId, newValue) {
        try {
            const parsed = JSON.parse(schema);
            if (parsed.definitions && parsed.definitions[fieldId]) {
                parsed.definitions[fieldId].value = newValue;
                schema = JSON.stringify(parsed, null, 2);
            }
        } catch {
            // Invalid JSON, ignore
        }
    }

    // Update attestation signed status
    function updateAttestationSigned(attId, signed) {
        try {
            const parsed = JSON.parse(schema);
            if (parsed.attestations && parsed.attestations[attId]) {
                parsed.attestations[attId].signed = signed;
                schema = JSON.stringify(parsed, null, 2);
            }
        } catch {
            // Invalid JSON, ignore
        }
    }

    // Sync UI when switching to UI mode
    $effect(() => {
        if (viewMode === "ui" && !isInitializing) {
            runSchemaForUI();
        }
    });

    function syncScroll() {
        if (lineNumbersEl && textareaEl) {
            lineNumbersEl.scrollTop = textareaEl.scrollTop;
        }
    }

    // Auto-run on schema change (debounced)
    $effect(() => {
        if (!autoRun || isInitializing || initError) return;

        // Access schema to track it
        const _ = schema;

        // Debounce to avoid running on every keystroke
        if (debounceTimer) clearTimeout(debounceTimer);
        debounceTimer = setTimeout(() => {
            runSchema();
            if (viewMode === "ui") {
                runSchemaForUI();
            }
        }, 300);

        return () => {
            if (debounceTimer) clearTimeout(debounceTimer);
        };
    });

    onMount(async () => {
        try {
            await init();
            isInitializing = false;
            // Run once on init
            runSchema();
            runSchemaForUI();
        } catch (err) {
            initError = err instanceof Error ? err.message : "Failed to initialize Tenet VM";
            isInitializing = false;
        }
    });

    function resetSchema() {
        schema = examples[selectedExample].schema;
        output = `// Click "Run" to execute the schema`;
    }

    function handleExampleChange(exampleKey) {
        selectedExample = exampleKey;
        schema = examples[exampleKey].schema;
    }

    function runSchema() {
        if (!isReady()) {
            output = `// Error: Tenet VM is not ready`;
            return;
        }

        isRunning = true;

        try {
            // Run the schema through the VM
            const result = run(schema);

            if (result.error) {
                output = `// Error: ${result.error}`;
            } else if (result.result) {
                output = JSON.stringify(result.result, null, 2);
            } else {
                output = `// Unexpected result format`;
            }
        } catch (err) {
            output = `// Error: ${err instanceof Error ? err.message : "Unknown error"}`;
        } finally {
            isRunning = false;
        }
    }
</script>

<div class="flex h-[calc(100vh-3.5rem)] flex-col">
    <!-- Toolbar -->
    <div
        class="mx-auto max-w-screen-2xl w-full flex items-center justify-between border-b py-2 px-4 sm:px-6 lg:px-8"
    >
        <div class="flex items-center gap-3">
            <h2 class="text-lg font-semibold">Playground</h2>
            <div class="relative">
                <select
                    value={selectedExample}
                    onchange={(e) => handleExampleChange(e.target.value)}
                    class="appearance-none rounded-md border bg-background pl-3 pr-8 py-1.5 text-sm focus:outline-none focus:ring-2 focus:ring-primary cursor-pointer"
                >
                    {#each Object.entries(examples) as [key, example]}
                        <option value={key}>{example.name}</option>
                    {/each}
                </select>
                <ChevronDown class="absolute right-2 top-1/2 -translate-y-1/2 h-4 w-4 text-muted-foreground pointer-events-none" />
            </div>
        </div>
        <div class="flex items-center gap-2">
            {#if initError}
                <span class="text-sm text-red-500">{initError}</span>
            {:else if isInitializing}
                <span class="flex items-center text-sm text-muted-foreground">
                    <Loader2 class="mr-2 h-4 w-4 animate-spin" />
                    Loading VM...
                </span>
            {/if}
            <!-- View Mode Toggle -->
            <div class="flex items-center rounded-md border bg-muted/40 p-0.5">
                <button
                    class="flex items-center gap-1 rounded px-2 py-1 text-sm transition-colors {viewMode === 'code' ? 'bg-background shadow-sm' : 'text-muted-foreground hover:text-foreground'}"
                    onclick={() => viewMode = 'code'}
                >
                    <Code class="h-4 w-4" />
                    Code
                </button>
                <button
                    class="flex items-center gap-1 rounded px-2 py-1 text-sm transition-colors {viewMode === 'ui' ? 'bg-background shadow-sm' : 'text-muted-foreground hover:text-foreground'}"
                    onclick={() => viewMode = 'ui'}
                >
                    <PanelLeft class="h-4 w-4" />
                    UI
                </button>
            </div>
            {#if viewMode === 'code'}
                <label class="flex items-center gap-2 text-sm text-muted-foreground cursor-pointer select-none">
                    <input
                        type="checkbox"
                        bind:checked={autoRun}
                        class="h-4 w-4 rounded border-muted-foreground/50"
                    />
                    Auto-run
                </label>
            {/if}
            <Button variant="outline" size="sm" onclick={resetSchema} disabled={isRunning}>
                <RotateCcw class="mr-2 h-4 w-4" />
                Reset
            </Button>
            {#if viewMode === 'code' && !autoRun}
                <Button size="sm" onclick={runSchema} disabled={isInitializing || isRunning || !!initError}>
                    {#if isRunning}
                        <Loader2 class="mr-2 h-4 w-4 animate-spin" />
                        Running...
                    {:else}
                        <Play class="mr-2 h-4 w-4" />
                        Run
                    {/if}
                </Button>
            {/if}
        </div>
    </div>

    <!-- Editor -->
    <div class="flex-1 overflow-hidden">
        {#if viewMode === 'code'}
            <div class="grid h-full grid-cols-1 md:grid-cols-2">
                <!-- Input -->
                <div class="flex h-full flex-col border-r overflow-hidden">
                    <div
                        class="border-b bg-muted/40 px-4 py-2 text-sm font-medium text-muted-foreground"
                    >
                        Schema Input
                    </div>
                    <div class="flex flex-1 overflow-hidden">
                        <div
                            bind:this={lineNumbersEl}
                            class="overflow-hidden bg-muted/20 py-4 pl-4 pr-2 text-right font-mono text-sm leading-relaxed text-muted-foreground/50 select-none"
                        >
                            {#each Array(lineCount) as _, i}
                                <div>{i + 1}</div>
                            {/each}
                        </div>
                        <textarea
                            bind:this={textareaEl}
                            class="flex-1 resize-none overflow-auto bg-background py-4 pr-4 pl-2 font-mono text-sm leading-relaxed focus:outline-none"
                            spellcheck="false"
                            bind:value={schema}
                            onscroll={syncScroll}
                        ></textarea>
                    </div>
                </div>

                <!-- Output -->
                <div class="flex h-full flex-col bg-muted/10 overflow-hidden">
                    <div
                        class="border-b bg-muted/40 px-4 py-2 text-sm font-medium text-muted-foreground"
                    >
                        Output
                    </div>
                    <div class="flex-1 overflow-auto p-4">
                        <pre
                            class="font-mono text-sm text-muted-foreground whitespace-pre">{output}</pre>
                    </div>
                </div>
            </div>
        {:else}
            <!-- UI Mode -->
            <div class="grid h-full grid-cols-1 md:grid-cols-2">
                <!-- Form -->
                <div class="flex h-full flex-col border-r overflow-hidden">
                    <div
                        class="border-b bg-muted/40 px-4 py-2 text-sm font-medium text-muted-foreground flex items-center justify-between"
                    >
                        <div class="flex items-center gap-3">
                            <span>Form</span>
                            {#if uiSchema?.schema_id || uiSchema?.version}
                                <span class="text-xs text-muted-foreground/70">
                                    {uiSchema.schema_id || 'untitled'}{#if uiSchema.version} v{uiSchema.version}{/if}
                                </span>
                            {/if}
                        </div>
                        <div class="flex items-center gap-2">
                            {#if uiSchema?.temporal_map?.length}
                                {@const activeBranch = uiSchema.temporal_map.find(b => b.status === 'ACTIVE')}
                                {#if activeBranch?.logic_version}
                                    <span class="px-2 py-0.5 rounded text-xs bg-blue-500/20 text-blue-600">
                                        {activeBranch.logic_version}
                                    </span>
                                {/if}
                            {/if}
                            {#if uiSchema?.status}
                                <span class="px-2 py-0.5 rounded text-xs font-medium {uiSchema.status === 'READY' ? 'bg-green-500/20 text-green-600' : uiSchema.status === 'INVALID' ? 'bg-red-500/20 text-red-600' : 'bg-yellow-500/20 text-yellow-600'}">
                                    {uiSchema.status}
                                </span>
                            {/if}
                        </div>
                    </div>
                    <div class="flex-1 overflow-auto p-4">
                        {#if uiSchema?.definitions}
                            <div class="space-y-4 max-w-md">
                                {#each Object.entries(uiSchema.definitions) as [fieldId, def]}
                                    {#if def.visible !== false}
                                        <div class="space-y-1">
                                            <label for={fieldId} class="block text-sm font-medium {def.readonly ? 'text-muted-foreground' : ''}">
                                                {def.label || fieldId}
                                                {#if def.required}
                                                    <span class="text-red-500">*</span>
                                                {/if}
                                            </label>

                                            {#if def.type === 'string'}
                                                <input
                                                    id={fieldId}
                                                    type="text"
                                                    value={def.value ?? ''}
                                                    disabled={def.readonly}
                                                    oninput={(e) => updateFieldValue(fieldId, e.target.value)}
                                                    class="w-full rounded-md border bg-background px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-primary disabled:bg-muted disabled:text-muted-foreground"
                                                />
                                            {:else if def.type === 'number' || def.type === 'currency'}
                                                <input
                                                    id={fieldId}
                                                    type="number"
                                                    value={def.value ?? ''}
                                                    disabled={def.readonly}
                                                    min={def.min}
                                                    max={def.max}
                                                    step={def.step ?? 'any'}
                                                    oninput={(e) => updateFieldValue(fieldId, e.target.value ? Number(e.target.value) : null)}
                                                    class="w-full rounded-md border bg-background px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-primary disabled:bg-muted disabled:text-muted-foreground"
                                                />
                                            {:else if def.type === 'boolean'}
                                                <div class="flex items-center gap-2">
                                                    <input
                                                        id={fieldId}
                                                        type="checkbox"
                                                        checked={def.value === true}
                                                        disabled={def.readonly}
                                                        onchange={(e) => updateFieldValue(fieldId, e.target.checked)}
                                                        class="h-4 w-4 rounded border disabled:opacity-50"
                                                    />
                                                    <span class="text-sm text-muted-foreground">{def.value ? 'Yes' : 'No'}</span>
                                                </div>
                                            {:else if def.type === 'select'}
                                                <select
                                                    id={fieldId}
                                                    value={def.value ?? ''}
                                                    disabled={def.readonly}
                                                    onchange={(e) => updateFieldValue(fieldId, e.target.value)}
                                                    class="w-full rounded-md border bg-background px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-primary disabled:bg-muted disabled:text-muted-foreground"
                                                >
                                                    {#if def.options}
                                                        {#each def.options as option}
                                                            <option value={option}>{option}</option>
                                                        {/each}
                                                    {/if}
                                                </select>
                                            {:else if def.type === 'date'}
                                                <input
                                                    id={fieldId}
                                                    type="date"
                                                    value={def.value ?? ''}
                                                    disabled={def.readonly}
                                                    oninput={(e) => updateFieldValue(fieldId, e.target.value)}
                                                    class="w-full rounded-md border bg-background px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-primary disabled:bg-muted disabled:text-muted-foreground"
                                                />
                                            {:else}
                                                <input
                                                    id={fieldId}
                                                    type="text"
                                                    value={def.value ?? ''}
                                                    disabled={def.readonly}
                                                    oninput={(e) => updateFieldValue(fieldId, e.target.value)}
                                                    class="w-full rounded-md border bg-background px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-primary disabled:bg-muted disabled:text-muted-foreground"
                                                />
                                            {/if}

                                            {#if def.ui_message}
                                                <p class="text-xs text-muted-foreground">{def.ui_message}</p>
                                            {/if}
                                        </div>
                                    {/if}
                                {/each}

                                <!-- Attestations -->
                                {#if uiSchema?.attestations}
                                    <div class="border-t pt-4 mt-4">
                                        <h3 class="text-sm font-medium mb-3">Attestations</h3>
                                        {#each Object.entries(uiSchema.attestations) as [attId, att]}
                                            <div class="flex items-start gap-3 p-3 rounded-md border bg-muted/20">
                                                <input
                                                    id={attId}
                                                    type="checkbox"
                                                    checked={att.signed === true}
                                                    onchange={(e) => updateAttestationSigned(attId, e.target.checked)}
                                                    class="h-4 w-4 rounded border mt-0.5"
                                                />
                                                <div class="flex-1">
                                                    <label for={attId} class="text-sm cursor-pointer">
                                                        {att.statement}
                                                        {#if att.required}
                                                            <span class="text-red-500">*</span>
                                                        {/if}
                                                    </label>
                                                    {#if att.law_ref}
                                                        <p class="text-xs text-muted-foreground mt-1">{att.law_ref}</p>
                                                    {/if}
                                                </div>
                                            </div>
                                        {/each}
                                    </div>
                                {/if}
                            </div>
                        {:else}
                            <p class="text-sm text-muted-foreground">Invalid schema or no definitions found.</p>
                        {/if}
                    </div>
                </div>

                <!-- Errors & Status -->
                <div class="flex h-full flex-col bg-muted/10 overflow-hidden">
                    <div
                        class="border-b bg-muted/40 px-4 py-2 text-sm font-medium text-muted-foreground"
                    >
                        Validation
                    </div>
                    <div class="flex-1 overflow-auto p-4">
                        {#if uiErrors.length > 0}
                            <div class="space-y-2">
                                {#each uiErrors as error}
                                    <div class="flex items-start gap-2 p-3 rounded-md bg-red-500/10 border border-red-500/20">
                                        <span class="text-red-500 text-sm">
                                            {#if error.field_id}
                                                <span class="font-medium">{error.field_id}:</span>
                                            {/if}
                                            {error.message}
                                            {#if error.law_ref}
                                                <span class="text-muted-foreground">({error.law_ref})</span>
                                            {/if}
                                        </span>
                                    </div>
                                {/each}
                            </div>
                        {:else if uiSchema}
                            <div class="flex items-center gap-2 p-3 rounded-md bg-green-500/10 border border-green-500/20">
                                <span class="text-green-600 text-sm">No validation errors</span>
                            </div>
                        {:else}
                            <p class="text-sm text-muted-foreground">Run the schema to see validation results.</p>
                        {/if}

                        {#if uiSchema}
                            <div class="mt-6">
                                <h3 class="text-sm font-medium text-muted-foreground mb-2">Raw Output</h3>
                                <pre class="font-mono text-xs text-muted-foreground whitespace-pre overflow-auto p-3 rounded-md bg-muted/30 max-h-64">{JSON.stringify(uiSchema, null, 2)}</pre>
                            </div>
                        {/if}
                    </div>
                </div>
            </div>
        {/if}
    </div>
</div>
