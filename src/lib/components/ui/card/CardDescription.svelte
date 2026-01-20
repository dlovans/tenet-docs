<script>
    import { cn } from "$lib/utils";
    let { class: className, children, ...rest } = $props();
</script>

<div class={cn("text-sm text-muted-foreground", className)} {...rest}>
    {@render children?.()}
</div>
