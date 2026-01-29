<script>
    import Button from "$lib/components/ui/Button.svelte";
    import { Github, Sun, Moon } from "lucide-svelte";
    import { browser } from "$app/environment";

    // Theme state: 'light', 'dark', or 'system'
    let theme = $state('system');

    // Initialize theme from localStorage
    if (browser) {
        const stored = localStorage.getItem('theme');
        if (stored === 'light' || stored === 'dark') {
            theme = stored;
            applyTheme(stored);
        }
    }

    function applyTheme(newTheme) {
        if (!browser) return;
        const root = document.documentElement;
        root.classList.remove('light', 'dark');
        if (newTheme !== 'system') {
            root.classList.add(newTheme);
        }
    }

    function toggleTheme() {
        // Cycle: system -> light -> dark -> system
        if (theme === 'system') {
            // Check current appearance to decide next
            const isDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
            theme = isDark ? 'light' : 'dark';
        } else if (theme === 'light') {
            theme = 'dark';
        } else {
            theme = 'system';
        }

        if (theme === 'system') {
            localStorage.removeItem('theme');
        } else {
            localStorage.setItem('theme', theme);
        }
        applyTheme(theme);
    }

    // Determine if currently showing dark mode
    let isDark = $derived.by(() => {
        if (!browser) return false;
        if (theme === 'dark') return true;
        if (theme === 'light') return false;
        return window.matchMedia('(prefers-color-scheme: dark)').matches;
    });
</script>

<header
    class="sticky top-0 z-50 w-full border-b-2 border-border bg-background/95 backdrop-blur supports-backdrop-filter:bg-background/60"
>
    <div
        class="mx-auto max-w-screen-2xl flex h-16 items-center px-4 sm:px-6 lg:px-8"
    >
        <!-- Logo -->
        <div class="mr-4 hidden md:flex">
            <a href="/" class="mr-8 flex items-center space-x-2 group">
                <span class="font-mono font-bold text-xl text-primary">Tenet</span>
            </a>
            <nav class="flex items-center gap-6 text-sm font-medium">
                <a
                    href="/docs"
                    class="text-muted-foreground transition-colors hover:text-primary relative after:absolute after:-bottom-1 after:left-0 after:h-0.5 after:w-0 after:bg-accent after:transition-all hover:after:w-full"
                    >Docs</a
                >
                <a
                    href="/playground"
                    class="text-muted-foreground transition-colors hover:text-primary relative after:absolute after:-bottom-1 after:left-0 after:h-0.5 after:w-0 after:bg-accent after:transition-all hover:after:w-full"
                    >Playground</a
                >
            </nav>
        </div>

        <!-- Mobile Logo -->
        <a href="/" class="mr-4 flex items-center space-x-2 md:hidden">
            <span class="font-mono font-bold text-xl text-primary">Tenet</span>
        </a>

        <!-- Right Side -->
        <div class="flex flex-1 items-center justify-end gap-2">
            <nav class="flex items-center gap-2">
                <!-- Theme Toggle -->
                <button
                    onclick={toggleTheme}
                    class="inline-flex items-center justify-center h-9 w-9 rounded-lg border-2 border-border bg-card text-foreground shadow-[2px_2px_0_0_var(--shadow)] hover:shadow-[3px_3px_0_0_var(--shadow)] hover:-translate-x-px hover:-translate-y-px active:shadow-[1px_1px_0_0_var(--shadow)] active:translate-x-0.5 active:translate-y-0.5 transition-all"
                    aria-label="Toggle theme"
                >
                    {#if isDark}
                        <Sun class="h-4 w-4" />
                    {:else}
                        <Moon class="h-4 w-4" />
                    {/if}
                </button>
                <Button
                    variant="outline"
                    size="sm"
                    href="https://github.com/dlovans/tenet"
                    target="_blank"
                    class="gap-2"
                >
                    <Github class="h-4 w-4" />
                    <span class="hidden sm:inline">GitHub</span>
                </Button>
            </nav>
        </div>
    </div>
</header>
