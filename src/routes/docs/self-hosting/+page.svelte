<div class="prose">
  <h1>Self-Hosting</h1>
  <p>ANT is designed to run on hardware you control — a Mac mini, a Linux server, or a Raspberry Pi. This guide covers running ANT as a persistent background daemon with TLS and optional Tailscale access.</p>

  <h2>Production build</h2>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-cmd">git clone https://github.com/Jktfe/a-nice-terminal.git</span></div>
    <div><span class="line-cmd">cd a-nice-terminal</span></div>
    <div><span class="line-cmd">npm install</span></div>
    <div><span class="line-cmd">npm run build</span></div>
    <div><span class="line-comment"># Start in production mode</span></div>
    <div><span class="line-cmd">NODE_ENV=production node build/index.js</span></div>
  </div>

  <h2>macOS: launchd daemon</h2>
  <p>To run ANT automatically at login and keep it alive, use a launchd plist. An example template is included at <code>vc.newmodel.ant.plist.example</code>.</p>

  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-comment"># Copy and edit the example plist</span></div>
    <div><span class="line-cmd">cp vc.newmodel.ant.plist.example ~/Library/LaunchAgents/ant.plist</span></div>
    <div><span class="line-cmd">nano ~/Library/LaunchAgents/ant.plist</span></div>
    <div style="margin-top:0.5rem;"><span class="line-comment"># Load it</span></div>
    <div><span class="line-cmd">launchctl load ~/Library/LaunchAgents/ant.plist</span></div>
    <div><span class="line-comment"># Check it started</span></div>
    <div><span class="line-cmd">launchctl list | grep ant</span></div>
  </div>

  <p>Key fields to set in the plist:</p>
  <ul>
    <li><code>WorkingDirectory</code> — absolute path to your <code>a-nice-terminal</code> clone</li>
    <li><code>EnvironmentVariables</code> — include <code>ANT_API_KEY</code>, <code>ANT_PORT</code>, etc.</li>
    <li><code>StandardOutPath</code> / <code>StandardErrorPath</code> — log file locations</li>
  </ul>

  <h2>Linux: systemd service</h2>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-comment"># /etc/systemd/system/ant.service</span></div>
    <div><span class="line-out">[Unit]</span></div>
    <div><span class="line-out">Description=ANT — A Nice Terminal</span></div>
    <div><span class="line-out">After=network.target</span></div>
    <div style="margin-top:0.25rem;"><span class="line-out">[Service]</span></div>
    <div><span class="line-out">Type=simple</span></div>
    <div><span class="line-out">User=youruser</span></div>
    <div><span class="line-out">WorkingDirectory=/home/youruser/a-nice-terminal</span></div>
    <div><span class="line-out">EnvironmentFile=/home/youruser/a-nice-terminal/.env</span></div>
    <div><span class="line-out">ExecStart=/usr/bin/node build/index.js</span></div>
    <div><span class="line-out">Restart=on-failure</span></div>
    <div style="margin-top:0.25rem;"><span class="line-out">[Install]</span></div>
    <div><span class="line-out">WantedBy=multi-user.target</span></div>
  </div>

  <div class="code-block" style="padding:1rem 1.25rem; margin-top:0.5rem;">
    <div><span class="line-cmd">sudo systemctl daemon-reload</span></div>
    <div><span class="line-cmd">sudo systemctl enable --now ant</span></div>
    <div><span class="line-cmd">sudo systemctl status ant</span></div>
  </div>

  <h2>TLS with Caddy</h2>
  <p>Caddy handles TLS certificates automatically via Let's Encrypt. Install Caddy, then create a <code>Caddyfile</code>:</p>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-out">ant.example.com {'{'}</span></div>
    <div><span class="line-out">  reverse_proxy localhost:6458</span></div>
    <div><span class="line-out">{'}'}</span></div>
  </div>
  <div class="code-block" style="padding:1rem 1.25rem; margin-top:0.5rem;">
    <div><span class="line-cmd">caddy run --config Caddyfile</span></div>
  </div>
  <p>Update <code>ANT_SHARE_BASE_URL=https://ant.example.com</code> in your <code>.env</code> so share links use the public hostname.</p>

  <h2>Tailscale (zero-config private access)</h2>
  <p>Tailscale lets you access ANT from anywhere on your tailnet without exposing it to the public internet.</p>
  <ol style="color:#94A3B8; line-height:1.9; padding-left:1.25rem; margin-bottom:1rem;">
    <li>Install Tailscale on both the server and your client devices.</li>
    <li>Enable Tailscale HTTPS: <code>tailscale serve https / http://localhost:6458</code></li>
    <li>Set <code>ANT_SHARE_BASE_URL</code> to your Tailscale device name (e.g. <code>https://myserver.tail1234.ts.net</code>).</li>
    <li>Access ANT at <code>https://myserver.tail1234.ts.net</code> from any device on your tailnet.</li>
  </ol>

  <h2>Keeping the database safe</h2>
  <ul>
    <li>ANT uses SQLite in WAL mode — safe for concurrent reads and crash recovery.</li>
    <li>Back up <code>ant.db</code> regularly (<code>sqlite3 ant.db .dump > backup.sql</code>).</li>
    <li>Use <code>ANT_DB_PATH</code> to store the database outside the project directory (e.g. <code>/var/lib/ant/ant.db</code>).</li>
  </ul>
</div>
