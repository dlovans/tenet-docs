<script>
    import Button from "$lib/components/ui/Button.svelte";
    import Badge from "$lib/components/ui/Badge.svelte";
    import { Github, ChevronRight, Zap, Clock, ShieldCheck, FileCheck, Globe, Code } from "lucide-svelte";
    import {
        Card,
        CardContent,
        CardDescription,
        CardHeader,
        CardTitle,
    } from "$lib/components/ui/card";
</script>

<!-- Hero Section -->
<section class="border-b-2 border-border">
    <div
        class="container mx-auto flex flex-col items-center gap-4 py-12 text-center md:py-20 lg:py-24 xl:gap-6"
    >
        <!-- Badge Pill -->
        <a
            href="/docs/introduction"
            class="group inline-flex items-center"
        >
            <Badge variant="outline" class="gap-1.5 hover:shadow-[3px_3px_0_0_var(--shadow)] transition-all">
                <span class="flex size-2 rounded-full bg-accent animate-pulse"></span>
                Tenet v0.2.1 Public Beta
                <ChevronRight class="h-3 w-3 text-muted-foreground group-hover:translate-x-0.5 transition-transform" />
            </Badge>
        </a>

        <!-- Headline -->
        <h1
            class="font-mono text-4xl font-bold tracking-tight text-balance lg:leading-[1.1] xl:text-6xl max-w-4xl animate-fade-in"
        >
            <span class="text-primary">Declarative Logic</span>
            <br />
            <span class="text-foreground">for</span> <span class="text-accent">Anything</span>
        </h1>

        <!-- Subtitle -->
        <p class="text-muted-foreground max-w-2xl text-lg text-balance sm:text-xl animate-fade-in">
            A portable, verifiable runtime for encoding business rules, validations,
            and computed state as executable JSON.
        </p>

        <!-- Action Buttons -->
        <div class="flex w-full items-center justify-center gap-3 pt-4 animate-fade-in">
            <Button href="/docs/introduction" size="lg">
                Get Started
            </Button>
            <Button variant="secondary" href="/playground" size="lg">
                Try Playground
            </Button>
        </div>

        <!-- Open Source Badge -->
        <p class="text-sm text-muted-foreground pt-2 font-mono">
            Open Source • MIT Licensed
        </p>
    </div>
</section>

<!-- Feature Grid -->
<section id="features" class="py-16 md:py-20 lg:py-24">
    <div class="container mx-auto">
        <div class="text-center mb-12">
            <h2 class="font-mono text-3xl font-bold text-primary mb-4">Why Tenet?</h2>
            <p class="text-muted-foreground max-w-2xl mx-auto">
                From form validation to complex workflows, from recipe scaling to tax calculations — encode any rule-based logic as portable, verifiable schemas.
            </p>
        </div>
        <div class="grid gap-6 md:grid-cols-2 lg:grid-cols-3">
            <!-- Feature Card 1 -->
            <Card>
                <CardHeader class="pb-3">
                    <div class="flex items-center gap-3">
                        <div class="p-2 rounded-lg bg-accent/20 text-accent">
                            <Zap class="h-5 w-5" />
                        </div>
                        <CardTitle>Reactive Rules</CardTitle>
                    </div>
                </CardHeader>
                <CardContent>
                    <CardDescription class="text-sm leading-relaxed">
                        Define if-then logic that executes instantly. Tenet
                        validates and modifies your data in real-time based on
                        your schema definitions.
                    </CardDescription>
                </CardContent>
            </Card>

            <!-- Feature Card 2 -->
            <Card>
                <CardHeader class="pb-3">
                    <div class="flex items-center gap-3">
                        <div class="p-2 rounded-lg bg-accent/20 text-accent">
                            <Clock class="h-5 w-5" />
                        </div>
                        <CardTitle>Temporal Routing</CardTitle>
                    </div>
                </CardHeader>
                <CardContent>
                    <CardDescription class="text-sm leading-relaxed">
                        Rules evolve. Your code shouldn't break. Version your
                        logic based on effective dates to handle changes
                        effortlessly.
                    </CardDescription>
                </CardContent>
            </Card>

            <!-- Feature Card 3 -->
            <Card>
                <CardHeader class="pb-3">
                    <div class="flex items-center gap-3">
                        <div class="p-2 rounded-lg bg-accent/20 text-accent">
                            <ShieldCheck class="h-5 w-5" />
                        </div>
                        <CardTitle>Attestations</CardTitle>
                    </div>
                </CardHeader>
                <CardContent>
                    <CardDescription class="text-sm leading-relaxed">
                        Signature validation with audit trails. Every state
                        change is traceable to a specific rule version and
                        signer.
                    </CardDescription>
                </CardContent>
            </Card>

            <!-- Feature Card 4 -->
            <Card>
                <CardHeader class="pb-3">
                    <div class="flex items-center gap-3">
                        <div class="p-2 rounded-lg bg-accent/20 text-accent">
                            <FileCheck class="h-5 w-5" />
                        </div>
                        <CardTitle>Verification</CardTitle>
                    </div>
                </CardHeader>
                <CardContent>
                    <CardDescription class="text-sm leading-relaxed">
                        Cryptographically verify that document transformations
                        adhered to the rules at any point in history.
                    </CardDescription>
                </CardContent>
            </Card>

            <!-- Feature Card 5 -->
            <Card>
                <CardHeader class="pb-3">
                    <div class="flex items-center gap-3">
                        <div class="p-2 rounded-lg bg-accent/20 text-accent">
                            <Globe class="h-5 w-5" />
                        </div>
                        <CardTitle>Cross-Platform</CardTitle>
                    </div>
                </CardHeader>
                <CardContent>
                    <CardDescription class="text-sm leading-relaxed">
                        Native Go implementation and JavaScript package for browsers
                        and Node.js. Same logic, everywhere.
                    </CardDescription>
                </CardContent>
            </Card>

            <!-- Feature Card 6 -->
            <Card>
                <CardHeader class="pb-3">
                    <div class="flex items-center gap-3">
                        <div class="p-2 rounded-lg bg-accent/20 text-accent">
                            <Code class="h-5 w-5" />
                        </div>
                        <CardTitle>Open Source</CardTitle>
                    </div>
                </CardHeader>
                <CardContent>
                    <CardDescription class="text-sm leading-relaxed">
                        MIT licensed. Transparent, auditable, and
                        community-driven. Build on top of it or customize it to
                        your needs.
                    </CardDescription>
                </CardContent>
            </Card>
        </div>
    </div>
</section>

<!-- Install Section -->
<section class="border-t-2 border-border py-16 md:py-20 lg:py-24">
    <div class="container mx-auto flex flex-col items-center text-center gap-6">
        <h2 class="font-mono text-3xl font-bold text-primary sm:text-4xl">
            Get Started in Seconds
        </h2>
        <p class="text-muted-foreground max-w-xl text-lg">
            Install the JavaScript package and start encoding your logic.
        </p>

        <!-- Install Command -->
        <div class="w-full max-w-md">
            <div class="rounded-lg border-2 border-border bg-card shadow-[3px_3px_0_0_var(--shadow)] overflow-hidden">
                <div class="bg-secondary/50 px-4 py-2 border-b-2 border-border">
                    <span class="text-xs font-mono text-muted-foreground">npm</span>
                </div>
                <div class="px-4 py-3">
                    <code class="font-mono text-sm text-primary">npm install @dlovans/tenet-core</code>
                </div>
            </div>
        </div>

        <!-- Usage Example -->
        <div class="w-full max-w-lg mt-4">
            <div class="rounded-lg border-2 border-border bg-card shadow-[3px_3px_0_0_var(--shadow)] overflow-hidden text-left">
                <div class="bg-secondary/50 px-4 py-2 border-b-2 border-border">
                    <span class="text-xs font-mono text-muted-foreground">usage</span>
                </div>
                <pre class="px-4 py-3 overflow-x-auto"><code class="font-mono text-sm text-foreground"><span class="text-primary">import</span> {"{"} init, run {"}"} <span class="text-primary">from</span> <span class="text-accent">'@dlovans/tenet-core'</span>;

<span class="text-muted-foreground">// Initialize and run your schema</span>
<span class="text-primary">await</span> init();
<span class="text-primary">const</span> result = run(schema);</code></pre>
            </div>
        </div>

        <div class="flex flex-col sm:flex-row items-center gap-4 pt-4">
            <Button href="/docs/introduction" size="lg">
                Read the Docs
            </Button>
            <Button
                variant="secondary"
                href="https://github.com/dlovans/tenet"
                target="_blank"
                size="lg"
            >
                <Github class="h-5 w-5" />
                Star on GitHub
            </Button>
        </div>
    </div>
</section>
