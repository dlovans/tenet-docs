<script>
    import Sidebar from "$lib/components/docs/Sidebar.svelte";
    import TableOfContents from "$lib/components/docs/TableOfContents.svelte";

    let { children } = $props();
</script>

<div class="mx-auto max-w-screen-2xl px-4 sm:px-6 lg:px-8">
    <div class="flex gap-8 lg:gap-16">
        <!-- Left Sidebar -->
        <aside class="hidden md:block w-[200px] shrink-0">
            <div
                class="sticky top-14 h-[calc(100vh-3.5rem)] overflow-y-auto py-8 pr-8"
            >
                <Sidebar />
            </div>
        </aside>

        <!-- Main Content -->
        <main class="flex-1 min-w-0 py-8">
            <article class="mx-auto w-full min-w-0 max-w-3xl px-4">
                <div
                    class="prose prose-zinc dark:prose-invert max-w-none prose-headings:scroll-m-20 prose-headings:tracking-tight prose-h1:text-3xl prose-h1:font-semibold prose-h2:text-2xl prose-h2:font-semibold prose-h2:border-b prose-h2:pb-2 prose-h2:mt-10 prose-p:leading-7 prose-a:font-medium prose-a:underline prose-a:underline-offset-4"
                >
                    {@render children()}
                </div>
            </article>
        </main>

        <!-- Right Sidebar (Table of Contents) -->
        <aside class="hidden xl:block w-[200px] shrink-0">
            <div
                class="sticky top-14 h-[calc(100vh-3.5rem)] overflow-y-auto py-8 pl-8"
            >
                <TableOfContents />
            </div>
        </aside>
    </div>
</div>
