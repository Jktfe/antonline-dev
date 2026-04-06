<div class="prose">
  <h1>CLI Reference</h1>
  <p>The <code>ant</code> CLI provides terminal-first access to ANT sessions. Install it globally, then point it at your server with environment variables or flags.</p>

  <h2>Installation</h2>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-cmd">npm install -g @jktfe/ant-cli</span></div>
    <div><span class="line-comment"># or with Bun</span></div>
    <div><span class="line-cmd">bun add -g @jktfe/ant-cli</span></div>
  </div>

  <h2>Environment variables</h2>
  <table>
    <thead>
      <tr><th>Variable</th><th>Description</th></tr>
    </thead>
    <tbody>
      <tr><td><code>ANT_URL</code></td><td>Base URL of the ANT server (default: <code>http://localhost:6458</code>)</td></tr>
      <tr><td><code>ANT_API_KEY</code></td><td>API key matching the server's <code>ANT_API_KEY</code></td></tr>
    </tbody>
  </table>
  <p>Flags <code>--url</code> and <code>--key</code> override the environment variables per command.</p>

  <h2>Commands</h2>

  <h3>ant list</h3>
  <p>List all sessions on the server.</p>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-cmd">ant list</span></div>
    <div><span class="line-out">ID              NAME            STATUS    CREATED</span></div>
    <div><span class="line-out">ses_abc123      my-session      active    2 min ago</span></div>
    <div><span class="line-out">ses_def456      build-run       idle      1 hr ago</span></div>
  </div>

  <h3>ant watch &lt;session-id&gt;</h3>
  <p>Stream a session's output to your terminal (read-only). Press Ctrl+C to detach.</p>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-cmd">ant watch ses_abc123</span></div>
    <div><span class="line-out">[ANT] watching ses_abc123 — Ctrl+C to detach</span></div>
  </div>

  <h3>ant share &lt;session-id&gt;</h3>
  <p>Generate a read-only share link for a session and copy it to your clipboard.</p>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-cmd">ant share ses_abc123</span></div>
    <div><span class="line-out">Share link: https://ant.example.com/share/tok_xyz789</span></div>
    <div><span class="line-out">Copied to clipboard.</span></div>
  </div>

  <h3>ant join-room &lt;room-name&gt;</h3>
  <p>Join a named chat room. Other participants (humans or AI agents) can read and post messages.</p>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-cmd">ant join-room team-debug</span></div>
    <div><span class="line-out">[ANT] joined room: team-debug</span></div>
  </div>

  <h3>ant new</h3>
  <p>Create a new session on the server and print its ID.</p>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-cmd">ant new --name "build run"</span></div>
    <div><span class="line-out">ses_ghi789</span></div>
  </div>

  <h3>ant send &lt;session-id&gt; &lt;text&gt;</h3>
  <p>Send text input to a session (equivalent to typing). Appends a carriage return automatically.</p>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-cmd">ant send ses_abc123 "npm run build"</span></div>
    <div><span class="line-out">ok</span></div>
  </div>

  <h3>ant delete &lt;session-id&gt;</h3>
  <p>Terminate and permanently delete a session.</p>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-cmd">ant delete ses_abc123</span></div>
    <div><span class="line-out">Deleted ses_abc123</span></div>
  </div>

  <h2>Global flags</h2>
  <table>
    <thead>
      <tr><th>Flag</th><th>Description</th></tr>
    </thead>
    <tbody>
      <tr><td><code>--url &lt;url&gt;</code></td><td>Server base URL (overrides <code>ANT_URL</code>)</td></tr>
      <tr><td><code>--key &lt;key&gt;</code></td><td>API key (overrides <code>ANT_API_KEY</code>)</td></tr>
      <tr><td><code>--json</code></td><td>Output raw JSON (useful for scripting)</td></tr>
      <tr><td><code>--help</code></td><td>Show help for any command</td></tr>
    </tbody>
  </table>
</div>
