<script>
    import { page } from "$app/stores";
    import { cn } from "$lib/utils";

    const sidebarNavItems = [
        {
            title: "Getting Started",
            items: [{ title: "Introduction", href: "/docs/introduction" }],
        },
        {
            title: "Core Concepts",
            items: [
                { title: "Schema Reference", href: "/docs/schema-reference" },
                { title: "Operators", href: "/docs/operators" },
                { title: "Temporal Routing", href: "/docs/temporal-routing" },
                { title: "Verification", href: "/docs/verification" },
                { title: "Errors", href: "/docs/errors" },
            ],
        },
        {
            title: "Developers",
            items: [
                { title: "API Reference", href: "/docs/api-reference" },
                { title: "CLI Reference", href: "/docs/cli-reference" },
                { title: "Performance", href: "/docs/performance" },
            ],
        },
        {
            title: "Examples",
            items: [{ title: "Use Cases", href: "/docs/examples" }],
        },
    ];
</script>

<div class="w-full">
    {#each sidebarNavItems as group, i}
        <div class={cn("pb-4", i !== 0 && "pt-4")}>
            <h4 class="mb-2 px-3 py-1 text-xs font-mono font-bold uppercase tracking-wider text-primary">
                {group.title}
            </h4>
            {#if group.items?.length}
                <div class="grid grid-flow-row auto-rows-max text-sm">
                    {#each group.items as item}
                        <a
                            href={item.href}
                            class={cn(
                                "group flex w-full items-center px-3 py-2 text-muted-foreground hover:text-foreground hover:bg-secondary transition-all border-l-3 border-transparent",
                                $page.url.pathname === item.href &&
                                    "font-medium text-foreground bg-secondary border-l-3 border-accent",
                            )}
                        >
                            {item.title}
                        </a>
                    {/each}
                </div>
            {/if}
        </div>
    {/each}
</div>
