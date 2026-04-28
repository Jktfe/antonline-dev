<div class="prose">
  <h1>CLI Reference</h1>
  <p>The <code>ant</code> CLI is a Bun-native operator and agent interface for ANT. It can list sessions, attach to terminals, send chat messages, answer prompt cards, create tasks, flag files, and read evidence from terminal history and run events.</p>

  <h2>Installation</h2>
  <p>The CLI lives in the main repository.</p>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-cmd">cd a-nice-terminal/cli</span></div>
    <div><span class="line-cmd">bun install</span></div>
    <div><span class="line-cmd">bun link</span></div>
  </div>

  <h2>Configuration</h2>
  <table>
    <thead>
      <tr><th>Setting</th><th>Description</th></tr>
    </thead>
    <tbody>
      <tr><td><code>serverUrl</code></td><td>Base URL of the ANT server.</td></tr>
      <tr><td><code>apiKey</code></td><td>API key matching the server's <code>ANT_API_KEY</code>.</td></tr>
      <tr><td><code>handle</code></td><td>Optional operator or agent handle used as sender identity.</td></tr>
      <tr><td><code>sessionId</code></td><td>Optional default session identity for native or scripted use.</td></tr>
    </tbody>
  </table>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-cmd">ant config</span></div>
    <div><span class="line-cmd">ant config set --url https://your-host:6458 --key YOUR_KEY --handle @you</span></div>
  </div>
  <p>Per-command flags <code>--server</code>, <code>--key</code>, <code>--external</code>, and <code>--json</code> are available for scripting and external agents.</p>

  <h2>Session commands</h2>
  <table>
    <thead>
      <tr><th>Command</th><th>Purpose</th></tr>
    </thead>
    <tbody>
      <tr><td><code>ant sessions</code></td><td>List active sessions.</td></tr>
      <tr><td><code>ant sessions create --name "Dev" --type terminal</code></td><td>Create a terminal or chat session.</td></tr>
      <tr><td><code>ant sessions archive &lt;id&gt;</code></td><td>Archive a session and remove it from the active list.</td></tr>
      <tr><td><code>ant sessions delete &lt;id&gt;</code></td><td>Soft-delete a session.</td></tr>
      <tr><td><code>ant sessions export &lt;id&gt;</code></td><td>Export a session summary through the configured capture pipeline.</td></tr>
    </tbody>
  </table>

  <h2>Terminal commands</h2>
  <table>
    <thead>
      <tr><th>Command</th><th>Purpose</th></tr>
    </thead>
    <tbody>
      <tr><td><code>ant terminal &lt;id&gt;</code></td><td>Attach interactively to a PTY session.</td></tr>
      <tr><td><code>ant terminal send &lt;id&gt; --cmd "npm test"</code></td><td>Send a command and submit it.</td></tr>
      <tr><td><code>ant terminal key &lt;id&gt; ctrl-c</code></td><td>Send a special key such as <code>ctrl-c</code>, <code>enter</code>, <code>tab</code>, or arrows.</td></tr>
      <tr><td><code>ant terminal history &lt;id&gt; --since 5m --limit 50</code></td><td>Read persisted terminal history.</td></tr>
      <tr><td><code>ant terminal history &lt;id&gt; --grep "error"</code></td><td>Search terminal history.</td></tr>
      <tr><td><code>ant terminal events &lt;id&gt; --kind layout-change</code></td><td>Read tmux control-mode events.</td></tr>
    </tbody>
  </table>

  <h2>Chat and prompt commands</h2>
  <table>
    <thead>
      <tr><th>Command</th><th>Purpose</th></tr>
    </thead>
    <tbody>
      <tr><td><code>ant chat &lt;id&gt;</code></td><td>Open an interactive chat session.</td></tr>
      <tr><td><code>ant chat join &lt;id&gt;</code></td><td>Join a real-time streaming chat room.</td></tr>
      <tr><td><code>ant chat send &lt;id&gt; --msg "hello"</code></td><td>Post a message to a room.</td></tr>
      <tr><td><code>ant chat read &lt;id&gt; --limit 50</code></td><td>Read recent room history.</td></tr>
      <tr><td><code>ant chat reply &lt;id&gt; --msg "yes"</code></td><td>Reply to the latest message.</td></tr>
      <tr><td><code>ant chat pending &lt;id&gt;</code></td><td>Show a pending interactive prompt for a terminal or linked chat.</td></tr>
      <tr><td><code>ant chat decide &lt;id&gt; approve --why "safe edit"</code></td><td>Approve, deny, retry, abort, select, or send text to a prompt card.</td></tr>
      <tr><td><code>ant chat leave &lt;id&gt;</code></td><td>Remove the current session or handle from a chat room.</td></tr>
      <tr><td><code>ant msg &lt;room-id&gt; @handle "message"</code></td><td>Send a targeted message to a participant.</td></tr>
    </tbody>
  </table>

  <h2>Tasks, files, memory, and search</h2>
  <table>
    <thead>
      <tr><th>Command</th><th>Purpose</th></tr>
    </thead>
    <tbody>
      <tr><td><code>ant task &lt;id&gt; list</code></td><td>List room tasks.</td></tr>
      <tr><td><code>ant task &lt;id&gt; create "title" --desc "..."</code></td><td>Create a proposed room task.</td></tr>
      <tr><td><code>ant task &lt;id&gt; assign &lt;task-id&gt; @handle</code></td><td>Assign a task.</td></tr>
      <tr><td><code>ant task &lt;id&gt; done &lt;task-id&gt;</code></td><td>Mark a task complete.</td></tr>
      <tr><td><code>ant flag &lt;id&gt; path/to/file --note "why"</code></td><td>Add a file reference to the room.</td></tr>
      <tr><td><code>ant flag &lt;id&gt; list</code></td><td>List flagged files.</td></tr>
      <tr><td><code>ant search "query"</code></td><td>Search messages across sessions.</td></tr>
      <tr><td><code>ant memory audit</code></td><td>Report duplicate, oversize, and noisy operational memory rows.</td></tr>
    </tbody>
  </table>

  <h2>Prompt bridge</h2>
  <p>The prompt bridge lets ANT detect interactive prompts and route them to a linked room or configured destination.</p>
  <div class="code-block" style="padding:1rem 1.25rem;">
    <div><span class="line-cmd">ant prompt config --enable --target linked</span></div>
    <div><span class="line-cmd">ant prompt pending &lt;terminal-id&gt;</span></div>
    <div><span class="line-cmd">ant prompt respond &lt;terminal-id&gt; --text "yes"</span></div>
  </div>
</div>
