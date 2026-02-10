---
title: "Search"
layout: "search"
---

<!-- Pagefind UI Styles -->
<link href="/_pagefind/pagefind-ui.css" rel="stylesheet">
<script src="/_pagefind/pagefind-ui.js"></script>

<div id="search" class="pro-search"></div>

<script>
    window.addEventListener('DOMContentLoaded', (event) => {
        new PagefindUI({ 
            element: "#search", 
            showSubResults: true,
            pageSize: 5,
            translations: {
                placeholder: "Search Formal Syntax..."
            }
        });
    });
</script>

<style>
    /* Professional Overrides for Pagefind */
    .pro-search {
        --pagefind-ui-primary: var(--primary);
        --pagefind-ui-text: var(--primary);
        --pagefind-ui-background: var(--theme);
        --pagefind-ui-border: var(--tertiary);
        --pagefind-ui-font: "JetBrains Mono", monospace;
    }
</style>