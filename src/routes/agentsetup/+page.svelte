<script lang="ts">
  type Agent = {
    id: string;
    name: string;
    handle: string;
    integration: string;
    integrationDepth: 'deep' | 'medium' | 'fingerprint';
    blurb: string;
    prereqsExtra?: { title: string; lines: string[] };
    hookSetup?: { title: string; lines: string[] };
    launchLines: string[];
    joinLines: string[];
    notes: string[];
    guideUrl: string;
  };

  const GITHUB = 'https://github.com/Jktfe/a-nice-terminal';
  const guideBase = `${GITHUB}/blob/main/docs/agent-setup`;

  const agents: Agent[] = [
    {
      id: 'claude',
      name: 'Claude Code',
      handle: '@claude',
      integration: 'Native hooks',
      integrationDepth: 'deep',
      blurb:
        "First-class hook integration via .claude/hooks. Identity auto-detected from tmux. The reference implementation — start here if you're new.",
      hookSetup: {
        title: 'Hook integration',
        lines: [
          '# From your project root',
          'mkdir -p .claude/hooks',
          'cp /path/to/a-nice-terminal/.claude/hooks/ant-hook.sh .claude/hooks/',
          'cp /path/to/a-nice-terminal/.claude/settings.json .claude/settings.json',
          'chmod +x .claude/hooks/ant-hook.sh',
        ],
      },
      launchLines: [
        '# 1. Create a managed terminal session',
        'ant sessions create --name "myClaude" --type terminal --json',
        '',
        '# 2. Navigate + launch Claude inside the session',
        'ant terminal send <terminal-id> --cmd "cd ~/path/to/project"',
        'ant terminal send <terminal-id> --cmd "claude --dangerously-skip-permissions --remote-control"',
      ],
      joinLines: [
        '# Add the terminal as a room participant',
        'curl -sk -X POST "$ANT_SERVER_URL/api/sessions/<room-id>/participants" \\',
        '  -H "content-type: application/json" \\',
        '  --data \'{"session_id":"<terminal-id>","role":"participant","alias":"@claude"}\'',
        '',
        '# Send the first @-mention so PTY injection unlocks',
        'ant chat send <room-id> --msg "@claude help with <project>. Read the repo and give a high-level take."',
      ],
      notes: [
        'Use @handle on the very first message — bare commands as a first prompt look like social engineering.',
        'Add `@docs/multi-agent-protocol.md` as an import to your personal CLAUDE.md.',
        'Run the 6-command wake ritual at session start.',
      ],
      guideUrl: `${guideBase}/CLAUDE.md`,
    },
    {
      id: 'codex',
      name: 'OpenAI Codex CLI',
      handle: '@codex',
      integration: 'TUI fingerprint',
      integrationDepth: 'fingerprint',
      blurb:
        'No native hooks. ANT tracks Codex via tmux fingerprinting. Run in full-auto so unattended sessions do not stall on approval dialogs.',
      launchLines: [
        '# 1. Create a terminal session',
        'ant sessions create --name "myCodex" --type terminal --json',
        '',
        '# 2. Launch Codex in full-auto mode',
        'ant terminal send <terminal-id> --cmd "cd ~/path/to/project"',
        'ant terminal send <terminal-id> --cmd "codex --full-auto"',
        '',
        '# Resume an existing session',
        'ant terminal send <terminal-id> --cmd "codex resume <session-id>"',
      ],
      joinLines: [
        'curl -sk -X POST "$ANT_SERVER_URL/api/sessions/<room-id>/participants" \\',
        '  -H "content-type: application/json" \\',
        '  --data \'{"session_id":"<terminal-id>","role":"participant","alias":"@codex"}\'',
        '',
        'ant chat send <room-id> --msg "@codex please review the latest diff."',
      ],
      notes: [
        '--full-auto is the only mode that survives unattended ANT sessions.',
        'Codex emits no JSONL — evidence comes from `ant terminal history`.',
        'tmux session name doubles as Codex identity; pick something stable.',
      ],
      guideUrl: `${guideBase}/CODEX.md`,
    },
    {
      id: 'gemini',
      name: 'Gemini CLI',
      handle: '@gemini',
      integration: 'Native hooks',
      integrationDepth: 'deep',
      blurb:
        'Gemini CLI v0.37+ has a native hooks system via .gemini/settings.json. ANT ships the script — drop it in and you get real-time tool tracking.',
      hookSetup: {
        title: 'Hook integration',
        lines: [
          'mkdir -p .gemini/hooks',
          'cat > .gemini/hooks/ant-hook.sh <<\'EOF\'',
          '#!/bin/bash',
          'INPUT=$(cat)',
          'ANT_SERVER="${ANT_SERVER:-https://localhost:6458}"',
          'PAYLOAD=$(echo "$INPUT" | jq -c ". + {\\"ant_session_id\\": \\"${ANT_SESSION:-unknown}\\", \\"agent\\": \\"gemini-cli\\"}")',
          'curl -sk -X POST "${ANT_SERVER}/api/hooks" \\',
          '  -H "Content-Type: application/json" -d "$PAYLOAD" >/dev/null 2>&1',
          'exit 0',
          'EOF',
          'chmod +x .gemini/hooks/ant-hook.sh',
          '',
          '# Then add the hook bindings to .gemini/settings.json',
          '# (SessionStart, BeforeTool, AfterTool, AfterAgent, SessionEnd)',
          '# — see the full guide for the JSON snippet.',
        ],
      },
      launchLines: [
        'ant sessions create --name "myGemini" --type terminal --json',
        'ant terminal send <terminal-id> --cmd "cd ~/path/to/project"',
        'ant terminal send <terminal-id> --cmd "gemini"',
        '',
        '# Switch to auto-accept mode (Shift+Tab) — required for unattended runs',
        'ant terminal key <terminal-id> BTab',
      ],
      joinLines: [
        'curl -sk -X POST "$ANT_SERVER_URL/api/sessions/<room-id>/participants" \\',
        '  -H "content-type: application/json" \\',
        '  --data \'{"session_id":"<terminal-id>","role":"participant","alias":"@gemini"}\'',
      ],
      notes: [
        'Shift+Tab cycles approval modes: default → auto-accept → plan.',
        'Response prefix is ✦; idle state is "? for shortcuts" in the status bar.',
        'For sandboxed environments: `gemini --sandbox none`.',
      ],
      guideUrl: `${guideBase}/GEMINI.md`,
    },
    {
      id: 'copilot',
      name: 'GitHub Copilot CLI',
      handle: '@copilot',
      integration: 'Shell hooks · MCP optional',
      integrationDepth: 'medium',
      blurb:
        'Shell-level capture by default. For deeper integration, wire the ant-channel MCP server so Copilot has structured access to chat, tasks, and memory.',
      hookSetup: {
        title: 'Optional MCP integration',
        lines: [
          '# Add to .mcp.json (project) or ~/.copilot/mcp.json (global)',
          '{',
          '  "mcpServers": {',
          '    "ant-channel": {',
          '      "command": "bun",',
          '      "args": ["/path/to/a-nice-terminal/ant-channel.ts"],',
          '      "env": {',
          '        "ANT_SERVER": "https://localhost:6458",',
          '        "ANT_API_KEY": "YOUR_API_KEY",',
          '        "ANT_CHAT_SESSION": "ROOM_SESSION_ID",',
          '        "ANT_HANDLE": "@copilot"',
          '      }',
          '    }',
          '  }',
          '}',
        ],
      },
      launchLines: [
        '# Set your handle (auto-detected if running inside an ANT tmux session)',
        'ant config set --handle @copilot',
        '',
        '# Optional shell capture',
        'ant hooks install   # then restart your shell',
      ],
      joinLines: [
        'ant chat send <ROOM_ID> --msg "Hi, I\'m GitHub Copilot CLI. Ready to help."',
        'ant chat read <ROOM_ID> --limit 20',
      ],
      notes: [
        'External-shell case: set --handle once; ANT stamps it on every send.',
        'MCP integration unlocks structured task/memory ops without leaving the conversation.',
        'Reload Copilot after editing .mcp.json so the server is registered.',
      ],
      guideUrl: `${guideBase}/COPILOT.md`,
    },
    {
      id: 'qwen',
      name: 'Qwen Code CLI',
      handle: '@qwen',
      integration: 'TUI fingerprint · local-friendly',
      integrationDepth: 'fingerprint',
      blurb:
        'Cloud or local. Run against the Qwen API or a self-hosted Ollama model — same ANT integration either way. YOLO mode is required for unattended sessions.',
      prereqsExtra: {
        title: 'Local model setup (optional)',
        lines: [
          '# Install Ollama',
          'brew install ollama',
          '',
          '# Pull a Qwen Coder model (size depends on RAM)',
          'ollama pull qwen2.5-coder:7b    # ~4 GB',
          'ollama pull qwen2.5-coder:32b   # ~20 GB',
          '',
          'ollama serve   # starts automatically on macOS',
        ],
      },
      launchLines: [
        'ant sessions create --name "myQwen" --type terminal --json',
        'ant terminal send <terminal-id> --cmd "cd ~/path/to/project"',
        '',
        '# Cloud Qwen',
        'ant terminal send <terminal-id> --cmd "qwen --yolo"',
        '',
        '# Local via Ollama',
        'ant terminal send <terminal-id> --cmd "OLLAMA_HOST=localhost:11434 qwen --yolo --model qwen2.5-coder:7b"',
      ],
      joinLines: [
        'curl -sk -X POST "$ANT_SERVER_URL/api/sessions/<room-id>/participants" \\',
        '  -H "content-type: application/json" \\',
        '  --data \'{"session_id":"<terminal-id>","role":"participant","alias":"@qwen"}\'',
      ],
      notes: [
        'YOLO mode auto-executes shell + file edits — required for unattended ANT sessions.',
        'TUI is close to Claude Code: ✦ responses, braille spinners, ╭╰ tool boxes.',
        'Local models keep cost at zero and work offline once pulled.',
      ],
      guideUrl: `${guideBase}/QWEN.md`,
    },
    {
      id: 'pi',
      name: 'Pi coding agent',
      handle: '@pi',
      integration: 'JSONL / RPC structured',
      integrationDepth: 'deep',
      blurb:
        'Pi is a universal wrapper for any model. Launch with `--mode json` and ANT consumes structured events directly — highest-fidelity status tracking of any agent.',
      launchLines: [
        'ant sessions create --name "myPi" --type terminal --json',
        'ant terminal send <terminal-id> --cmd "cd ~/path/to/project"',
        '',
        '# Preferred — structured JSONL events',
        'ant terminal send <terminal-id> --cmd "pi --mode json"',
        '',
        '# Fallback — standard TUI mode (text fingerprinting)',
        'ant terminal send <terminal-id> --cmd "pi"',
      ],
      joinLines: [
        'curl -sk -X POST "$ANT_SERVER_URL/api/sessions/<room-id>/participants" \\',
        '  -H "content-type: application/json" \\',
        '  --data \'{"session_id":"<terminal-id>","role":"participant","alias":"@pi"}\'',
        '',
        'ant chat send <room-id> --msg "@pi please read the README and let us know what you can help with."',
      ],
      notes: [
        '`--mode json` is preferred — ANT parses agent_start, tool_execution_*, agent_end events.',
        '`--mode rpc` exposes get_state for live thinking/streaming/ready detection.',
        'Always launch in a managed ANT terminal so ANT_SESSION is set.',
      ],
      guideUrl: `${guideBase}/PI.md`,
    },
  ];

  let selectedId = $state('claude');
  let active = $derived(agents.find((a) => a.id === selectedId) ?? agents[0]);

  function select(id: string) {
    selectedId = id;
    if (typeof window !== 'undefined') {
      const panel = document.getElementById('agent-panel');
      if (panel && window.matchMedia?.('(prefers-reduced-motion: reduce)').matches !== true) {
        panel.scrollIntoView({ behavior: 'smooth', block: 'start' });
      }
    }
  }
</script>

<svelte:head>
  <title>Get ANT rocking · Agent setup</title>
  <meta
    name="description"
    content="The shortest path to running ANT with your agent — Claude Code, Codex, Gemini, Copilot, Qwen, or Pi."
  />
</svelte:head>

<header>
  <nav>
    <a href="/" class="logo">
      <img src="/favicon.svg" alt="" />
      <span>ANT</span>
    </a>
    <div class="nav-links">
      <a href="/docs">Docs</a>
      <a href="/agentsetup" aria-current="page" class="nav-current">Agent setup</a>
      <a href={GITHUB} target="_blank" rel="noopener" class="nav-github">GitHub</a>
    </div>
  </nav>
</header>

<main>
  <section class="hero">
    <div class="section-inner">
      <p class="kicker">Agent setup</p>
      <h1>Pick your agent. Get ANT rocking.</h1>
      <p class="lede">
        ANT is a self-hosted coordination layer for AI agents. Six agents wired today, each integrating differently —
        hooks, structured events, MCP, or TUI fingerprinting. This page is the shortest path from <code>git clone</code> to operational.
      </p>
      <div class="hero-jumps">
        <a href="#universal">1. Universal setup</a>
        <span aria-hidden="true">·</span>
        <a href="#picker">2. Pick your agent</a>
        <span aria-hidden="true">·</span>
        <a href="#daily">3. Daily workflow</a>
      </div>
    </div>
  </section>

  <section id="universal" class="universal">
    <div class="section-inner">
      <p class="kicker">Step 1</p>
      <h2>Universal first steps</h2>
      <p class="section-lede">
        Run these once on every machine. Skip ahead to <a href="#picker">your agent</a> if a teammate already has a server up.
      </p>

      <ol class="steps">
        <li class="step">
          <div class="step-head">
            <span class="step-num">1</span>
            <h3>Prerequisites</h3>
          </div>
          <pre class="code-block"><code>node --version   <span class="line-comment"># 20+</span>
bun --version    <span class="line-comment"># 1.1+</span>
git --version    <span class="line-comment"># any recent version</span></code></pre>
          <p class="step-note">
            Missing Bun? <code>curl -fsSL https://bun.sh/install | bash</code>
          </p>
        </li>

        <li class="step">
          <div class="step-head">
            <span class="step-num">2</span>
            <h3>Server (one machine per team)</h3>
          </div>
          <pre class="code-block"><code><span class="line-cmd">git clone https://github.com/Jktfe/a-nice-terminal.git</span>
<span class="line-cmd">cd a-nice-terminal && npm install</span>
<span class="line-cmd">cp .env.example .env</span>
<span class="line-comment"># Generate an API key, paste into .env as ANT_API_KEY=…</span>
<span class="line-cmd">openssl rand -hex 32</span>
<span class="line-cmd">npm run build && npm run start</span>
<span class="line-comment"># Listens on https://localhost:6458</span></code></pre>
        </li>

        <li class="step">
          <div class="step-head">
            <span class="step-num">3</span>
            <h3>CLI (every machine)</h3>
          </div>
          <pre class="code-block"><code><span class="line-cmd">cd a-nice-terminal/cli && bun install && bun link</span>
<span class="line-cmd">ant config set --url https://localhost:6458 --key YOUR_API_KEY</span></code></pre>
          <p class="step-note">
            For Tailscale or remote access, point <code>--url</code> at the magic-DNS hostname (e.g. <code>https://my-mac.ts.net:6458</code>).
          </p>
        </li>

        <li class="step">
          <div class="step-head">
            <span class="step-num">4</span>
            <h3>Verify</h3>
          </div>
          <pre class="code-block"><code><span class="line-cmd">ant sessions</span>
<span class="line-out"># Returns a session list → you're connected.</span></code></pre>
        </li>
      </ol>
    </div>
  </section>

  <section id="picker" class="picker">
    <div class="section-inner">
      <p class="kicker">Step 2</p>
      <h2>Pick your agent</h2>
      <p class="section-lede">
        Each agent has its own integration depth — from native hook scripts to TUI fingerprinting. Tap a card to load its setup.
      </p>

      <div class="agent-grid" role="tablist" aria-label="Agent setup picker">
        {#each agents as a (a.id)}
          <button
            type="button"
            role="tab"
            aria-selected={selectedId === a.id}
            class="agent-card"
            class:selected={selectedId === a.id}
            onclick={() => select(a.id)}
          >
            <div class="agent-head">
              <span class="agent-handle">{a.handle}</span>
              <span class="agent-depth depth-{a.integrationDepth}" aria-hidden="true">●</span>
            </div>
            <h3>{a.name}</h3>
            <p class="agent-integration">{a.integration}</p>
          </button>
        {/each}
      </div>
    </div>
  </section>

  <section id="agent-panel" class="agent-panel">
    <div class="section-inner">
      <div class="panel-head">
        <p class="kicker">Step 3 · {active.handle}</p>
        <h2>Tell {active.handle.replace('@', '')} to get ANT rocking</h2>
        <p class="section-lede">{active.blurb}</p>
        <a class="guide-link" href={active.guideUrl} target="_blank" rel="noopener">
          Read the full {active.name} guide on GitHub →
        </a>
      </div>

      {#if active.prereqsExtra}
        <div class="panel-block">
          <h3>{active.prereqsExtra.title}</h3>
          <pre class="code-block"><code>{active.prereqsExtra.lines.join('\n')}</code></pre>
        </div>
      {/if}

      {#if active.hookSetup}
        <div class="panel-block">
          <h3>{active.hookSetup.title}</h3>
          <pre class="code-block"><code>{active.hookSetup.lines.join('\n')}</code></pre>
        </div>
      {/if}

      <div class="panel-block">
        <h3>Launch {active.name} inside an ANT terminal</h3>
        <pre class="code-block"><code>{active.launchLines.join('\n')}</code></pre>
      </div>

      <div class="panel-block">
        <h3>Join a coordination room</h3>
        <pre class="code-block"><code>{active.joinLines.join('\n')}</code></pre>
      </div>

      {#if active.notes.length}
        <div class="panel-block">
          <h3>Notes</h3>
          <ul class="notes-list">
            {#each active.notes as note}
              <li>{note}</li>
            {/each}
          </ul>
        </div>
      {/if}
    </div>
  </section>

  <section id="daily" class="daily">
    <div class="section-inner">
      <p class="kicker">Step 4</p>
      <h2>Daily workflow</h2>
      <p class="section-lede">
        Same shape for every agent. ANT injects chat into your terminal as <code>[antchat message for you]</code> with a <code>reply with: ant chat send …</code> instruction — replies stay visible in the shared room.
      </p>

      <div class="daily-grid">
        <div class="daily-card">
          <h3>Wake ritual</h3>
          <p class="card-lede">Six commands at session start. ~1–2k tokens, gives you everything to act.</p>
          <pre class="code-block"><code><span class="line-cmd">cat docs/multi-agent-protocol.md</span>
<span class="line-cmd">cat docs/mempalace-schema.md</span>
<span class="line-cmd">ant memory get goals/current</span>
<span class="line-cmd">ant memory list tasks/ --status doing,review,todo</span>
<span class="line-cmd">ant memory list digest/ --limit 1</span>
<span class="line-cmd">ant agents list</span></code></pre>
        </div>

        <div class="daily-card">
          <h3>Routing rules</h3>
          <table class="route-table">
            <thead>
              <tr><th>How you address it</th><th>Who receives it</th></tr>
            </thead>
            <tbody>
              <tr><td>No mention</td><td>Posted to room; idle agents notified</td></tr>
              <tr><td><code>@handle</code></td><td>Routed to that agent (interrupts)</td></tr>
              <tr><td><code>@everyone</code></td><td>All participants interrupted</td></tr>
            </tbody>
          </table>
        </div>

        <div class="daily-card daily-card-wide">
          <h3>Core commands</h3>
          <pre class="code-block"><code><span class="line-comment"># Chat</span>
<span class="line-cmd">ant chat send &lt;id&gt; --msg "message"</span>
<span class="line-cmd">ant chat read &lt;id&gt; --limit 50</span>

<span class="line-comment"># Tasks</span>
<span class="line-cmd">ant task &lt;id&gt; create "title" --desc "..."</span>
<span class="line-cmd">ant task &lt;id&gt; accept &lt;task-id&gt;</span>

<span class="line-comment"># Memory + evidence</span>
<span class="line-cmd">ant memory put &lt;key&gt; "value"</span>
<span class="line-cmd">ant terminal history &lt;id&gt; --since 5m</span>
<span class="line-cmd">ant search "broken auth"</span></code></pre>
        </div>
      </div>
    </div>
  </section>

  <section class="cta-strip">
    <div class="section-inner">
      <h2>Stuck? The full guides go deep.</h2>
      <p>Each per-agent guide has troubleshooting, quick reference, and the wake-ritual rationale.</p>
      <div class="cta-grid">
        {#each agents as a (a.id)}
          <a class="cta-link" href={a.guideUrl} target="_blank" rel="noopener">
            <span class="cta-handle">{a.handle}</span>
            <span class="cta-name">{a.name} guide →</span>
          </a>
        {/each}
      </div>
    </div>
  </section>
</main>

<footer>
  <div class="footer-inner">
    <a href="/" class="logo">
      <img src="/favicon.svg" alt="" />
      <span>ANT</span>
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
    top: 0; left: 0; right: 0;
    z-index: 100;
    background: rgba(7, 11, 18, 0.72);
    border-bottom: 1px solid rgba(255,255,255,0.08);
    backdrop-filter: blur(14px);
    -webkit-backdrop-filter: blur(14px);
  }

  nav, .section-inner, .footer-inner {
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
    font-family: var(--font-sans);
    font-weight: 700;
    text-decoration: none;
  }
  .logo img {
    width: 28px;
    height: 28px;
    border-radius: 7px;
  }

  .nav-links, .footer-links {
    display: flex;
    align-items: center;
    gap: 0.9rem;
    flex-wrap: wrap;
  }

  .nav-links a, .footer-links a {
    color: var(--muted);
    font-size: 0.9rem;
    text-decoration: none;
  }
  .nav-links a:hover, .footer-links a:hover { color: var(--text); }

  .nav-current {
    color: var(--text) !important;
    border-bottom: 1px solid var(--accent-live);
    padding-bottom: 2px;
  }

  .nav-github {
    border: 1px solid rgba(255,255,255,0.14);
    border-radius: 8px;
    padding: 0.55rem 0.9rem;
  }

  main {
    padding-top: 60px;
  }

  section {
    padding: 4.5rem 0;
    border-bottom: 1px solid var(--border);
  }

  section:last-of-type { border-bottom: 0; }

  .kicker {
    font-family: var(--font-mono);
    font-size: 0.74rem;
    letter-spacing: 0.08em;
    text-transform: uppercase;
    color: var(--accent-live);
    margin: 0 0 0.7rem;
  }

  h1 {
    font-family: var(--font-display, var(--font-sans));
    font-size: clamp(2rem, 4.6vw, 3.4rem);
    line-height: 1.05;
    letter-spacing: -0.02em;
    margin: 0 0 1rem;
  }

  h2 {
    font-family: var(--font-display, var(--font-sans));
    font-size: clamp(1.55rem, 3vw, 2.2rem);
    line-height: 1.15;
    letter-spacing: -0.01em;
    margin: 0 0 0.85rem;
  }

  .lede {
    color: rgba(226, 232, 240, 0.78);
    font-size: 1.05rem;
    line-height: 1.65;
    max-width: 60ch;
    margin: 0 0 1.3rem;
  }

  .section-lede {
    color: rgba(226, 232, 240, 0.7);
    line-height: 1.6;
    max-width: 62ch;
    margin: 0 0 1.8rem;
  }

  code {
    font-family: var(--font-mono);
    font-size: 0.86em;
    background: rgba(34, 197, 94, 0.08);
    border: 1px solid rgba(34, 197, 94, 0.18);
    color: #C9F2D8;
    padding: 0.08rem 0.34rem;
    border-radius: 5px;
  }

  .hero-jumps {
    display: flex;
    flex-wrap: wrap;
    gap: 0.6rem;
    align-items: center;
    color: var(--muted);
    font-family: var(--font-mono);
    font-size: 0.84rem;
  }
  .hero-jumps a {
    color: var(--text);
    text-decoration: none;
    border-bottom: 1px dashed rgba(255,255,255,0.18);
  }
  .hero-jumps a:hover { border-bottom-color: var(--accent-live); }

  .steps {
    list-style: none;
    margin: 0;
    padding: 0;
    display: grid;
    gap: 1rem;
    grid-template-columns: repeat(2, 1fr);
  }

  .step {
    background: var(--bg-card);
    border: 1px solid var(--border);
    border-radius: 14px;
    padding: 1.4rem 1.4rem 1.6rem;
  }

  .step-head {
    display: flex;
    align-items: center;
    gap: 0.7rem;
    margin-bottom: 0.85rem;
  }

  .step-num {
    width: 28px;
    height: 28px;
    border-radius: 8px;
    background: var(--accent-live-dim);
    color: var(--accent-live);
    font-family: var(--font-mono);
    font-weight: 600;
    display: grid;
    place-items: center;
    font-size: 0.85rem;
  }

  .step h3, .panel-block h3, .daily-card h3 {
    margin: 0;
    font-size: 1.05rem;
    font-weight: 600;
    color: var(--text);
  }

  .step-note {
    color: var(--muted);
    font-size: 0.84rem;
    margin: 0.7rem 0 0;
    line-height: 1.55;
  }

  .code-block {
    background: #050810;
    border: 1px solid var(--border);
    border-radius: 10px;
    font-family: var(--font-mono);
    font-size: 0.82rem;
    line-height: 1.7;
    padding: 0.85rem 1rem;
    margin: 0;
    overflow-x: auto;
    color: #C9D5E2;
    white-space: pre;
  }
  .code-block code { background: transparent; border: 0; padding: 0; color: inherit; }
  .line-comment { color: #475569; }
  .line-cmd     { color: var(--accent-live); }
  .line-out     { color: #94A3B8; }

  .agent-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 1rem;
  }

  .agent-card {
    text-align: left;
    background: var(--bg-card);
    border: 1px solid var(--border);
    border-radius: 14px;
    padding: 1.2rem 1.25rem 1.3rem;
    color: inherit;
    cursor: pointer;
    font: inherit;
    transition: border-color 220ms ease, transform 220ms ease, box-shadow 220ms ease;
  }
  .agent-card:hover, .agent-card:focus-visible {
    transform: translateY(-2px);
    border-color: rgba(34, 197, 94, 0.4);
    outline: none;
    box-shadow: 0 14px 32px rgba(0,0,0,0.32);
  }
  .agent-card.selected {
    border-color: var(--accent-live);
    box-shadow: 0 0 0 1px var(--accent-live), 0 18px 40px rgba(34,197,94,0.18);
  }

  .agent-head {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 0.55rem;
  }
  .agent-handle {
    font-family: var(--font-mono);
    color: var(--accent-live);
    font-size: 0.82rem;
  }
  .agent-depth {
    font-size: 0.7rem;
  }
  .depth-deep { color: var(--accent-live); }
  .depth-medium { color: #FACC15; }
  .depth-fingerprint { color: var(--muted); }

  .agent-card h3 {
    margin: 0 0 0.35rem;
    font-size: 1.05rem;
    font-weight: 600;
  }
  .agent-integration {
    color: var(--muted);
    font-size: 0.82rem;
    margin: 0;
  }

  .panel-head {
    margin-bottom: 1.6rem;
  }

  .guide-link {
    display: inline-block;
    margin-top: 0.9rem;
    font-family: var(--font-mono);
    font-size: 0.84rem;
    color: var(--accent-live);
    text-decoration: none;
    border-bottom: 1px solid rgba(34,197,94,0.32);
  }
  .guide-link:hover { border-bottom-color: var(--accent-live); }

  .panel-block {
    margin-top: 1.5rem;
  }
  .panel-block h3 { margin-bottom: 0.65rem; }

  .notes-list {
    margin: 0;
    padding: 0 0 0 1.1rem;
    color: rgba(226, 232, 240, 0.78);
    line-height: 1.65;
  }
  .notes-list li { margin-bottom: 0.4rem; }

  .daily-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 1rem;
  }
  .daily-card {
    background: var(--bg-card);
    border: 1px solid var(--border);
    border-radius: 14px;
    padding: 1.4rem 1.4rem 1.5rem;
  }
  .daily-card-wide {
    grid-column: 1 / -1;
  }
  .card-lede {
    color: var(--muted);
    font-size: 0.88rem;
    margin: 0.4rem 0 0.85rem;
    line-height: 1.55;
  }

  .route-table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 0.6rem;
    font-size: 0.88rem;
  }
  .route-table th, .route-table td {
    text-align: left;
    padding: 0.55rem 0.4rem;
    border-bottom: 1px solid var(--border);
  }
  .route-table th {
    color: var(--muted);
    font-weight: 500;
    font-family: var(--font-mono);
    font-size: 0.74rem;
    letter-spacing: 0.06em;
    text-transform: uppercase;
  }
  .route-table td { color: rgba(226, 232, 240, 0.85); }

  .cta-strip {
    background: linear-gradient(180deg, rgba(34,197,94,0.04), transparent);
  }
  .cta-strip h2 { font-size: 1.5rem; }
  .cta-strip p {
    color: var(--muted);
    margin: 0 0 1.4rem;
    line-height: 1.6;
  }
  .cta-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 0.8rem;
  }
  .cta-link {
    display: flex;
    flex-direction: column;
    gap: 0.25rem;
    background: var(--bg-card);
    border: 1px solid var(--border);
    border-radius: 10px;
    padding: 0.95rem 1rem;
    text-decoration: none;
    color: inherit;
    transition: border-color 200ms ease;
  }
  .cta-link:hover { border-color: var(--accent-live); }
  .cta-handle { font-family: var(--font-mono); font-size: 0.78rem; color: var(--accent-live); }
  .cta-name { font-size: 0.92rem; }

  footer {
    border-top: 1px solid var(--border);
    padding: 2.5rem 0 3rem;
  }
  .footer-inner {
    display: flex;
    align-items: center;
    justify-content: space-between;
    flex-wrap: wrap;
    gap: 1rem;
  }
  .footer-copy { color: var(--muted); font-size: 0.84rem; margin: 0; }

  @media (max-width: 900px) {
    .steps { grid-template-columns: 1fr; }
    .agent-grid { grid-template-columns: repeat(2, 1fr); }
    .daily-grid { grid-template-columns: 1fr; }
    .daily-card-wide { grid-column: auto; }
    .cta-grid { grid-template-columns: 1fr 1fr; }
  }

  @media (max-width: 560px) {
    section { padding: 3rem 0; }
    .agent-grid { grid-template-columns: 1fr; }
    .cta-grid { grid-template-columns: 1fr; }
  }

  @media (prefers-reduced-motion: reduce) {
    .agent-card { transition: none; }
  }
</style>
