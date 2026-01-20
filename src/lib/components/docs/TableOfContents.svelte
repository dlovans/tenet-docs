<script>
    import { page } from "$app/stores";
    import { onMount, tick } from "svelte";

    let headings = $state([]);
    let activeId = $state("");
    let observer = null;

    // Re-extract headings when the page URL changes
    $effect(() => {
        // Track the pathname to trigger re-extraction on navigation
        const currentPath = $page.url.pathname;

        // Use tick to wait for DOM to update after navigation
        tick().then(() => {
            extractHeadings();
        });
    });

    function extractHeadings() {
        // Clean up old observer
        if (observer) {
            observer.disconnect();
        }

        // Extract headings from the article
        const article = document.querySelector("article");
        if (article) {
            const h2s = article.querySelectorAll("h2, h3");
            const idCounts = {};

            headings = Array.from(h2s).map((h, i) => {
                // Generate a base ID from the text
                let baseId = h.textContent
                    .toLowerCase()
                    .replace(/\s+/g, "-")
                    .replace(/[^\w-]/g, "");

                // Track duplicates and make IDs unique
                if (idCounts[baseId] === undefined) {
                    idCounts[baseId] = 0;
                } else {
                    idCounts[baseId]++;
                }

                // Create unique ID by appending count if duplicate
                const uniqueId =
                    idCounts[baseId] === 0
                        ? baseId
                        : `${baseId}-${idCounts[baseId]}`;

                // Update the heading's ID in the DOM
                h.id = uniqueId;

                return {
                    id: uniqueId,
                    text: h.textContent,
                    level: h.tagName === "H2" ? 2 : 3,
                };
            });

            // Set up intersection observer for active tracking
            observer = new IntersectionObserver(
                (entries) => {
                    entries.forEach((entry) => {
                        if (entry.isIntersecting) {
                            activeId = entry.target.id;
                        }
                    });
                },
                { rootMargin: "-80px 0px -80% 0px" },
            );

            headings.forEach((heading) => {
                const el = document.getElementById(heading.id);
                if (el) observer.observe(el);
            });
        } else {
            headings = [];
        }
    }

    onMount(() => {
        return () => {
            if (observer) observer.disconnect();
        };
    });

    function scrollToHeading(id) {
        const el = document.getElementById(id);
        if (el) {
            el.scrollIntoView({ behavior: "smooth", block: "start" });
        }
    }
</script>

{#if headings.length > 0}
    <div>
        <p class="font-medium text-sm mb-4">On This Page</p>
        <nav class="space-y-1">
            {#each headings as heading}
                <button
                    onclick={() => scrollToHeading(heading.id)}
                    class="block text-left w-full text-[0.8rem] leading-relaxed transition-colors hover:text-foreground {heading.level ===
                    3
                        ? 'pl-3'
                        : ''} {activeId === heading.id
                        ? 'text-foreground font-medium'
                        : 'text-muted-foreground'}"
                >
                    {heading.text}
                </button>
            {/each}
        </nav>
    </div>
{/if}
