<script>
    import Button from "$lib/components/ui/Button.svelte";
    import { Github } from "lucide-svelte";
</script>

<header
    class="sticky top-0 z-50 w-full border-b bg-background/95 backdrop-blur supports-[backdrop-filter]:bg-background/60"
>
    <div
        class="mx-auto max-w-screen-2xl flex h-14 items-center px-4 sm:px-6 lg:px-8"
    >
        <!-- Logo -->
        <div class="mr-4 hidden md:flex">
            <a href="/" class="mr-6 flex items-center space-x-2">
                <span class="font-semibold">Tenet</span>
            </a>
            <nav class="flex items-center gap-4 text-sm">
                <a
                    href="/docs"
                    class="text-muted-foreground transition-colors hover:text-foreground"
                    >Docs</a
                >
                <a
                    href="/playground"
                    class="text-muted-foreground transition-colors hover:text-foreground"
                    >Playground</a
                >
            </nav>
        </div>

        <!-- Mobile Logo -->
        <a href="/" class="mr-4 flex items-center space-x-2 md:hidden">
            <span class="font-semibold">Tenet</span>
        </a>

        <!-- Right Side -->
        <div class="flex flex-1 items-center justify-end gap-2">
            <nav class="flex items-center gap-1">
                <Button
                    variant="ghost"
                    size="icon"
                    href="https://github.com/dlovans/tenet"
                    target="_blank"
                    class="h-8 w-8"
                >
                    <Github class="h-4 w-4" />
                    <span class="sr-only">GitHub</span>
                </Button>
            </nav>
        </div>
    </div>
</header>
