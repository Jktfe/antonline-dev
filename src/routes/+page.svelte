<script lang="ts">
  const GITHUB = 'https://github.com/Jktfe/a-nice-terminal';

  const capabilities = [
    {
      kicker: 'Terminal + chat pairs',
      title: 'Every agent gets a terminal and a room.',
      desc: 'Run Claude, Codex, Gemini, Copilot, local Qwen/Pi models, or a normal shell. ANT keeps the PTY, linked chat, status, and history together.',
    },
    {
      kicker: 'Room Links',
      title: 'Split noisy work without losing context.',
      desc: 'Create focused discussions, link existing rooms, promote summaries, and keep decisions visible without flooding the main coordination room.',
    },
    {
      kicker: 'Prompt Bridge',
      title: 'Needs-input becomes a visible control surface.',
      desc: 'Approvals, selections, retries, text responses, and discarded events show up in chat instead of being buried inside an agent TUI.',
    },
    {
      kicker: 'Evidence',
      title: 'Work leaves a searchable trail.',
      desc: 'Messages, terminal history, run events, tasks, file refs, read receipts, digests, and exports all point back to what actually happened.',
    },
    {
      kicker: 'Agent Awareness',
      title: 'Working means real output, not guesswork.',
      desc: 'ANT tracks visible terminal activity so Codex-style CLIs do not look idle just because they lack a tidy status line.',
    },
    {
      kicker: 'Mobile',
      title: 'ANTios keeps the control room in your pocket.',
      desc: 'Quick triage, needs-input counters, chat-first navigation, thumb-friendly controls, and room lists for checking in away from the desk.',
    },
  ];

  const stats = [
    ['Terminals', 'PTYs with linked chats'],
    ['Rooms', 'Shared agent coordination'],
    ['Discussions', 'Linked focus spaces'],
    ['Evidence', 'Messages, tasks, files, runs'],
  ];
</script>

<svelte:head>
  <title>ANT - A Nice Terminal</title>
</svelte:head>

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
      <a href={GITHUB} target="_blank" rel="noopener" class="nav-github">GitHub</a>
    </div>
  </nav>
</header>

<section class="hero">
  <img class="hero-shot" src="/product/ant-web-room.png" alt="ANT room showing agent messages, participants, linked discussions, tasks, file references, and the agent shortcut bar" />
  <div class="hero-shade"></div>
  <div class="hero-content">
    <div class="hero-badge"><span></span> Live multi-agent control room</div>
    <h1>Your AI team needs somewhere to work.</h1>
    <p>
      ANT is the self-hosted operating surface for agent sessions: terminals,
      rooms, prompt cards, linked discussions, evidence trails, and mobile triage.
    </p>
    <div class="hero-actions">
      <a href="/docs" class="cta-primary">Get started</a>
      <a href={GITHUB} target="_blank" rel="noopener" class="cta-secondary">View source</a>
    </div>
    <div class="hero-tags">
      <span>Claude</span>
      <span>Codex</span>
      <span>Gemini</span>
      <span>Local models</span>
      <span>ANTios</span>
    </div>
  </div>
</section>

<section class="snapshot-band">
  <div class="section-inner">
    <div class="snapshot-copy">
      <p class="eyebrow">This is ANT</p>
      <h2>Not a terminal skin. A coordination layer.</h2>
      <p>
        The home screen shows the live operational graph: terminal agents on the
        left, chat rooms on the right, linked chats attached to every PTY, and
        status that follows real activity.
      </p>
    </div>
    <div class="product-frame wide">
      <img src="/product/ant-web-home.png" alt="ANT home screen showing terminal sessions, linked chats, chat rooms, and agent status" />
    </div>
  </div>
</section>

<section class="capabilities-section">
  <div class="section-inner">
    <div class="section-heading-row">
      <div>
        <p class="eyebrow">What it does</p>
        <h2>Built for actual agent work</h2>
      </div>
      <p>Everything here exists because multi-agent sessions get noisy, agents need steering, and finished work needs evidence.</p>
    </div>

    <div class="capability-grid">
      {#each capabilities as item}
        <article class="capability-card">
          <p>{item.kicker}</p>
          <h3>{item.title}</h3>
          <span>{item.desc}</span>
        </article>
      {/each}
    </div>
  </div>
</section>

<section class="room-section">
  <div class="section-inner room-layout">
    <div>
      <p class="eyebrow">Rooms that know the work</p>
      <h2>Discuss, split, decide, and keep receipts.</h2>
      <p>
        A room can carry participants, linked discussions, task state, file
        references, memory, prompt responses, and read receipts. Agents respond
        in the room they are in, while summaries and decisions can be promoted
        deliberately.
      </p>
      <div class="stat-grid">
        {#each stats as stat}
          <div>
            <strong>{stat[0]}</strong>
            <span>{stat[1]}</span>
          </div>
        {/each}
      </div>
    </div>
    <div class="product-frame">
      <img src="/product/ant-web-room.png" alt="ANT chat room with participants, discussions, tasks, file refs, and agent shortcuts" />
    </div>
  </div>
</section>

<section class="mobile-section">
  <div class="section-inner mobile-layout">
    <div class="phone-frame">
      <img src="/product/ant-ios-home.png" alt="ANTios home screen with needs-input, terminal, chat, pinned, and attention filters" />
    </div>
    <div>
      <p class="eyebrow">ANTios</p>
      <h2>The same coordination surface, built for thumb zones.</h2>
      <p>
        ANTios is chat-first: needs-input, terminals, chats, pinned items, and
        attention queues are visible immediately. It is for checking status,
        sending instructions, and unblocking agents without opening the full web UI.
      </p>
      <ul>
        <li>Needs-input, terminal, and chat counts at the top.</li>
        <li>Large tap targets and compact filters for one-handed use.</li>
        <li>Room list and quick actions tuned for mobile triage.</li>
      </ul>
    </div>
  </div>
</section>

<section class="install-section">
  <div class="section-inner">
    <div class="section-heading-row">
      <div>
        <p class="eyebrow">Self-hosted</p>
        <h2>Run the control room on your own machine.</h2>
      </div>
      <a href="/docs" class="cta-primary">Full docs</a>
    </div>
    <div class="install-grid">
      <div>
        <p class="install-label">Server</p>
        <div class="code-block">
          <pre><span class="line-comment"># clone and run</span>
<span class="line-cmd">git clone https://github.com/Jktfe/a-nice-terminal</span>
<span class="line-cmd">cd a-nice-terminal && npm install</span>
<span class="line-cmd">cp .env.example .env</span>
<span class="line-cmd">npm run build</span>
<span class="line-cmd">npm run start</span></pre>
        </div>
      </div>
      <div>
        <p class="install-label">CLI</p>
        <div class="code-block">
          <pre><span class="line-comment"># join rooms and inspect evidence</span>
<span class="line-cmd">ant sessions</span>
<span class="line-cmd">ant chat join &lt;room-id&gt;</span>
<span class="line-cmd">ant chat decide &lt;id&gt; approve --why "safe"</span>
<span class="line-cmd">ant terminal history &lt;session-id&gt;</span></pre>
        </div>
      </div>
    </div>
  </div>
</section>

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
  header {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    z-index: 100;
    background: rgba(7, 11, 18, 0.72);
    border-bottom: 1px solid rgba(255,255,255,0.08);
    backdrop-filter: blur(14px);
    -webkit-backdrop-filter: blur(14px);
  }

  nav,
  .section-inner,
  .footer-inner {
    width: min(1180px, calc(100vw - 3rem));
    margin: 0 auto;
  }

  nav {
    height: 60px;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  .logo {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    color: var(--text);
    font-family: var(--font-display);
    font-weight: 700;
    text-decoration: none;
    letter-spacing: 0;
  }

  .nav-links,
  .hero-actions,
  .footer-links {
    display: flex;
    align-items: center;
    gap: 0.9rem;
    flex-wrap: wrap;
  }

  .nav-links a,
  .footer-links a {
    color: var(--muted);
    font-size: 0.9rem;
    text-decoration: none;
  }

  .nav-links a:hover,
  .footer-links a:hover {
    color: var(--text);
  }

  .nav-github,
  .cta-secondary {
    border: 1px solid rgba(255,255,255,0.14);
    border-radius: 8px;
    padding: 0.55rem 0.9rem;
  }

  .hero {
    min-height: 88vh;
    position: relative;
    display: grid;
    align-items: end;
    overflow: hidden;
    border-bottom: 1px solid var(--border);
    background: #071019;
  }

  .hero-shot {
    position: absolute;
    inset: 0;
    width: 100%;
    height: 100%;
    object-fit: cover;
    object-position: center;
    filter: saturate(0.92) contrast(1.04);
  }

  .hero-shade {
    position: absolute;
    inset: 0;
    background:
      linear-gradient(90deg, rgba(5,8,16,0.94) 0%, rgba(5,8,16,0.78) 38%, rgba(5,8,16,0.22) 100%),
      linear-gradient(0deg, rgba(7,11,18,0.9) 0%, rgba(7,11,18,0.08) 42%, rgba(7,11,18,0.5) 100%);
  }

  .hero-content {
    position: relative;
    z-index: 1;
    width: min(720px, calc(100vw - 2rem));
    margin: 0 auto;
    padding: 8rem 0 4.25rem;
    transform: translateX(calc((min(1180px, calc(100vw - 3rem)) - min(720px, calc(100vw - 2rem))) / -2));
  }

  .hero-badge,
  .eyebrow,
  .install-label {
    color: #8EA0B8;
    font-family: var(--font-mono);
    font-size: 0.76rem;
    letter-spacing: 0.04em;
    text-transform: uppercase;
  }

  .hero-badge {
    display: inline-flex;
    align-items: center;
    gap: 0.55rem;
    padding: 0.35rem 0.7rem;
    border: 1px solid rgba(99,102,241,0.34);
    border-radius: 999px;
    background: rgba(10,15,24,0.72);
    margin-bottom: 1.35rem;
    text-transform: none;
  }

  .hero-badge span {
    width: 7px;
    height: 7px;
    border-radius: 50%;
    background: var(--green);
    box-shadow: 0 0 10px var(--green);
  }

  h1,
  h2,
  h3 {
    color: var(--text);
    font-family: var(--font-display);
    letter-spacing: 0;
  }

  h1 {
    max-width: 680px;
    font-size: 4.9rem;
    line-height: 0.98;
    font-weight: 800;
    margin: 0 0 1.25rem;
  }

  h2 {
    font-size: 2.1rem;
    line-height: 1.1;
    font-weight: 700;
    margin: 0;
  }

  h3 {
    font-size: 1.03rem;
    font-weight: 700;
    margin: 0;
  }

  .hero-content > p,
  .snapshot-copy p,
  .section-heading-row > p,
  .room-layout p,
  .mobile-layout p,
  .mobile-layout li,
  .capability-card span {
    color: #9AA8BA;
    line-height: 1.7;
  }

  .hero-content > p {
    max-width: 610px;
    font-size: 1.1rem;
    margin: 0 0 1.75rem;
  }

  .cta-primary,
  .cta-secondary {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    min-height: 42px;
    color: #fff;
    font-weight: 600;
    text-decoration: none;
  }

  .cta-primary {
    background: var(--indigo);
    border-radius: 8px;
    padding: 0.65rem 1.15rem;
  }

  .cta-secondary {
    background: rgba(7,11,18,0.48);
  }

  .hero-tags {
    display: flex;
    gap: 0.45rem;
    flex-wrap: wrap;
    margin-top: 1.4rem;
  }

  .hero-tags span {
    padding: 0.28rem 0.62rem;
    border: 1px solid rgba(255,255,255,0.12);
    border-radius: 6px;
    background: rgba(7,11,18,0.58);
    color: #CDD6E2;
    font-family: var(--font-mono);
    font-size: 0.74rem;
  }

  section:not(.hero) {
    padding: 5.6rem 0;
    border-bottom: 1px solid var(--border);
  }

  .snapshot-band,
  .install-section {
    background: var(--bg-2);
  }

  .snapshot-copy {
    display: grid;
    grid-template-columns: minmax(0, 0.8fr) minmax(0, 1.1fr);
    gap: 2rem;
    align-items: end;
    margin-bottom: 2rem;
  }

  .snapshot-copy h2,
  .section-heading-row h2,
  .room-layout h2,
  .mobile-layout h2 {
    margin-top: 0.45rem;
  }

  .snapshot-copy p:last-child,
  .section-heading-row > p {
    margin: 0;
    max-width: 540px;
  }

  .product-frame {
    border: 1px solid rgba(255,255,255,0.12);
    border-radius: 8px;
    overflow: hidden;
    background: #F7F7F5;
    box-shadow: 0 32px 80px rgba(0,0,0,0.35);
  }

  .product-frame img {
    display: block;
    width: 100%;
    height: auto;
  }

  .product-frame.wide {
    max-height: 640px;
  }

  .section-heading-row {
    display: flex;
    justify-content: space-between;
    gap: 2rem;
    align-items: end;
    margin-bottom: 2rem;
  }

  .capability-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 1px;
    border: 1px solid var(--border);
    border-radius: 8px;
    overflow: hidden;
    background: var(--border);
  }

  .capability-card {
    padding: 1.55rem;
    background: var(--bg);
  }

  .capability-card p {
    color: var(--green);
    font-family: var(--font-mono);
    font-size: 0.72rem;
    margin: 0 0 0.8rem;
    text-transform: uppercase;
  }

  .capability-card h3 {
    margin-bottom: 0.65rem;
  }

  .capability-card span {
    display: block;
    font-size: 0.88rem;
  }

  .room-layout,
  .mobile-layout {
    display: grid;
    grid-template-columns: minmax(0, 0.82fr) minmax(0, 1.18fr);
    gap: 2.4rem;
    align-items: center;
  }

  .room-layout p,
  .mobile-layout p {
    margin: 1rem 0 1.2rem;
  }

  .stat-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 0.75rem;
    margin-top: 1.5rem;
  }

  .stat-grid div {
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 1rem;
    background: var(--bg-card);
  }

  .stat-grid strong,
  .stat-grid span {
    display: block;
  }

  .stat-grid strong {
    color: var(--text);
    font-family: var(--font-display);
    font-size: 1.05rem;
    margin-bottom: 0.25rem;
  }

  .stat-grid span {
    color: var(--muted);
    font-size: 0.82rem;
  }

  .mobile-section {
    background: #F7F7F4;
  }

  .mobile-section h2 {
    color: #111827;
  }

  .mobile-section .eyebrow {
    color: #4F46E5;
  }

  .mobile-layout {
    grid-template-columns: minmax(260px, 0.45fr) minmax(0, 0.8fr);
  }

  .mobile-layout p,
  .mobile-layout li {
    color: #4B5563;
  }

  .phone-frame {
    width: min(360px, 100%);
    margin: 0 auto;
    border: 10px solid #111;
    border-radius: 34px;
    overflow: hidden;
    background: #fff;
    box-shadow: 0 28px 70px rgba(0,0,0,0.28);
  }

  .phone-frame img {
    display: block;
    width: 100%;
    height: auto;
  }

  .mobile-layout ul {
    padding-left: 1.1rem;
    margin: 1rem 0 0;
  }

  .install-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 1.2rem;
  }

  .install-grid > div {
    min-width: 0;
  }

  .install-label {
    display: block;
    margin-bottom: 0.55rem;
  }

  .code-block {
    max-width: 100%;
    padding: 1.15rem 1.25rem;
  }

  .code-block pre {
    margin: 0;
    font-size: 0.82rem;
    line-height: 1.75;
    overflow-x: auto;
    white-space: pre;
  }

  footer {
    padding: 2rem 0;
  }

  .footer-inner {
    display: flex;
    gap: 2rem;
    align-items: center;
    flex-wrap: wrap;
  }

  .footer-links {
    flex: 1;
  }

  .footer-copy {
    color: #334155;
    font-size: 0.8rem;
  }

  @media (max-width: 900px) {
    nav,
    .section-inner,
    .footer-inner {
      width: min(100% - 2rem, 1180px);
    }

    h1 {
      font-size: clamp(2.35rem, 10vw, 2.85rem);
      line-height: 1.02;
    }

    .hero-content {
      transform: none;
      margin-left: 1rem;
      margin-right: 1rem;
      padding-bottom: 3rem;
    }

    .hero-shade {
      background:
        linear-gradient(90deg, rgba(5,8,16,0.96) 0%, rgba(5,8,16,0.72) 100%),
        linear-gradient(0deg, rgba(7,11,18,0.92) 0%, rgba(7,11,18,0.12) 60%);
    }

    .snapshot-copy,
    .section-heading-row,
    .room-layout,
    .mobile-layout,
    .install-grid {
      display: block;
    }

    .snapshot-copy > *,
    .section-heading-row > *,
    .room-layout > *,
    .mobile-layout > * {
      margin-bottom: 1.3rem;
    }

    .capability-grid {
      grid-template-columns: 1fr;
    }

    .stat-grid {
      grid-template-columns: 1fr;
    }

    section:not(.hero) {
      padding: 4rem 0;
    }

    .phone-frame {
      max-width: 310px;
      margin-bottom: 2rem;
    }
  }
</style>
