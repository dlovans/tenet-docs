<script>
    import { cn } from "$lib/utils";
    let { class: className, level = "h3", children, ...rest } = $props();
</script>

<div
    role="heading"
    aria-level="3"
    class={cn("text-2xl font-semibold leading-none tracking-tight", className)}
    {...rest}
>
    {@render children?.()}
</div>
