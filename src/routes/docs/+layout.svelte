<script lang="ts">
  import { page } from '$app/stores';
  let { children } = $props();

  const nav = [
    { href: '/docs',              label: 'Getting Started' },
    { href: '/docs/config',       label: 'Configuration'   },
    { href: '/docs/cli',          label: 'CLI Reference'   },
    { href: '/docs/api',          label: 'REST & WebSocket API' },
    { href: '/docs/self-hosting', label: 'Self-Hosting'    },
  ];
</script>

<div class="docs-shell" style="min-height:100vh; background:var(--bg);">
  <!-- Top nav -->
  <nav style="position:sticky; top:0; z-index:50; background:rgba(7,11,18,0.85); backdrop-filter:blur(12px); border-bottom:1px solid var(--border);">
    <div style="max-width:1200px; margin:0 auto; padding:0 1.5rem; height:56px; display:flex; align-items:center; gap:2rem;">
      <a href="/" style="display:flex; align-items:center; gap:0.5rem; text-decoration:none;">
        <svg width="24" height="24" viewBox="0 0 32 32" fill="none">
          <rect width="32" height="32" rx="7" fill="#070B12"/>
          <path d="M8 12 L14 16 L8 20" stroke="#6366F1" stroke-width="2.5" fill="none" stroke-linecap="round" stroke-linejoin="round"/>
          <rect x="16" y="20" width="8" height="2.5" rx="1.25" fill="#22C55E"/>
        </svg>
        <span style="font-family:var(--font-display); font-weight:700; font-size:0.95rem; color:var(--text);">ANT</span>
      </a>
      <span style="color:var(--border); font-size:1.2rem;">/</span>
      <span style="font-size:0.875rem; color:var(--muted);">Docs</span>
      <div style="flex:1;"></div>
      <a href="https://github.com/Jktfe/a-nice-terminal" target="_blank" rel="noopener"
         style="font-size:0.8rem; color:var(--muted); text-decoration:none; transition:color 0.15s;"
         onmouseenter={(e)=>(e.currentTarget.style.color='var(--text)')}
         onmouseleave={(e)=>(e.currentTarget.style.color='var(--muted)')}>
        GitHub →
      </a>
    </div>
  </nav>

  <div class="docs-wrap" style="max-width:1200px; margin:0 auto; padding:2rem 1.5rem; display:flex; gap:3rem; align-items:flex-start;">
    <!-- Sidebar -->
    <aside class="docs-sidebar" style="width:200px; flex-shrink:0; position:sticky; top:80px;">
      <p style="font-family:var(--font-mono); font-size:0.7rem; text-transform:uppercase; letter-spacing:0.08em; color:var(--muted); margin-bottom:0.75rem;">Reference</p>
      <nav style="display:flex; flex-direction:column; gap:2px;">
        {#each nav as item}
          <a href={item.href}
             class="sidebar-link"
             class:active={$page.url.pathname === item.href}>
            {item.label}
          </a>
        {/each}
      </nav>

      <div style="margin-top:2.5rem; padding-top:1.5rem; border-top:1px solid var(--border);">
        <p style="font-family:var(--font-mono); font-size:0.7rem; text-transform:uppercase; letter-spacing:0.08em; color:var(--muted); margin-bottom:0.75rem;">Links</p>
        <a href="https://github.com/Jktfe/a-nice-terminal" target="_blank" rel="noopener" class="sidebar-link">GitHub</a>
        <a href="https://github.com/Jktfe/a-nice-terminal/issues" target="_blank" rel="noopener" class="sidebar-link">Issues</a>
        <a href="https://github.com/Jktfe/a-nice-terminal/releases" target="_blank" rel="noopener" class="sidebar-link">Releases</a>
      </div>
    </aside>

    <!-- Content -->
    <main class="docs-main" style="flex:1; min-width:0; max-width:720px;">
      {@render children()}
    </main>
  </div>
</div>

<style>
  @media (max-width: 800px) {
    .docs-wrap {
      display: block !important;
      padding: 1rem !important;
    }

    .docs-sidebar {
      width: 100% !important;
      position: static !important;
      margin-bottom: 1.5rem;
    }

    .docs-sidebar > nav {
      display: flex !important;
      flex-direction: row !important;
      gap: 0.35rem !important;
      overflow-x: auto;
      padding-bottom: 0.25rem;
    }

    .docs-sidebar > div {
      display: none;
    }

    .docs-main {
      max-width: 100% !important;
    }
  }
</style>
