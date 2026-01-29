<script>
    import { cva } from "class-variance-authority";
    import { cn } from "$lib/utils";

    let { class: className, variant = "default", children, ...rest } = $props();

    // Neubrutalist badge variants with small offset shadows
    const badgeVariants = cva(
        "inline-flex items-center rounded-md border-2 border-border px-2.5 py-0.5 text-xs font-semibold font-mono shadow-[2px_2px_0_0_var(--shadow)] transition-colors focus:outline-none focus:ring-2 focus:ring-ring focus:ring-offset-2",
        {
            variants: {
                variant: {
                    default: "bg-accent text-accent-foreground",
                    secondary: "bg-secondary text-secondary-foreground",
                    destructive: "bg-destructive text-destructive-foreground",
                    outline: "bg-background text-foreground",
                    success: "bg-success text-success-foreground",
                    info: "bg-primary text-primary-foreground",
                },
            },
            defaultVariants: {
                variant: "default",
            },
        },
    );
</script>

<div class={cn(badgeVariants({ variant }), className)} {...rest}>
    {@render children?.()}
</div>
