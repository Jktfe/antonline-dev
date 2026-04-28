<div class="prose">
  <h1>REST &amp; WebSocket API</h1>
  <p>ANT exposes a REST and WebSocket surface for tools that need to read or control the operational graph: sessions, rooms, participants, messages, prompt state, terminal history, run events, tasks, file refs, and health.</p>

  <h2>Authentication</h2>
  <p>When <code>ANT_API_KEY</code> is configured, external callers can authenticate with <code>Authorization: Bearer &lt;key&gt;</code>, <code>X-API-Key: &lt;key&gt;</code>, or <code>?apiKey=&lt;key&gt;</code>. Same-origin browser calls are handled by the app.</p>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-cmd">curl http://localhost:6458/api/sessions \</span></div>
    <div><span class="line-cmd">  -H "Authorization: Bearer YOUR_KEY"</span></div>
  </div>

  <h2>Sessions</h2>
  <table>
    <thead>
      <tr><th>Method</th><th>Path</th><th>Description</th></tr>
    </thead>
    <tbody>
      <tr><td><code>GET</code></td><td><code>/api/sessions</code></td><td>List active sessions plus recoverable archived/deleted rows.</td></tr>
      <tr><td><code>POST</code></td><td><code>/api/sessions</code></td><td>Create a terminal, chat, or agent session.</td></tr>
      <tr><td><code>GET</code></td><td><code>/api/sessions/:id</code></td><td>Resolve session identity and metadata.</td></tr>
      <tr><td><code>PATCH</code></td><td><code>/api/sessions/:id</code></td><td>Rename, archive, relink, or update session metadata.</td></tr>
      <tr><td><code>DELETE</code></td><td><code>/api/sessions/:id</code></td><td>Soft-delete a session. Use <code>?hard=true</code> for permanent delete.</td></tr>
      <tr><td><code>POST</code></td><td><code>/api/sessions/:id/restore</code></td><td>Restore an archived or recoverable deleted session.</td></tr>
      <tr><td><code>PATCH</code></td><td><code>/api/sessions/:id/handle</code></td><td>Set a public handle or display name.</td></tr>
      <tr><td><code>PATCH</code></td><td><code>/api/sessions/:id/cli-flag</code></td><td>Set the agent CLI driver used for event detection and stripping.</td></tr>
    </tbody>
  </table>

  <h3>Create a session</h3>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-out">{'{'}</span></div>
    <div><span class="line-out">  "name": "codex",</span></div>
    <div><span class="line-out">  "type": "terminal",</span></div>
    <div><span class="line-out">  "root_dir": "/path/to/workspace",</span></div>
    <div><span class="line-out">  "meta": {'{ "project": "ant" }'}</span></div>
    <div><span class="line-out">{'}'}</span></div>
  </div>
  <p>Creating a terminal session also creates and links a private chat session for coordination.</p>

  <h2>Messages and routing</h2>
  <table>
    <thead>
      <tr><th>Method</th><th>Path</th><th>Description</th></tr>
    </thead>
    <tbody>
      <tr><td><code>GET</code></td><td><code>/api/sessions/:id/messages</code></td><td>Read room history. Supports <code>limit</code>, <code>since</code>, and <code>before</code>.</td></tr>
      <tr><td><code>POST</code></td><td><code>/api/sessions/:id/messages</code></td><td>Post a room message and trigger ANT routing.</td></tr>
      <tr><td><code>PATCH</code></td><td><code>/api/sessions/:id/messages?msgId=...</code></td><td>Merge message metadata such as reactions, bookmarks, or event status.</td></tr>
      <tr><td><code>DELETE</code></td><td><code>/api/sessions/:id/messages?msgId=...</code></td><td>Delete a message.</td></tr>
      <tr><td><code>PATCH</code></td><td><code>/api/sessions/:id/messages/:msgId/pin</code></td><td>Pin or unpin a message.</td></tr>
      <tr><td><code>GET</code></td><td><code>/api/sessions/:id/messages/search?q=...</code></td><td>Search one room's messages.</td></tr>
      <tr><td><code>GET</code></td><td><code>/api/search?q=...</code></td><td>Search all indexed messages.</td></tr>
    </tbody>
  </table>

  <h3>Post a message</h3>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-out">{'{'}</span></div>
    <div><span class="line-out">  "role": "user",</span></div>
    <div><span class="line-out">  "content": "@codex run the tests",</span></div>
    <div><span class="line-out">  "format": "text",</span></div>
    <div><span class="line-out">  "sender_id": "@james"</span></div>
    <div><span class="line-out">{'}'}</span></div>
  </div>
  <p>The response includes the persisted message and delivery attempts for that route. Historical delivery logs are currently internal.</p>

  <h2>Participants, presence, and reads</h2>
  <table>
    <thead>
      <tr><th>Method</th><th>Path</th><th>Description</th></tr>
    </thead>
    <tbody>
      <tr><td><code>GET</code></td><td><code>/api/sessions/:id/participants</code></td><td>List room participants and message-derived posters.</td></tr>
      <tr><td><code>POST</code></td><td><code>/api/sessions/:id/participants</code></td><td>Add a session or handle to a chat room.</td></tr>
      <tr><td><code>DELETE</code></td><td><code>/api/sessions/:id/participants</code></td><td>Mark a participant as left.</td></tr>
      <tr><td><code>GET</code></td><td><code>/api/presence/:sessionId</code></td><td>Read WebSocket presence for handles joined to a room.</td></tr>
      <tr><td><code>POST</code></td><td><code>/api/sessions/:id/typing</code></td><td>Broadcast typing state.</td></tr>
      <tr><td><code>GET</code></td><td><code>/api/sessions/:id/reads</code></td><td>Read receipts for all messages in a room.</td></tr>
      <tr><td><code>POST</code></td><td><code>/api/sessions/:id/messages/:msgId/read</code></td><td>Mark one message read.</td></tr>
    </tbody>
  </table>

  <h2>Room links</h2>
  <p>Room links are typed relationships between chat sessions. They power discussions, summaries, spawned rooms, and follow-up rooms without forcing a strict hierarchy.</p>
  <table>
    <thead>
      <tr><th>Method</th><th>Path</th><th>Description</th></tr>
    </thead>
    <tbody>
      <tr><td><code>GET</code></td><td><code>/api/sessions/:id/links</code></td><td>List outgoing and incoming room links.</td></tr>
      <tr><td><code>POST</code></td><td><code>/api/sessions/:id/links</code></td><td>Create a new linked discussion or link an existing room.</td></tr>
      <tr><td><code>DELETE</code></td><td><code>/api/sessions/:id/links?linkId=...</code></td><td>Remove a room relationship.</td></tr>
    </tbody>
  </table>
  <p>Supported relationship values are <code>discussion_of</code>, <code>promoted_summary_for</code>, <code>spawned_from</code>, and <code>follows_up</code>.</p>

  <h2>Prompts, approvals, and status</h2>
  <table>
    <thead>
      <tr><th>Method</th><th>Path</th><th>Description</th></tr>
    </thead>
    <tbody>
      <tr><td><code>GET</code></td><td><code>/api/sessions/:id/status</code></td><td>Read needs-input state, route metadata, linked chat, and agent status.</td></tr>
      <tr><td><code>POST</code></td><td><code>/api/sessions/:linkedChatId/messages</code></td><td>Send <code>msg_type: "agent_response"</code> to approve, deny, select, or respond to a prompt card.</td></tr>
      <tr><td><code>GET</code></td><td><code>/api/sessions/:id/prompt-bridge/pending</code></td><td>Read generic prompt bridge pending state.</td></tr>
      <tr><td><code>POST</code></td><td><code>/api/sessions/:id/prompt-bridge/respond</code></td><td>Inject a raw prompt response into a terminal.</td></tr>
      <tr><td><code>GET</code></td><td><code>/api/prompt-bridge/config</code></td><td>Read prompt bridge configuration.</td></tr>
      <tr><td><code>PUT</code></td><td><code>/api/prompt-bridge/config</code></td><td>Enable or update prompt bridge routing.</td></tr>
    </tbody>
  </table>

  <h2>Evidence surfaces</h2>
  <table>
    <thead>
      <tr><th>Method</th><th>Path</th><th>Description</th></tr>
    </thead>
    <tbody>
      <tr><td><code>GET</code></td><td><code>/api/sessions/:id/run-events</code></td><td>Read interpreted evidence events. Supports <code>since</code>, <code>source</code>, <code>kind</code>, <code>q</code>, and <code>limit</code>.</td></tr>
      <tr><td><code>GET</code></td><td><code>/api/sessions/:id/terminal/history</code></td><td>Read terminal transcript chunks. Supports <code>since</code>, <code>grep</code>, <code>raw</code>, and <code>limit</code>.</td></tr>
      <tr><td><code>GET</code></td><td><code>/api/sessions/:id/terminal/events</code></td><td>Read tmux control-mode events.</td></tr>
      <tr><td><code>GET</code></td><td><code>/api/sessions/:id/digest</code></td><td>Read a lightweight extractive room digest.</td></tr>
      <tr><td><code>POST</code></td><td><code>/api/sessions/:id/export</code></td><td>Export a session summary through the configured capture pipeline.</td></tr>
    </tbody>
  </table>

  <h2>Tasks and file references</h2>
  <table>
    <thead>
      <tr><th>Method</th><th>Path</th><th>Description</th></tr>
    </thead>
    <tbody>
      <tr><td><code>GET</code></td><td><code>/api/sessions/:id/tasks</code></td><td>List room tasks.</td></tr>
      <tr><td><code>POST</code></td><td><code>/api/sessions/:id/tasks</code></td><td>Create a room task.</td></tr>
      <tr><td><code>PATCH</code></td><td><code>/api/sessions/:id/tasks/:taskId</code></td><td>Update task status, assignment, description, or file refs.</td></tr>
      <tr><td><code>DELETE</code></td><td><code>/api/sessions/:id/tasks/:taskId</code></td><td>Soft-delete a task.</td></tr>
      <tr><td><code>GET</code></td><td><code>/api/sessions/:id/file-refs</code></td><td>List flagged files for a room.</td></tr>
      <tr><td><code>POST</code></td><td><code>/api/sessions/:id/file-refs</code></td><td>Add a file reference.</td></tr>
      <tr><td><code>DELETE</code></td><td><code>/api/sessions/:id/file-refs?refId=...</code></td><td>Remove a file reference.</td></tr>
    </tbody>
  </table>

  <h2>Health and terminal control</h2>
  <table>
    <thead>
      <tr><th>Surface</th><th>Description</th></tr>
    </thead>
    <tbody>
      <tr><td><code>GET /api/health</code></td><td>Read status, version, resource pressure, active session count, cap, and stalled sessions.</td></tr>
      <tr><td><code>POST /api/sessions/:id/terminal/input</code></td><td>Write raw bytes to a terminal PTY.</td></tr>
      <tr><td><code>WebSocket /ws</code></td><td>Join sessions, stream terminal output, send terminal input, resize terminals, receive chat and status broadcasts.</td></tr>
    </tbody>
  </table>

  <h2>WebSocket events</h2>
  <p>Connect to <code>ws://localhost:6458/ws?apiKey=YOUR_KEY</code> or <code>wss://</code> behind TLS. The browser and CLI use this same live channel for terminal and room updates.</p>
  <p>Common server events include <code>terminal_output</code>, <code>message_created</code>, <code>message_updated</code>, <code>message_deleted</code>, <code>message_read</code>, <code>task_created</code>, <code>task_updated</code>, <code>file_ref_created</code>, <code>room_link_created</code>, <code>typing</code>, <code>agent_status_updated</code>, <code>session_needs_input</code>, <code>session_input_resolved</code>, <code>prompt_detected</code>, and <code>sessions_changed</code>.</p>

  <h2>Consumer caveats</h2>
  <ul>
    <li>REST and WebSocket are the stable integration surfaces. CLI output is operator UX unless <code>--json</code> is used.</li>
    <li>Delivery attempts are returned from message POSTs, but historical delivery logs are not exposed as a public read API yet.</li>
    <li>Presence exists for WebSocket clients that joined with handles; typing is broadcast-only and not durable.</li>
    <li>ANT exposes lifecycle/resource facts, but it does not currently expose a public recurrence or scheduled-workflow API.</li>
  </ul>
</div>
