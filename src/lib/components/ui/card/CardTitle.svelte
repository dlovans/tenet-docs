<script>
    import { cn } from "$lib/utils";
    let { class: className, level = "h3", children, ...rest } = $props();
</script>

<div
    role="heading"
    aria-level="3"
    class={cn("font-mono text-xl font-semibold leading-none tracking-tight text-primary", className)}
    {...rest}
>
    {@render children?.()}
</div>
