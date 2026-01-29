<script>
    let { class: className } = $props();
</script>

<footer class="border-t-2 border-border py-8 md:px-8">
    <div
        class="container flex flex-col items-center justify-between gap-4 md:h-16 md:flex-row"
    >
        <p
            class="text-balance text-center text-sm leading-loose text-muted-foreground md:text-left font-mono"
        >
            Built by <a
                href="https://github.com/dlovan"
                target="_blank"
                rel="noreferrer"
                class="font-medium text-primary hover:text-accent transition-colors">Dlovan</a
            >. The source code is available on
            <a
                href="https://github.com/dlovans/tenet"
                target="_blank"
                rel="noreferrer"
                class="font-medium text-primary hover:text-accent transition-colors">GitHub</a
            >.
        </p>
    </div>
</footer>
