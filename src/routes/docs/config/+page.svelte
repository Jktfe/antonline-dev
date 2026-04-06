<div class="prose">
  <h1>Configuration</h1>
  <p>ANT is configured via environment variables. Copy <code>.env.example</code> to <code>.env</code> and edit before starting the server.</p>

  <h2>Environment variables</h2>

  <table>
    <thead>
      <tr>
        <th>Variable</th>
        <th>Default</th>
        <th>Description</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td><code>ANT_API_KEY</code></td>
        <td><em>required</em></td>
        <td>Secret key for WebSocket auth and REST API. Set to a long random string.</td>
      </tr>
      <tr>
        <td><code>ANT_PORT</code></td>
        <td><code>6458</code></td>
        <td>HTTP/WebSocket server port.</td>
      </tr>
      <tr>
        <td><code>ANT_DB_PATH</code></td>
        <td><code>ant.db</code></td>
        <td>Path to the SQLite database file. Relative paths resolve from the project root.</td>
      </tr>
      <tr>
        <td><code>ANT_DEFAULT_SHELL</code></td>
        <td><code>$SHELL</code></td>
        <td>Shell binary for new PTY sessions (e.g. <code>/bin/zsh</code>, <code>/bin/bash</code>).</td>
      </tr>
      <tr>
        <td><code>ANT_DEFAULT_COLS</code></td>
        <td><code>220</code></td>
        <td>Default PTY column width for new sessions.</td>
      </tr>
      <tr>
        <td><code>ANT_DEFAULT_ROWS</code></td>
        <td><code>50</code></td>
        <td>Default PTY row count for new sessions.</td>
      </tr>
      <tr>
        <td><code>ANT_MAX_SCROLLBACK</code></td>
        <td><code>5000</code></td>
        <td>xterm.js scrollback buffer size (lines). Higher values use more RAM.</td>
      </tr>
      <tr>
        <td><code>ANT_OBSIDIAN_VAULT</code></td>
        <td>—</td>
        <td>Absolute path to an Obsidian vault directory. Enables the session capture → Obsidian writer feature.</td>
      </tr>
      <tr>
        <td><code>ANT_SHARE_BASE_URL</code></td>
        <td><code>http://localhost:$ANT_PORT</code></td>
        <td>Base URL used when generating share links. Set to your public hostname (e.g. <code>https://ant.example.com</code>).</td>
      </tr>
      <tr>
        <td><code>NODE_ENV</code></td>
        <td><code>development</code></td>
        <td>Set to <code>production</code> to enable stricter error handling and disable source maps.</td>
      </tr>
    </tbody>
  </table>

  <h2>Example .env</h2>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-comment"># Required</span></div>
    <div><span class="line-cmd">ANT_API_KEY=change-me-to-a-long-random-secret</span></div>
    <div style="margin-top:0.5rem;"><span class="line-comment"># Optional — uncomment to override defaults</span></div>
    <div><span class="line-out"># ANT_PORT=6458</span></div>
    <div><span class="line-out"># ANT_DB_PATH=ant.db</span></div>
    <div><span class="line-out"># ANT_DEFAULT_SHELL=/bin/zsh</span></div>
    <div><span class="line-out"># ANT_SHARE_BASE_URL=https://ant.example.com</span></div>
    <div><span class="line-out"># ANT_OBSIDIAN_VAULT=/Users/you/Notes</span></div>
  </div>

  <h2>Security note</h2>
  <p>Never commit <code>.env</code> to version control. The repo's <code>.gitignore</code> excludes it by default. Rotate <code>ANT_API_KEY</code> if it is ever exposed — all WebSocket connections and API calls will be blocked until clients update their key.</p>

  <h2>TLS / HTTPS</h2>
  <p>ANT's built-in server is HTTP only. For TLS termination, put a reverse proxy in front (nginx, Caddy, or Tailscale's built-in HTTPS). See <a href="/docs/self-hosting">Self-Hosting</a> for a full setup guide.</p>
</div>
