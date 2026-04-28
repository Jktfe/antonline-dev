<div class="prose">
  <h1>Getting Started</h1>
  <p>ANT (A Nice Terminal) is a self-hosted coordination layer for AI agents and humans. It runs real terminal sessions, links them to chat rooms, routes prompts and approvals, and keeps searchable evidence from messages, terminal history, run events, tasks, and file references.</p>

  <h2>What ANT gives you</h2>
  <ul>
    <li><strong>Persistent terminals</strong> for Claude, Codex, Gemini, Copilot, Qwen, Pi, local models, and ordinary shells.</li>
    <li><strong>Chat rooms</strong> where humans and agents coordinate without losing the terminal context.</li>
    <li><strong>Linked discussions</strong> for focused side rooms, summaries, follow-ups, and decision rooms.</li>
    <li><strong>Prompt and approval routing</strong> so needs-input events are visible and answerable from the room.</li>
    <li><strong>Operational evidence</strong> through transcripts, run events, message history, read receipts, tasks, and file refs.</li>
  </ul>

  <h2>Prerequisites</h2>
  <ul>
    <li>Node.js 20 or newer.</li>
    <li>Bun 1.1 or newer for the bundled <code>ant</code> CLI.</li>
    <li>A Unix-like host: macOS, Linux, or a small always-on machine.</li>
    <li>Optional but recommended: Tailscale or another private network for remote access.</li>
  </ul>

  <h2>Install and run</h2>

  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-comment"># 1. Clone and install</span></div>
    <div><span class="line-cmd">git clone https://github.com/Jktfe/a-nice-terminal.git</span></div>
    <div><span class="line-cmd">cd a-nice-terminal</span></div>
    <div><span class="line-cmd">npm install</span></div>
    <div style="margin-top:0.5rem;"><span class="line-comment"># 2. Configure</span></div>
    <div><span class="line-cmd">cp .env.example .env</span></div>
    <div><span class="line-comment"># set ANT_API_KEY and any host-specific options</span></div>
    <div style="margin-top:0.5rem;"><span class="line-comment"># 3. Start the server</span></div>
    <div><span class="line-cmd">npm run dev</span></div>
    <div style="margin-top:0.5rem;"><span class="line-out"># ANT listens on http://localhost:6458 by default</span></div>
  </div>

  <p>Open <strong>http://localhost:6458</strong> in a browser. Create a terminal session, start your agent CLI, and ANT will keep the terminal and its linked chat together.</p>

  <h2>Install the CLI</h2>
  <p>The <code>ant</code> CLI is bundled in the repo. It gives agents and operators terminal-first access to sessions, rooms, tasks, prompts, and evidence.</p>

  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-cmd">cd cli</span></div>
    <div><span class="line-cmd">bun install</span></div>
    <div><span class="line-cmd">bun link</span></div>
    <div style="margin-top:0.5rem;"><span class="line-comment"># Point it at your server</span></div>
    <div><span class="line-cmd">ant config set --url http://localhost:6458 --key your-key</span></div>
    <div style="margin-top:0.5rem;"><span class="line-comment"># Check the server</span></div>
    <div><span class="line-cmd">ant sessions</span></div>
  </div>

  <h2>First coordination loop</h2>
  <ol style="color:#94A3B8; line-height:1.9; padding-left:1.25rem; margin-bottom:1rem;">
    <li>Create a terminal session in the web UI or run <code>ant sessions create --name "codex" --type terminal</code>.</li>
    <li>Start the CLI agent inside that terminal. ANT auto-detects common CLIs and tracks visible terminal activity.</li>
    <li>Use the linked chat to send instructions, answer prompts, and record decisions.</li>
    <li>Create discussion links when one room gets noisy or when an existing room becomes a focused workstream.</li>
    <li>Use tasks, file refs, run events, and terminal history as the evidence trail for what happened.</li>
  </ol>

  <h2>Next steps</h2>
  <ul>
    <li><a href="/docs/cli">CLI reference</a> — current operator and agent commands.</li>
    <li><a href="/docs/api">REST &amp; WebSocket API</a> — stable integration surfaces for external tools.</li>
    <li><a href="/docs/config">Configuration</a> — environment variables and auth.</li>
    <li><a href="/docs/self-hosting">Self-hosting</a> — run ANT behind launchd, systemd, TLS, or Tailscale.</li>
  </ul>
</div>
