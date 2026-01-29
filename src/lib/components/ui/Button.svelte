<script>
  import { cva } from "class-variance-authority";
  import { cn } from "$lib/utils";

  let {
    class: className,
    variant = "default",
    size = "default",
    children,
    onclick,
    href = undefined,
    ...rest
  } = $props();

  // Neubrutalist button variants with offset shadows
  const buttonVariants = cva(
    "inline-flex items-center justify-center gap-2 whitespace-nowrap text-sm font-medium font-mono transition-all duration-150 focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring disabled:pointer-events-none disabled:opacity-50 [&_svg]:pointer-events-none [&_svg]:size-4 [&_svg]:shrink-0 border-2 border-border rounded-lg",
    {
      variants: {
        variant: {
          default:
            "bg-accent text-accent-foreground shadow-[3px_3px_0_0_var(--shadow)] hover:shadow-[4px_4px_0_0_var(--shadow)] hover:-translate-x-px hover:-translate-y-px active:shadow-[1px_1px_0_0_var(--shadow)] active:translate-x-0.5 active:translate-y-0.5",
          secondary:
            "bg-card text-primary shadow-[3px_3px_0_0_var(--shadow)] hover:shadow-[4px_4px_0_0_var(--shadow)] hover:-translate-x-px hover:-translate-y-px active:shadow-[1px_1px_0_0_var(--shadow)] active:translate-x-0.5 active:translate-y-0.5",
          destructive:
            "bg-destructive text-destructive-foreground shadow-[3px_3px_0_0_var(--shadow)] hover:shadow-[4px_4px_0_0_var(--shadow)] hover:-translate-x-px hover:-translate-y-px active:shadow-[1px_1px_0_0_var(--shadow)] active:translate-x-0.5 active:translate-y-0.5",
          outline:
            "bg-background text-foreground shadow-[2px_2px_0_0_var(--shadow)] hover:shadow-[3px_3px_0_0_var(--shadow)] hover:-translate-x-px hover:-translate-y-px",
          ghost:
            "border-transparent hover:bg-secondary hover:text-secondary-foreground shadow-none",
          link:
            "text-primary underline-offset-4 hover:underline border-transparent shadow-none",
        },
        size: {
          default: "h-10 px-5 py-2",
          sm: "h-8 px-3 text-xs",
          lg: "h-12 px-8 text-base",
          icon: "h-10 w-10",
        },
      },
      defaultVariants: {
        variant: "default",
        size: "default",
      },
    },
  );
</script>

{#if href}
  <a {href} class={cn(buttonVariants({ variant, size, className }))} {...rest}>
    {@render children?.()}
  </a>
{:else}
  <button
    class={cn(buttonVariants({ variant, size, className }))}
    {onclick}
    {...rest}
  >
    {@render children?.()}
  </button>
{/if}
