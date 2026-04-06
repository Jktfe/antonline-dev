<div class="prose">
  <h1>Getting Started</h1>
  <p>ANT (A Nice Terminal) is a self-hosted terminal multiplexer that streams live PTY sessions over WebSocket. Run it on your server or Mac, then access full terminal sessions from any browser — with keyboard-correct input, session persistence, and full-text search.</p>

  <h2>Prerequisites</h2>
  <ul>
    <li>Node.js ≥ 20 or Bun ≥ 1.1</li>
    <li>A Unix-like OS (macOS, Linux)</li>
    <li>Optional: <code>ant</code> CLI for remote access</li>
  </ul>

  <h2>Install &amp; run</h2>

  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-comment"># 1. Clone and install</span></div>
    <div><span class="line-cmd">git clone https://github.com/Jktfe/a-nice-terminal.git</span></div>
    <div><span class="line-cmd">cd a-nice-terminal &amp;&amp; npm install</span></div>
    <div style="margin-top:0.5rem;"><span class="line-comment"># 2. Configure (copy example and edit)</span></div>
    <div><span class="line-cmd">cp .env.example .env</span></div>
    <div><span class="line-comment"># set ANT_API_KEY and optionally ANT_PORT</span></div>
    <div style="margin-top:0.5rem;"><span class="line-comment"># 3. Start the server</span></div>
    <div><span class="line-cmd">npm run dev</span></div>
    <div style="margin-top:0.5rem;"><span class="line-out"># → Server listening on http://localhost:6458</span></div>
  </div>

  <p>Open <strong>http://localhost:6458</strong> in your browser. You'll see the session list. Click <em>New session</em> to start a terminal.</p>

  <h2>Install the CLI</h2>
  <p>The <code>ant</code> CLI lets you attach, share, and manage sessions from your terminal.</p>

  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-cmd">npm install -g @jktfe/ant-cli</span></div>
    <div style="margin-top:0.5rem;"><span class="line-comment"># Point it at your server</span></div>
    <div><span class="line-cmd">export ANT_URL=http://localhost:6458</span></div>
    <div><span class="line-cmd">export ANT_API_KEY=your-key</span></div>
    <div style="margin-top:0.5rem;"><span class="line-comment"># List sessions</span></div>
    <div><span class="line-cmd">ant list</span></div>
    <div><span class="line-comment"># Watch a session in your terminal</span></div>
    <div><span class="line-cmd">ant watch &lt;session-id&gt;</span></div>
  </div>

  <h2>First session walkthrough</h2>
  <ol style="color:#94A3B8; line-height:1.9; padding-left:1.25rem; margin-bottom:1rem;">
    <li>Click <strong>New session</strong> — ANT spawns a PTY with your default shell.</li>
    <li>Type commands normally. Keyboard shortcuts (arrows, Tab, Ctrl+C, Esc, Option+key) all work.</li>
    <li>For long commands use <strong>Slow Edit</strong> (the pencil icon) — compose in a textarea, send with Ctrl+Enter.</li>
    <li>Click the share icon to generate a read-only share link. Observers see your terminal live, but cannot type.</li>
    <li>Use the search icon to run a full-text search across all session output (powered by SQLite FTS5).</li>
  </ol>

  <h2>Next steps</h2>
  <ul>
    <li><a href="/docs/config">Configuration</a> — all environment variables</li>
    <li><a href="/docs/cli">CLI reference</a> — full <code>ant</code> command list</li>
    <li><a href="/docs/api">REST &amp; WebSocket API</a> — integrate ANT with your own tools or AI agents</li>
    <li><a href="/docs/self-hosting">Self-hosting</a> — launchd, TLS, Tailscale setup</li>
  </ul>
</div>
