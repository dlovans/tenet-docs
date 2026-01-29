<script>
    import { cn } from "$lib/utils";
    let { class: className, children, ...rest } = $props();
</script>

<div
    class={cn(
        "rounded-lg bg-card text-card-foreground border-2 border-border shadow-[3px_3px_0_0_var(--shadow)] transition-all duration-150 hover:shadow-[4px_4px_0_0_var(--shadow)] hover:-translate-x-px hover:-translate-y-px",
        className
    )}
    {...rest}
>
    {@render children?.()}
</div>
