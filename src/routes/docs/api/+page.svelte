<div class="prose">
  <h1>REST &amp; WebSocket API</h1>
  <p>ANT exposes a REST API for session management and a WebSocket API for real-time terminal streaming. All endpoints require the <code>ANT_API_KEY</code> header (or query param for WebSocket).</p>

  <h2>Authentication</h2>
  <p>Pass your API key in the <code>x-api-key</code> request header:</p>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-cmd">curl http://localhost:6458/api/sessions \</span></div>
    <div><span class="line-cmd">  -H "x-api-key: your-key"</span></div>
  </div>
  <p>For WebSocket connections, pass it as a query parameter: <code>?key=your-key</code></p>

  <h2>REST endpoints</h2>

  <h3>Sessions</h3>
  <table>
    <thead>
      <tr><th>Method</th><th>Path</th><th>Description</th></tr>
    </thead>
    <tbody>
      <tr><td><code>GET</code></td><td><code>/api/sessions</code></td><td>List all sessions</td></tr>
      <tr><td><code>POST</code></td><td><code>/api/sessions</code></td><td>Create a new session</td></tr>
      <tr><td><code>GET</code></td><td><code>/api/sessions/:id</code></td><td>Get a single session</td></tr>
      <tr><td><code>PATCH</code></td><td><code>/api/sessions/:id</code></td><td>Rename or update a session</td></tr>
      <tr><td><code>DELETE</code></td><td><code>/api/sessions/:id</code></td><td>Delete a session</td></tr>
    </tbody>
  </table>

  <h3>POST /api/sessions — request body</h3>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-out">{'{'}</span></div>
    <div><span class="line-out">  "name": "my session",   // optional</span></div>
    <div><span class="line-out">  "cols": 220,            // optional, default 220</span></div>
    <div><span class="line-out">  "rows": 50              // optional, default 50</span></div>
    <div><span class="line-out">{'}'}</span></div>
  </div>

  <h3>Terminal input</h3>
  <table>
    <thead>
      <tr><th>Method</th><th>Path</th><th>Description</th></tr>
    </thead>
    <tbody>
      <tr><td><code>POST</code></td><td><code>/api/sessions/:id/terminal/input</code></td><td>Send text input to a PTY session</td></tr>
      <tr><td><code>GET</code></td><td><code>/api/sessions/:id/terminal/snapshot</code></td><td>Get the current terminal state as plain text</td></tr>
    </tbody>
  </table>

  <h3>POST /api/sessions/:id/terminal/input — request body</h3>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-out">{'{ "data": "npm run build\\r" }'}</span></div>
  </div>
  <p>Include <code>\r</code> at the end to submit the command. Without it, text is typed but not executed (useful for the two-call Slow Edit pattern).</p>

  <h3>Search</h3>
  <table>
    <thead>
      <tr><th>Method</th><th>Path</th><th>Description</th></tr>
    </thead>
    <tbody>
      <tr><td><code>GET</code></td><td><code>/api/search?q=text</code></td><td>Full-text search across all session output (FTS5)</td></tr>
    </tbody>
  </table>

  <h3>Share</h3>
  <table>
    <thead>
      <tr><th>Method</th><th>Path</th><th>Description</th></tr>
    </thead>
    <tbody>
      <tr><td><code>POST</code></td><td><code>/api/sessions/:id/share</code></td><td>Create a read-only share token for a session</td></tr>
    </tbody>
  </table>

  <h2>WebSocket API</h2>
  <p>Connect to <code>ws://localhost:6458?key=your-key</code> (or <code>wss://</code> if behind TLS).</p>
  <p>All messages are JSON. Send a <code>join</code> message to subscribe to a session's output stream.</p>

  <h3>Client → server messages</h3>
  <table>
    <thead>
      <tr><th>Type</th><th>Payload</th><th>Description</th></tr>
    </thead>
    <tbody>
      <tr><td><code>join</code></td><td><code>{'{ sessionId }'}</code></td><td>Subscribe to a session's output stream</td></tr>
      <tr><td><code>leave</code></td><td><code>{'{ sessionId }'}</code></td><td>Unsubscribe from a session</td></tr>
      <tr><td><code>input</code></td><td><code>{'{ sessionId, data }'}</code></td><td>Send raw input to the PTY</td></tr>
      <tr><td><code>resize</code></td><td><code>{'{ sessionId, cols, rows }'}</code></td><td>Resize the PTY window</td></tr>
      <tr><td><code>join_room</code></td><td><code>{'{ room }'}</code></td><td>Join a named chat room</td></tr>
      <tr><td><code>post_to_room</code></td><td><code>{'{ room, text }'}</code></td><td>Post a message to a chat room</td></tr>
    </tbody>
  </table>

  <h3>Server → client messages</h3>
  <table>
    <thead>
      <tr><th>Type</th><th>Payload</th><th>Description</th></tr>
    </thead>
    <tbody>
      <tr><td><code>output</code></td><td><code>{'{ sessionId, data }'}</code></td><td>Raw PTY output (ANSI escape sequences)</td></tr>
      <tr><td><code>session_created</code></td><td><code>{'{ session }'}</code></td><td>A new session was created</td></tr>
      <tr><td><code>session_updated</code></td><td><code>{'{ session }'}</code></td><td>Session metadata changed</td></tr>
      <tr><td><code>session_deleted</code></td><td><code>{'{ sessionId }'}</code></td><td>A session was deleted</td></tr>
      <tr><td><code>room_message</code></td><td><code>{'{ room, from, text, ts }'}</code></td><td>A message posted to a joined room</td></tr>
      <tr><td><code>error</code></td><td><code>{'{ message }'}</code></td><td>Server-side error</td></tr>
    </tbody>
  </table>

  <h3>Example: connect and stream output</h3>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-cmd">const ws = new WebSocket('ws://localhost:6458?key=your-key');</span></div>
    <div style="margin-top:0.25rem;"><span class="line-out">ws.onopen = () => {'{'}</span></div>
    <div><span class="line-out">  ws.send(JSON.stringify({'{ type: "join", sessionId: "ses_abc123" }'}));</span></div>
    <div><span class="line-out">{'};'}</span></div>
    <div><span class="line-out">ws.onmessage = (e) => {'{'}</span></div>
    <div><span class="line-out">  const msg = JSON.parse(e.data);</span></div>
    <div><span class="line-out">  if (msg.type === 'output') process.stdout.write(msg.data);</span></div>
    <div><span class="line-out">{'};'}</span></div>
  </div>
</div>
