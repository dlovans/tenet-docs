<script>
    import Button from "$lib/components/ui/Button.svelte";
    import { Github, ChevronRight } from "lucide-svelte";
    import {
        Card,
        CardContent,
        CardDescription,
        CardHeader,
        CardTitle,
    } from "$lib/components/ui/card";
</script>

<!-- Hero Section -->
<section class="border-b">
    <div
        class="container mx-auto flex flex-col items-center gap-2 py-8 text-center md:py-16 lg:py-20 xl:gap-4"
    >
        <!-- Badge Pill -->
        <a
            href="/docs/introduction"
            class="inline-flex items-center justify-center rounded-full border px-2.5 py-0.5 text-xs font-medium gap-1 bg-transparent text-foreground hover:bg-accent transition-colors"
        >
            <span class="flex size-2 rounded-full bg-blue-500"></span>
            Tenet v0.1.1 Public Beta
            <ChevronRight class="h-3 w-3 text-muted-foreground" />
        </a>

        <!-- Headline -->
        <h1
            class="text-primary text-4xl font-semibold tracking-tight text-balance lg:leading-[1.1] xl:text-5xl xl:tracking-tighter max-w-4xl"
        >
            The Foundation for Declarative Business Logic
        </h1>

        <!-- Subtitle -->
        <p class="text-foreground max-w-3xl text-base text-balance sm:text-lg">
            A portable, verifiable runtime for encoding regulations, validation
            rules, and computed state as executable JSON. Open Source. Open
            Code.
        </p>

        <!-- Action Buttons -->
        <div class="flex w-full items-center justify-center gap-2 pt-2">
            <Button
                href="/docs/introduction"
                class="h-8 px-3 text-sm rounded-lg"
            >
                Get Started
            </Button>
            <Button
                variant="ghost"
                href="/docs"
                class="h-8 px-3 text-sm rounded-lg"
            >
                Documentation
            </Button>
        </div>
    </div>
</section>

<!-- Secondary Navigation / Feature Tabs -->
<section class="border-b">
    <div class="container mx-auto">
        <div class="flex items-center justify-between overflow-x-auto py-3">
            <nav
                class="flex items-center gap-4 sm:gap-6 text-sm font-medium text-muted-foreground"
            >
                <a href="#features" class="text-foreground font-semibold"
                    >Features</a
                >
                <a href="/docs" class="hover:text-foreground transition-colors"
                    >Documentation</a
                >
                <a
                    href="/playground"
                    class="hover:text-foreground transition-colors"
                    >Playground</a
                >
                <a
                    href="https://github.com/dlovans/tenet"
                    target="_blank"
                    class="hover:text-foreground transition-colors">GitHub</a
                >
            </nav>
        </div>
    </div>
</section>

<!-- Feature Grid -->
<section id="features" class="py-12 md:py-16 lg:py-20">
    <div class="container mx-auto">
        <div class="grid gap-4 md:grid-cols-2 lg:grid-cols-3">
            <!-- Feature Card 1 -->
            <Card class="rounded-xl shadow-none">
                <CardHeader class="pb-2">
                    <CardTitle class="text-base font-semibold"
                        >Reactive Rules</CardTitle
                    >
                </CardHeader>
                <CardContent>
                    <CardDescription class="text-sm">
                        Define if-then logic that executes instantly. Tenet
                        validates and modifies your data in real-time based on
                        your schema definitions.
                    </CardDescription>
                </CardContent>
            </Card>

            <!-- Feature Card 2 -->
            <Card class="rounded-xl shadow-none">
                <CardHeader class="pb-2">
                    <CardTitle class="text-base font-semibold"
                        >Temporal Routing</CardTitle
                    >
                </CardHeader>
                <CardContent>
                    <CardDescription class="text-sm">
                        Laws change. Your code shouldn't break. Version your
                        rules based on effective dates to handle regulatory
                        changes effortlessly.
                    </CardDescription>
                </CardContent>
            </Card>

            <!-- Feature Card 3 -->
            <Card class="rounded-xl shadow-none">
                <CardHeader class="pb-2">
                    <CardTitle class="text-base font-semibold"
                        >Attestations</CardTitle
                    >
                </CardHeader>
                <CardContent>
                    <CardDescription class="text-sm">
                        Cryptographic signatures with audit trails. Every state
                        change is traceable to a specific rule version and
                        signer.
                    </CardDescription>
                </CardContent>
            </Card>

            <!-- Feature Card 4 -->
            <Card class="rounded-xl shadow-none">
                <CardHeader class="pb-2">
                    <CardTitle class="text-base font-semibold"
                        >Verification</CardTitle
                    >
                </CardHeader>
                <CardContent>
                    <CardDescription class="text-sm">
                        Cryptographically verify that document transformations
                        adhered to the rules at any point in history.
                    </CardDescription>
                </CardContent>
            </Card>

            <!-- Feature Card 5 -->
            <Card class="rounded-xl shadow-none">
                <CardHeader class="pb-2">
                    <CardTitle class="text-base font-semibold"
                        >Cross-Platform</CardTitle
                    >
                </CardHeader>
                <CardContent>
                    <CardDescription class="text-sm">
                        Runs natively in Go, or as a WASM module in browsers and
                        Node.js. Same logic, everywhere.
                    </CardDescription>
                </CardContent>
            </Card>

            <!-- Feature Card 6 -->
            <Card class="rounded-xl shadow-none">
                <CardHeader class="pb-2">
                    <CardTitle class="text-base font-semibold"
                        >Open Source</CardTitle
                    >
                </CardHeader>
                <CardContent>
                    <CardDescription class="text-sm">
                        MIT licensed. Transparent, auditable, and
                        community-driven. Build on top of it or customize it to
                        your needs.
                    </CardDescription>
                </CardContent>
            </Card>
        </div>
    </div>
</section>

<!-- CTA Section -->
<section class="border-t py-12 md:py-16 lg:py-20">
    <div class="container mx-auto flex flex-col items-center text-center gap-4">
        <h2 class="text-2xl font-semibold tracking-tight sm:text-3xl">
            Ready to get started?
        </h2>
        <p class="text-muted-foreground max-w-xl">
            Install Tenet and start encoding your business logic in minutes.
        </p>
        <div class="flex items-center gap-3 pt-2">
            <Button href="/docs/introduction" class="h-9 px-4">
                Get Started
            </Button>
            <Button
                variant="outline"
                href="https://github.com/dlovans/tenet"
                target="_blank"
                class="h-9 px-4"
            >
                <Github class="mr-2 h-4 w-4" />
                GitHub
            </Button>
        </div>
    </div>
</section>
