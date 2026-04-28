<div class="prose">
  <h1>Self-Hosting</h1>
  <p>ANT is designed to run on hardware you control: a Mac mini, a Linux box, a workstation, or a small always-on server. It keeps agent terminals, rooms, routing state, transcripts, and evidence close to your own machine.</p>

  <h2>Production start</h2>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-cmd">git clone https://github.com/Jktfe/a-nice-terminal.git</span></div>
    <div><span class="line-cmd">cd a-nice-terminal</span></div>
    <div><span class="line-cmd">npm install</span></div>
    <div><span class="line-cmd">cp .env.example .env</span></div>
    <div><span class="line-comment"># edit .env: set ANT_API_KEY and host-specific paths</span></div>
    <div><span class="line-cmd">npm run build</span></div>
    <div><span class="line-cmd">npm run start</span></div>
  </div>

  <h2>macOS: launchd daemon</h2>
  <p>Use launchd when ANT should start at login and stay alive on a Mac. The repo includes a plist example you can copy and adapt for your path and environment.</p>

  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-comment"># Copy and edit the example plist</span></div>
    <div><span class="line-cmd">cp ant.server.plist.example ~/Library/LaunchAgents/ant.server.plist</span></div>
    <div><span class="line-cmd">nano ~/Library/LaunchAgents/ant.server.plist</span></div>
    <div style="margin-top:0.5rem;"><span class="line-comment"># Load it</span></div>
    <div><span class="line-cmd">launchctl load ~/Library/LaunchAgents/ant.server.plist</span></div>
    <div><span class="line-cmd">launchctl list | grep ant</span></div>
  </div>

  <p>Check these fields before loading the plist:</p>
  <ul>
    <li><code>WorkingDirectory</code> — absolute path to your <code>a-nice-terminal</code> clone.</li>
    <li><code>ProgramArguments</code> — usually <code>npm run start</code> or the equivalent node command.</li>
    <li><code>EnvironmentVariables</code> — include <code>ANT_API_KEY</code>, <code>ANT_PORT</code>, <code>ANT_SERVER_URL</code>, and any local paths.</li>
    <li><code>StandardOutPath</code> and <code>StandardErrorPath</code> — log file locations you can inspect later.</li>
  </ul>

  <h2>Linux: systemd service</h2>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-comment"># /etc/systemd/system/ant.service</span></div>
    <div><span class="line-out">[Unit]</span></div>
    <div><span class="line-out">Description=ANT - A Nice Terminal</span></div>
    <div><span class="line-out">After=network.target</span></div>
    <div style="margin-top:0.25rem;"><span class="line-out">[Service]</span></div>
    <div><span class="line-out">Type=simple</span></div>
    <div><span class="line-out">User=youruser</span></div>
    <div><span class="line-out">WorkingDirectory=/home/youruser/a-nice-terminal</span></div>
    <div><span class="line-out">EnvironmentFile=/home/youruser/a-nice-terminal/.env</span></div>
    <div><span class="line-out">ExecStart=/usr/bin/npm run start</span></div>
    <div><span class="line-out">Restart=on-failure</span></div>
    <div style="margin-top:0.25rem;"><span class="line-out">[Install]</span></div>
    <div><span class="line-out">WantedBy=multi-user.target</span></div>
  </div>

  <div class="code-block" style="padding:1rem 1.25rem; margin-top:0.5rem;">
    <div><span class="line-cmd">sudo systemctl daemon-reload</span></div>
    <div><span class="line-cmd">sudo systemctl enable --now ant</span></div>
    <div><span class="line-cmd">sudo systemctl status ant</span></div>
  </div>

  <h2>TLS options</h2>
  <p>For personal deployments, the simplest secure path is usually Tailscale or a reverse proxy. ANT can also use local TLS certs when <code>ANT_TLS_CERT</code> and <code>ANT_TLS_KEY</code> are set.</p>

  <h3>Caddy reverse proxy</h3>
  <p>Caddy handles public TLS certificates automatically. Run ANT on localhost, then proxy to it:</p>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-out">ant.example.com {'{'}</span></div>
    <div><span class="line-out">  reverse_proxy localhost:6458</span></div>
    <div><span class="line-out">{'}'}</span></div>
  </div>
  <p>Set <code>ANT_SERVER_URL=https://ant.example.com</code> so share links and CLI hints use the public hostname.</p>

  <h3>Built-in HTTPS</h3>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-out">ANT_TLS_CERT=/absolute/path/ant.crt</span></div>
    <div><span class="line-out">ANT_TLS_KEY=/absolute/path/ant.key</span></div>
  </div>

  <h2>Tailscale private access</h2>
  <p>Tailscale lets you reach ANT from phones, laptops, and other agents without exposing it publicly.</p>
  <ol style="color:#94A3B8; line-height:1.9; padding-left:1.25rem; margin-bottom:1rem;">
    <li>Install Tailscale on the server and client devices.</li>
    <li>Serve ANT privately, for example: <code>tailscale serve https / http://localhost:6458</code>.</li>
    <li>Set <code>ANT_SERVER_URL</code> to the Tailscale HTTPS hostname.</li>
    <li>Use <code>ANT_TAILSCALE_ONLY=true</code> if you want API access restricted to Tailscale addresses.</li>
  </ol>

  <h2>Data safety</h2>
  <ul>
    <li>Use <code>ANT_DATA_DIR</code> to keep ANT data outside the repo checkout.</li>
    <li>Back up the ANT data directory regularly, especially the SQLite database.</li>
    <li>Keep <code>.env</code>, TLS keys, and API keys out of version control.</li>
    <li>Use archive and restore for normal cleanup; hard delete only when you want the record gone.</li>
  </ul>
</div>
