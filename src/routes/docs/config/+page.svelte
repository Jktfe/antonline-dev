<div class="prose">
  <h1>Configuration</h1>
  <p>ANT is configured with environment variables. Copy <code>.env.example</code> to <code>.env</code>, set an API key, and then add only the host-specific options you need.</p>

  <h2>Required</h2>
  <table>
    <thead>
      <tr><th>Variable</th><th>Default</th><th>Description</th></tr>
    </thead>
    <tbody>
      <tr>
        <td><code>ANT_API_KEY</code></td>
        <td><em>required for external callers</em></td>
        <td>Secret key for CLI, REST, and WebSocket access. Generate one with <code>openssl rand -hex 32</code>.</td>
      </tr>
    </tbody>
  </table>

  <h2>Server binding</h2>
  <table>
    <thead>
      <tr><th>Variable</th><th>Default</th><th>Description</th></tr>
    </thead>
    <tbody>
      <tr><td><code>ANT_PORT</code></td><td><code>6458</code></td><td>HTTP/WebSocket server port.</td></tr>
      <tr><td><code>ANT_HOST</code></td><td><code>0.0.0.0</code></td><td>Server bind address.</td></tr>
      <tr><td><code>ANT_SERVER_URL</code></td><td>derived from request</td><td>Public URL used in share links, CLI instructions, and prompt injection helpers.</td></tr>
      <tr><td><code>PORT</code></td><td>falls back to <code>ANT_PORT</code></td><td>Alternative port variable for platform hosts.</td></tr>
      <tr><td><code>HOST</code></td><td>falls back to <code>ANT_HOST</code></td><td>Alternative host variable for platform hosts.</td></tr>
    </tbody>
  </table>

  <h2>Security and network access</h2>
  <table>
    <thead>
      <tr><th>Variable</th><th>Default</th><th>Description</th></tr>
    </thead>
    <tbody>
      <tr><td><code>ANT_TAILSCALE_ONLY</code></td><td><code>false</code></td><td>When <code>true</code>, restrict API requests to Tailscale addresses.</td></tr>
      <tr><td><code>ANT_ALLOW_LOOPBACK</code></td><td><code>true</code></td><td>Allow loopback requests when Tailscale-only mode is enabled.</td></tr>
      <tr><td><code>ANT_TLS_CERT</code></td><td>unset</td><td>Path to a TLS certificate for built-in HTTPS.</td></tr>
      <tr><td><code>ANT_TLS_KEY</code></td><td>unset</td><td>Path to the TLS private key for built-in HTTPS.</td></tr>
    </tbody>
  </table>

  <h2>Data and workspace paths</h2>
  <table>
    <thead>
      <tr><th>Variable</th><th>Default</th><th>Description</th></tr>
    </thead>
    <tbody>
      <tr><td><code>ANT_DATA_DIR</code></td><td><code>~/.ant-v3</code></td><td>Directory for ANT database and local operational state.</td></tr>
      <tr><td><code>ANT_ROOT_DIR</code></td><td>unset</td><td>Optional root directory for project browsing and launch contexts.</td></tr>
      <tr><td><code>ANT_OBSIDIAN_VAULT</code></td><td>auto-detected local default</td><td>Obsidian vault path for session exports, docs, and capture output.</td></tr>
      <tr><td><code>ANT_QUICK_LAUNCH_FILE</code></td><td><code>~/.ant/quick-launch.json</code></td><td>Local quick-launch preset file for terminal command buttons.</td></tr>
    </tbody>
  </table>

  <h2>Limits</h2>
  <table>
    <thead>
      <tr><th>Variable</th><th>Default</th><th>Description</th></tr>
    </thead>
    <tbody>
      <tr><td><code>ANT_MAX_ACTIVE_AGENTS</code></td><td><code>0</code></td><td>Advisory active-agent cap. <code>0</code> means unlimited.</td></tr>
    </tbody>
  </table>

  <h2>Example .env</h2>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-comment"># Required for external API/CLI access</span></div>
    <div><span class="line-cmd">ANT_API_KEY=change-me-to-a-long-random-secret</span></div>
    <div style="margin-top:0.5rem;"><span class="line-comment"># Server binding</span></div>
    <div><span class="line-out">ANT_PORT=6458</span></div>
    <div><span class="line-out">ANT_HOST=0.0.0.0</span></div>
    <div><span class="line-out">ANT_SERVER_URL=https://ant.example.com</span></div>
    <div style="margin-top:0.5rem;"><span class="line-comment"># Optional local paths</span></div>
    <div><span class="line-out">ANT_DATA_DIR=/Users/you/.ant-v3</span></div>
    <div><span class="line-out">ANT_ROOT_DIR=/Users/you/projects</span></div>
    <div><span class="line-out">ANT_OBSIDIAN_VAULT=/Users/you/Notes</span></div>
  </div>

  <h2>Security note</h2>
  <p>Never commit <code>.env</code> or API keys. Prefer Tailscale or another private network for personal deployments, and rotate <code>ANT_API_KEY</code> if it is exposed.</p>
</div>
