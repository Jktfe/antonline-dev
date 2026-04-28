<script lang="ts">
  const GITHUB = 'https://github.com/Jktfe/a-nice-terminal';

  const features = [
    {
      icon: '⟳',
      title: 'Agent Terminals',
      desc: 'Run Claude, Codex, Gemini, Copilot, Qwen, Pi, or any shell CLI in persistent PTY sessions.',
    },
    {
      icon: '◌',
      title: 'Chat Rooms',
      desc: 'Give humans and agents a shared room for decisions, updates, questions, and handoffs.',
    },
    {
      icon: '⇄',
      title: 'Room Links',
      desc: 'Create focused discussion rooms, link existing rooms, and keep the main room readable.',
    },
    {
      icon: '✓',
      title: 'Prompt Bridge',
      desc: 'Surface approvals, selections, and needs-input events in chat instead of hiding them in TUIs.',
    },
    {
      icon: '⌁',
      title: 'Working Signals',
      desc: 'Track agent activity from real terminal output so busy CLIs do not look idle.',
    },
    {
      icon: '⌕',
      title: 'Evidence Trails',
      desc: 'Search messages, transcripts, run events, tasks, file refs, and room history from one place.',
    },
  ];

  const workflows = [
    {
      title: 'Start the work',
      desc: 'Create a terminal or chat room, launch the CLI you want, and ANT links the terminal to a private chat automatically.',
    },
    {
      title: 'Coordinate the agents',
      desc: 'Mention participants, route prompts, split noisy topics into linked discussions, and keep replies scoped to the right room.',
    },
    {
      title: 'Review the evidence',
      desc: 'Use messages, run events, terminal history, tasks, file refs, and read receipts as the operational record.',
    },
  ];
</script>

<svelte:head>
  <title>ANT — A Nice Terminal</title>
</svelte:head>

<!-- NAV -->
<header>
  <nav>
    <a href="/" class="logo">
      <svg viewBox="0 0 24 24" width="20" height="20" fill="none">
        <path d="M5 9 L11 12 L5 15" stroke="#6366F1" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
        <rect x="13" y="15" width="6" height="2" rx="1" fill="#22C55E"/>
      </svg>
      <span>ANT</span>
    </a>
    <div class="nav-links">
      <a href="/docs">Docs</a>
      <a href={GITHUB} target="_blank" rel="noopener" class="nav-github">
        <svg viewBox="0 0 24 24" width="16" height="16" fill="currentColor">
          <path d="M12 2C6.477 2 2 6.484 2 12.017c0 4.425 2.865 8.18 6.839 9.504.5.092.682-.217.682-.483 0-.237-.008-.868-.013-1.703-2.782.605-3.369-1.343-3.369-1.343-.454-1.158-1.11-1.466-1.11-1.466-.908-.62.069-.608.069-.608 1.003.07 1.531 1.032 1.531 1.032.892 1.53 2.341 1.088 2.91.832.092-.647.35-1.088.636-1.338-2.22-.253-4.555-1.113-4.555-4.951 0-1.093.39-1.988 1.029-2.688-.103-.253-.446-1.272.098-2.65 0 0 .84-.27 2.75 1.026A9.564 9.564 0 0112 6.844c.85.004 1.705.115 2.504.337 1.909-1.296 2.747-1.027 2.747-1.027.546 1.379.202 2.398.1 2.651.64.7 1.028 1.595 1.028 2.688 0 3.848-2.339 4.695-4.566 4.943.359.309.678.92.678 1.855 0 1.338-.012 2.419-.012 2.747 0 .268.18.58.688.482A10.019 10.019 0 0022 12.017C22 6.484 17.522 2 12 2z"/>
        </svg>
        GitHub
      </a>
    </div>
  </nav>
</header>

<!-- HERO -->
<section class="hero dot-grid">
  <div class="hero-inner">
    <!-- Text side -->
    <div class="hero-text">
      <div class="hero-badge fade-up" style="animation-delay:0.05s">
        <span class="badge-dot"></span>
        v3 · agent coordination
      </div>
      <h1 class="hero-heading fade-up" style="animation-delay:0.12s">
        ANT<br/>coordinates<br/>AI work.
      </h1>
      <p class="hero-sub fade-up" style="animation-delay:0.2s">
        Self-hosted control room for Claude, Codex, Gemini, Copilot, local CLIs,
        and the humans steering them.
      </p>
      <div class="hero-ctas fade-up" style="animation-delay:0.28s">
        <a href="/docs" class="cta-primary">Get started →</a>
        <a href={GITHUB} target="_blank" rel="noopener" class="cta-secondary">GitHub ↗</a>
      </div>
      <div class="hero-tags fade-up" style="animation-delay:0.36s">
        <span>Rooms</span><span>PTYs</span><span>Prompt bridge</span><span>Room links</span><span>SQLite</span>
      </div>
    </div>

    <!-- Terminal window -->
    <div class="terminal-window fade-up" style="animation-delay:0.2s">
      <div class="term-chrome">
        <div class="term-dots">
          <span style="background:#EF4444"></span>
          <span style="background:#F59E0B"></span>
          <span style="background:#22C55E"></span>
        </div>
        <span class="term-title">ant control room</span>
      </div>
      <div class="term-body">
        <div class="term-line" style="animation-delay:0.6s">
          <span class="t-prompt">~</span><span class="t-sym"> $ </span><span class="t-cmd">ant sessions</span>
        </div>
        <div class="term-line" style="animation-delay:0.9s">
          <span class="t-dim">  NAME              TYPE      STATUS</span>
        </div>
        <div class="term-line" style="animation-delay:1.05s">
          <span class="t-green">  ●</span><span class="t-out"> antcodex       </span><span class="t-dim">terminal  working</span>
        </div>
        <div class="term-line" style="animation-delay:1.2s">
          <span class="t-green">  ●</span><span class="t-out"> decisions      </span><span class="t-dim">chat      active</span>
        </div>
        <div class="term-line" style="animation-delay:1.35s">
          <span class="t-muted">  ·</span><span class="t-out"> vera-ai         </span><span class="t-dim">chat      linked</span>
        </div>
        <div class="term-line" style="animation-delay:1.6s">&nbsp;</div>
        <div class="term-line" style="animation-delay:1.8s">
          <span class="t-prompt">~</span><span class="t-sym"> $ </span><span class="t-cmd">ant msg decisions "@codex test"</span>
        </div>
        <div class="term-line" style="animation-delay:2.3s">
          <span class="t-dim">  routed to linked terminal chat</span>
        </div>
        <div class="term-line" style="animation-delay:2.9s">
          <span class="t-green">  ✓</span><span class="t-dim"> needs-input cleared with evidence</span>
        </div>
        <div class="term-line" style="animation-delay:3.1s">
          <span class="t-out">antcodex</span><span class="t-sym"> $ </span><span class="term-cursor"></span>
        </div>
      </div>
    </div>
  </div>

  <div class="hero-rule">
    <span>Runs on your machine &mdash; coordinates agents, rooms, prompts, and evidence</span>
  </div>
</section>

<!-- FEATURES -->
<section class="features-section">
  <div class="section-inner">
    <h2 class="section-heading">More than terminal multiplexing</h2>
    <div class="features-grid">
      {#each features as f, i}
        <div class="feature-card" style="animation-delay:{0.05 * i}s">
          <div class="feature-icon">{f.icon}</div>
          <h3 class="feature-title">{f.title}</h3>
          <p class="feature-desc">{f.desc}</p>
        </div>
      {/each}
    </div>
  </div>
</section>

<!-- WORKFLOW -->
<section class="workflow-section">
  <div class="section-inner">
    <h2 class="section-heading">A shared operating surface</h2>
    <div class="workflow-grid">
      {#each workflows as step, i}
        <div class="workflow-item">
          <div class="workflow-index">{i + 1}</div>
          <h3>{step.title}</h3>
          <p>{step.desc}</p>
        </div>
      {/each}
    </div>
  </div>
</section>

<!-- INSTALL -->
<section class="install-section">
  <div class="section-inner">
    <h2 class="section-heading">Run it on hardware you control</h2>
    <div class="install-grid">
      <div>
        <p class="install-label">Server</p>
        <div class="code-block">
          <pre><span class="line-comment"># clone and run</span>
<span class="line-cmd">git clone https://github.com/Jktfe/a-nice-terminal</span>
<span class="line-cmd">cd a-nice-terminal && npm install</span>
<span class="line-cmd">cp .env.example .env</span>
<span class="line-cmd">npm run build</span>
<span class="line-cmd">npm run start</span>
<span class="line-out">  ANT v3 running at http://0.0.0.0:6458</span></pre>
        </div>
      </div>
      <div>
        <p class="install-label">CLI (on any machine)</p>
        <div class="code-block">
          <pre><span class="line-comment"># point the ant CLI at your server</span>
<span class="line-cmd">ant config set --url https://your-host:6458</span>
<span class="line-cmd">ant sessions</span>
<span class="line-cmd">ant chat join &lt;room-id&gt;</span>
<span class="line-cmd">ant terminal history &lt;session-id&gt;</span></pre>
        </div>
      </div>
    </div>
    <div class="install-cta">
      <a href="/docs" class="cta-primary">Full setup guide →</a>
    </div>
  </div>
</section>

<!-- FOOTER -->
<footer>
  <div class="footer-inner">
    <a href="/" class="logo">
      <svg viewBox="0 0 24 24" width="16" height="16" fill="none">
        <path d="M5 9 L11 12 L5 15" stroke="#6366F1" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
        <rect x="13" y="15" width="6" height="2" rx="1" fill="#22C55E"/>
      </svg>
      ANT
    </a>
    <div class="footer-links">
      <a href="/docs">Docs</a>
      <a href="/docs/api">API</a>
      <a href="/docs/cli">CLI</a>
      <a href={GITHUB} target="_blank" rel="noopener">GitHub</a>
    </div>
    <p class="footer-copy">Open source. MIT licence.</p>
  </div>
</footer>

<style>
  /* NAV */
  header {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    z-index: 100;
    backdrop-filter: blur(12px);
    -webkit-backdrop-filter: blur(12px);
    background: rgba(7, 11, 18, 0.8);
    border-bottom: 1px solid var(--border);
  }

  nav {
    max-width: 1100px;
    margin: 0 auto;
    padding: 0 1.5rem;
    height: 60px;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  .logo {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    font-family: var(--font-display);
    font-weight: 700;
    font-size: 1.1rem;
    color: var(--text);
    text-decoration: none;
    letter-spacing: 0;
  }

  .nav-links {
    display: flex;
    align-items: center;
    gap: 1.5rem;
  }

  .nav-links a {
    font-size: 0.9rem;
    color: var(--muted);
    text-decoration: none;
    transition: color 0.15s;
  }
  .nav-links a:hover { color: var(--text); }

  .nav-github {
    display: flex;
    align-items: center;
    gap: 0.4rem;
    padding: 0.35rem 0.85rem;
    border: 1px solid var(--border);
    border-radius: 6px;
    font-size: 0.82rem !important;
    transition: border-color 0.15s !important;
  }
  .nav-github:hover { border-color: rgba(255,255,255,0.2) !important; }

  /* HERO */
  .hero {
    min-height: 84vh;
    padding-top: 60px;
    display: flex;
    flex-direction: column;
    position: relative;
    overflow: hidden;
  }

  .hero::before {
    content: '';
    position: absolute;
    inset: 0;
    background: radial-gradient(ellipse 80% 60% at 60% 40%, rgba(99,102,241,0.07) 0%, transparent 70%);
    pointer-events: none;
  }

  .hero-inner {
    flex: 1;
    max-width: 1100px;
    margin: 0 auto;
    padding: 4rem 1.5rem 3rem;
    display: grid;
    grid-template-columns: minmax(0, 1fr) minmax(360px, 0.9fr);
    gap: 3rem;
    align-items: center;
  }

  .hero-text {
    min-width: 0;
  }

  .hero-badge {
    display: inline-flex;
    align-items: center;
    gap: 0.5rem;
    padding: 0.3rem 0.75rem;
    border-radius: 999px;
    border: 1px solid rgba(99,102,241,0.25);
    background: rgba(99,102,241,0.07);
    font-size: 0.78rem;
    font-family: var(--font-mono);
    color: #94A3B8;
    margin-bottom: 1.5rem;
    width: fit-content;
  }

  .badge-dot {
    width: 6px;
    height: 6px;
    border-radius: 50%;
    background: var(--green);
    box-shadow: 0 0 6px var(--green);
  }

  .hero-heading {
    font-family: var(--font-display);
    font-size: 4.35rem;
    font-weight: 800;
    line-height: 1.0;
    letter-spacing: 0;
    color: var(--text);
    margin-bottom: 1.25rem;
  }

  .hero-sub {
    font-size: 1.1rem;
    line-height: 1.65;
    color: var(--muted);
    margin-bottom: 2rem;
  }

  .hero-ctas {
    display: flex;
    gap: 0.75rem;
    flex-wrap: wrap;
    margin-bottom: 2rem;
  }

  .cta-primary {
    padding: 0.65rem 1.25rem;
    border-radius: 8px;
    background: var(--indigo);
    color: #fff;
    font-size: 0.9rem;
    font-weight: 500;
    text-decoration: none;
    transition: opacity 0.15s, transform 0.15s;
  }
  .cta-primary:hover { opacity: 0.9; transform: translateY(-1px); }

  .cta-secondary {
    padding: 0.65rem 1.25rem;
    border-radius: 8px;
    border: 1px solid var(--border-accent);
    color: var(--text);
    font-size: 0.9rem;
    font-weight: 500;
    text-decoration: none;
    transition: border-color 0.15s, background 0.15s;
  }
  .cta-secondary:hover { border-color: rgba(99,102,241,0.5); background: rgba(99,102,241,0.05); }

  .hero-tags {
    display: flex;
    gap: 0.5rem;
    flex-wrap: wrap;
  }
  .hero-tags span {
    padding: 0.2rem 0.6rem;
    border-radius: 4px;
    background: rgba(255,255,255,0.04);
    border: 1px solid var(--border);
    font-size: 0.75rem;
    font-family: var(--font-mono);
    color: var(--muted);
  }

  /* Terminal window */
  .terminal-window {
    width: 100%;
    min-width: 0;
    border-radius: 12px;
    border: 1px solid rgba(255,255,255,0.1);
    overflow: hidden;
    box-shadow: 0 24px 60px rgba(0, 0, 0, 0.6), 0 0 0 1px rgba(99,102,241,0.1);
    background: #050810;
  }

  .term-chrome {
    display: flex;
    align-items: center;
    gap: 0.75rem;
    padding: 0.65rem 1rem;
    background: #0A0F18;
    border-bottom: 1px solid rgba(255,255,255,0.06);
  }

  .term-dots {
    display: flex;
    gap: 0.375rem;
  }
  .term-dots span {
    width: 10px;
    height: 10px;
    border-radius: 50%;
    opacity: 0.8;
  }

  .term-title {
    font-family: var(--font-mono);
    font-size: 0.72rem;
    color: #475569;
    flex: 1;
    text-align: center;
  }

  .term-body {
    padding: 1.25rem 1.25rem 1.5rem;
    font-family: var(--font-mono);
    font-size: 0.82rem;
    line-height: 1.8;
  }

  .term-line {
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }

  .t-prompt { color: #475569; }
  .t-sym    { color: #64748B; }
  .t-cmd    { color: var(--green); }
  .t-out    { color: #CBD5E1; }
  .t-dim    { color: #475569; }
  .t-muted  { color: #334155; }
  .t-green  { color: var(--green); }

  .hero-rule {
    text-align: center;
    padding: 1.5rem;
    border-top: 1px solid var(--border);
  }
  .hero-rule span {
    font-size: 0.82rem;
    color: var(--muted);
    font-family: var(--font-mono);
    letter-spacing: 0.02em;
  }

  /* FEATURES */
  .features-section {
    padding: 6rem 1.5rem;
    border-top: 1px solid var(--border);
  }

  .section-inner { max-width: 1100px; margin: 0 auto; }

  .section-heading {
    font-family: var(--font-display);
    font-size: 1.75rem;
    font-weight: 700;
    letter-spacing: 0;
    margin-bottom: 2.5rem;
    color: var(--text);
  }

  .features-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 1px;
    background: var(--border);
    border: 1px solid var(--border);
    border-radius: 12px;
    overflow: hidden;
  }

  .feature-card {
    padding: 1.75rem;
    background: var(--bg);
    transition: background 0.2s;
  }
  .feature-card:hover { background: var(--bg-card); }

  .feature-icon {
    font-size: 1.25rem;
    margin-bottom: 0.875rem;
    color: var(--indigo);
  }

  .feature-title {
    font-family: var(--font-display);
    font-size: 0.95rem;
    font-weight: 600;
    color: var(--text);
    margin-bottom: 0.5rem;
    letter-spacing: 0;
  }

  .feature-desc {
    font-size: 0.85rem;
    color: var(--muted);
    line-height: 1.65;
  }

  /* WORKFLOW */
  .workflow-section {
    padding: 6rem 1.5rem;
    border-top: 1px solid var(--border);
  }

  .workflow-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 1rem;
  }

  .workflow-item {
    border: 1px solid var(--border);
    border-radius: 8px;
    background: var(--bg-card);
    padding: 1.5rem;
  }

  .workflow-index {
    width: 2rem;
    height: 2rem;
    display: grid;
    place-items: center;
    border: 1px solid var(--border-accent);
    border-radius: 6px;
    color: var(--green);
    font-family: var(--font-mono);
    font-size: 0.8rem;
    margin-bottom: 1rem;
  }

  .workflow-item h3 {
    font-family: var(--font-display);
    font-size: 1rem;
    font-weight: 600;
    color: var(--text);
    margin-bottom: 0.5rem;
  }

  .workflow-item p {
    font-size: 0.86rem;
    color: var(--muted);
    line-height: 1.65;
  }

  /* INSTALL */
  .install-section {
    padding: 6rem 1.5rem;
    border-top: 1px solid var(--border);
    background: var(--bg-2);
  }

  .install-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 1.5rem;
    margin-bottom: 2rem;
  }

  .install-grid > div {
    min-width: 0;
  }

  .install-label {
    font-size: 0.78rem;
    font-family: var(--font-mono);
    color: var(--muted);
    margin-bottom: 0.625rem;
    letter-spacing: 0.05em;
    text-transform: uppercase;
  }

  .install-cta { text-align: center; }

  .code-block {
    padding: 1.25rem 1.5rem;
    max-width: 100%;
  }
  .code-block pre {
    margin: 0;
    font-size: 0.82rem;
    line-height: 1.75;
    white-space: pre;
    overflow-x: auto;
  }

  /* FOOTER */
  footer {
    border-top: 1px solid var(--border);
    padding: 2rem 1.5rem;
  }

  .footer-inner {
    max-width: 1100px;
    margin: 0 auto;
    display: flex;
    align-items: center;
    gap: 2rem;
    flex-wrap: wrap;
  }

  .footer-links {
    display: flex;
    gap: 1.25rem;
    flex: 1;
  }
  .footer-links a {
    font-size: 0.85rem;
    color: var(--muted);
    text-decoration: none;
    transition: color 0.15s;
  }
  .footer-links a:hover { color: var(--text); }

  .footer-copy {
    font-size: 0.8rem;
    color: #334155;
  }

  /* Responsive */
  @media (max-width: 768px) {
    .hero {
      min-height: 82vh;
    }
    .hero-inner {
      grid-template-columns: 1fr;
      padding: 3rem 1rem 2rem;
      width: 100%;
      max-width: 100%;
    }
    .hero-text {
      min-width: 0;
      max-width: 100%;
    }
    .hero-heading {
      font-size: 2.45rem;
      line-height: 1.08;
    }
    .hero-sub {
      font-size: 1rem;
    }
    .features-grid { grid-template-columns: 1fr; }
    .workflow-grid { grid-template-columns: 1fr; }
    .install-grid  { grid-template-columns: 1fr; }
    .terminal-window { display: none; }
  }
</style>
