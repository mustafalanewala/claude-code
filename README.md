# Claude Code

Claude Code is Anthropic's official CLI for Claude — an AI-powered coding assistant that runs in your terminal. It can read files, write code, run shell commands, search your codebase, browse the web, and autonomously complete complex multi-step engineering tasks.

---

## What It Does

| Capability        | Description                                            |
| ----------------- | ------------------------------------------------------ |
| Code editing      | Read, write, and edit files with surgical precision    |
| Shell execution   | Run bash/PowerShell with AST-based security validation |
| Codebase search   | Glob, grep, and LSP-powered code navigation            |
| Multi-agent       | Spawn sub-agents and teams for parallel tasks          |
| MCP support       | Connect to Model Context Protocol servers              |
| Web browsing      | Fetch URLs and search the web                          |
| Jupyter notebooks | Read and edit `.ipynb` files                           |
| Git integration   | Commit, branch, PR creation workflows                  |
| Skills & Plugins  | Extend behavior with custom skills and plugins         |
| Remote agents     | Run agents in the cloud via RemoteTrigger              |
| Voice input       | Voice-to-text for prompts                              |
| Vim mode          | Full vim keybindings in the terminal                   |
| LSP integration   | Language Server Protocol for diagnostics               |
| Computer use      | Screen automation support                              |
| Swarm mode        | Multi-agent team collaboration                         |

---

## Prerequisites

- **Bun** runtime — [bun.sh](https://bun.sh)
- **Anthropic API key** OR **AWS Bedrock** credentials

```bash
# Install Bun (Linux/Mac)
curl -fsSL https://bun.sh/install | bash

# Install Bun (Windows)
powershell -c "irm bun.sh/install.ps1 | iex"
```

---

## Setup

```bash
# Install dependencies
bun install

# Start
bun src/main.tsx
```

> **Warning:** `@ant/computer-use-mcp`, `@ant/claude-for-chrome-mcp`, `@ant/computer-use-swift`, `@anthropic-ai/mcpb` are **private Anthropic-internal packages** not on npm. You may need to remove or stub those imports.

---

## Authentication

**Option 1 — Anthropic API:**

```bash
export ANTHROPIC_API_KEY=sk-ant-...
bun src/main.tsx
# or run /login inside the REPL
```

**Option 2 — AWS Bedrock:**

```bash
export AWS_REGION=us-east-1
export AWS_ACCESS_KEY_ID=...
export AWS_SECRET_ACCESS_KEY=...
# Optional custom endpoint:
export ANTHROPIC_BEDROCK_BASE_URL=https://...
```

---

## Configuration

Settings are loaded in this priority order (later overrides earlier):

| Priority | Source            | Location                      |
| -------- | ----------------- | ----------------------------- |
| 1        | User settings     | `~/.claude/settings.json`     |
| 2        | Project settings  | `.claude/settings.json`       |
| 3        | Local settings    | `.claude/settings.local.json` |
| 4        | CLI flag          | `--settings <path>`           |
| 5        | Policy/enterprise | `managed-settings.json`       |

Schema: https://json.schemastore.org/claude-code-settings.json

---

## Key Environment Variables

| Variable                         | Description                        |
| -------------------------------- | ---------------------------------- |
| `ANTHROPIC_API_KEY`              | Anthropic API key                  |
| `AWS_REGION`                     | AWS region for Bedrock             |
| `AWS_ACCESS_KEY_ID`              | AWS access key                     |
| `AWS_SECRET_ACCESS_KEY`          | AWS secret key                     |
| `ANTHROPIC_BEDROCK_BASE_URL`     | Custom Bedrock endpoint            |
| `CLAUDE_CODE_CUSTOM_OAUTH_URL`   | Custom OAuth endpoint              |
| `CLAUDE_CODE_OAUTH_CLIENT_ID`    | OAuth client ID override           |
| `CLAUDE_CODE_SIMPLE`             | Enable simple/minimal mode         |
| `CLAUDE_CODE_ENTRYPOINT`         | Attribution entrypoint label       |
| `CLAUDE_CODE_ATTRIBUTION_HEADER` | Toggle billing header (default on) |
| `SHELL`                          | Shell used for command execution   |

---

## Project Structure

```
claude-code/
├── src/
│   ├── main.tsx                        # App entry point (Bun runtime)
│   ├── QueryEngine.ts                  # LLM query execution engine
│   ├── Tool.ts                         # Tool base class
│   ├── Task.ts                         # Task base class
│   ├── commands.ts                     # Command registry and dispatcher
│   ├── context.ts                      # Context management
│   ├── query.ts                        # Query processing utilities
│   ├── tasks.ts                        # Task management utilities
│   ├── tools.ts                        # Tool utilities
│   ├── history.ts                      # Session/command history
│   ├── cost-tracker.ts                 # Token and cost tracking
│   ├── costHook.ts                     # Cost tracking hooks
│   ├── setup.ts                        # App setup and initialization
│   ├── ink.ts                          # Ink renderer exports + ThemeProvider
│   ├── projectOnboardingState.ts       # Project onboarding state
│   ├── replLauncher.tsx                # REPL launcher
│   ├── dialogLaunchers.tsx             # Dialog launcher utilities
│   ├── interactiveHelpers.tsx          # Interactive helper utilities
│   │
│   ├── assistant/
│   │   └── sessionHistory.ts           # Conversation history management
│   │
│   ├── bootstrap/
│   │   └── state.ts                    # Bootstrap and initial app state
│   │
│   ├── bridge/                         # Desktop app <-> CLI communication
│   │   ├── bridgeApi.ts                # Bridge API interface
│   │   ├── bridgeConfig.ts             # Bridge configuration
│   │   ├── bridgeMain.ts               # Bridge coordinator
│   │   ├── bridgeMessaging.ts          # Message passing layer
│   │   ├── bridgeUI.ts                 # Bridge UI layer
│   │   ├── bridgeDebug.ts              # Debug utilities
│   │   ├── bridgeEnabled.ts            # Bridge feature flag
│   │   ├── bridgePermissionCallbacks.ts# Permission callback bridge
│   │   ├── bridgePointer.ts            # Pointer/session reference
│   │   ├── bridgeStatusUtil.ts         # Status utilities
│   │   ├── capacityWake.ts             # Capacity wake management
│   │   ├── codeSessionApi.ts           # Code session API
│   │   ├── createSession.ts            # Session creation
│   │   ├── debugUtils.ts               # Debug helpers
│   │   ├── envLessBridgeConfig.ts      # Config without env vars
│   │   ├── flushGate.ts                # Message flush gate
│   │   ├── inboundAttachments.ts       # Attachment ingestion + sanitization
│   │   ├── inboundMessages.ts          # Inbound message handling
│   │   ├── initReplBridge.ts           # REPL bridge initialization
│   │   ├── jwtUtils.ts                 # JWT decode, refresh scheduling
│   │   ├── pollConfig.ts               # Polling configuration
│   │   ├── pollConfigDefaults.ts       # Default poll config
│   │   ├── remoteBridgeCore.ts         # Remote bridge core
│   │   ├── replBridge.ts               # Main REPL bridge
│   │   ├── replBridgeHandle.ts         # REPL bridge handle
│   │   ├── replBridgeTransport.ts      # REPL bridge transport
│   │   ├── sessionIdCompat.ts          # Session ID compatibility
│   │   ├── sessionRunner.ts            # Session lifecycle
│   │   ├── trustedDevice.ts            # Device trust management
│   │   ├── types.ts                    # Bridge type definitions
│   │   └── workSecret.ts               # Work session secrets
│   │
│   ├── buddy/                          # AI companion sprite
│   │   ├── CompanionSprite.tsx         # Visual companion component
│   │   ├── companion.ts                # Companion state and logic
│   │   ├── useBuddyNotification.tsx    # Companion notification hook
│   │   ├── sprites.ts                  # Sprite definitions
│   │   ├── prompt.ts                   # Companion prompts
│   │   └── types.ts                    # Type definitions
│   │
│   ├── cli/                            # CLI interface layer
│   │   ├── exit.ts                     # CLI exit handling
│   │   ├── print.ts                    # Output printing
│   │   ├── remoteIO.ts                 # Remote I/O
│   │   ├── structuredIO.ts             # Structured NDJSON output
│   │   ├── update.ts                   # CLI auto-update
│   │   ├── ndjsonSafeStringify.ts      # Safe NDJSON serialization
│   │   ├── handlers/
│   │   │   ├── agents.ts               # Agent command handler
│   │   │   ├── auth.ts                 # Auth handler
│   │   │   ├── autoMode.ts             # Auto/headless mode
│   │   │   ├── mcp.tsx                 # MCP handler
│   │   │   ├── plugins.ts              # Plugin handler
│   │   │   └── util.tsx                # Handler utilities
│   │   └── transports/
│   │       ├── HybridTransport.ts      # Hybrid transport
│   │       ├── SSETransport.ts         # Server-Sent Events transport
│   │       ├── WebSocketTransport.ts   # WebSocket transport
│   │       ├── SerialBatchEventUploader.ts # Event batching + upload
│   │       ├── WorkerStateUploader.ts  # Worker state sync
│   │       ├── ccrClient.ts            # CCR client
│   │       └── transportUtils.ts       # Transport utilities
│   │
│   ├── commands/                       # 101 slash command implementations
│   │   ├── add-dir/                    # Add directory to context
│   │   ├── agents/                     # Manage agents
│   │   ├── ant-trace/                  # Internal tracing
│   │   ├── autofix-pr/                 # Auto-fix PR issues
│   │   ├── backfill-sessions/          # Backfill session data
│   │   ├── branch/                     # Git branch operations
│   │   ├── break-cache/                # Break prompt cache
│   │   ├── bridge/                     # Bridge mode
│   │   ├── btw/                        # Background task
│   │   ├── bughunter/                  # Bug hunting mode
│   │   ├── chrome/                     # Chrome integration
│   │   ├── clear/                      # Clear conversation
│   │   ├── color/                      # Color theme
│   │   ├── compact/                    # Compress conversation
│   │   ├── config/                     # Settings management
│   │   ├── context/                    # Context management
│   │   ├── copy/                       # Copy output
│   │   ├── cost/                       # Show cost/token usage
│   │   ├── ctx_viz/                    # Context visualization
│   │   ├── debug-tool-call/            # Debug tool calls
│   │   ├── desktop/                    # Desktop integration
│   │   ├── diff/                       # Show git diff
│   │   ├── doctor/                     # Run diagnostics
│   │   ├── effort/                     # Effort estimation
│   │   ├── env/                        # Environment variables
│   │   ├── exit/                       # Exit CLI
│   │   ├── export/                     # Export conversation
│   │   ├── extra-usage/                # Extended usage stats
│   │   ├── fast/                       # Toggle fast mode
│   │   ├── feedback/                   # Send feedback
│   │   ├── files/                      # File operations
│   │   ├── good-claude/                # Positive feedback
│   │   ├── heapdump/                   # Memory heap dump
│   │   ├── help/                       # Help system
│   │   ├── hooks/                      # Manage hooks
│   │   ├── ide/                        # IDE integration
│   │   ├── install-github-app/         # Install GitHub App
│   │   ├── install-slack-app/          # Install Slack App
│   │   ├── issue/                      # Create GitHub issue
│   │   ├── keybindings/                # Configure keybindings
│   │   ├── login/                      # Authenticate
│   │   ├── logout/                     # Sign out
│   │   ├── mcp/                        # MCP server management
│   │   ├── memory/                     # Memory management
│   │   ├── mobile/                     # Mobile integration
│   │   ├── mock-limits/                # Mock rate limits (dev)
│   │   ├── model/                      # Switch AI model
│   │   ├── oauth-refresh/              # Refresh OAuth token
│   │   ├── onboarding/                 # Project onboarding
│   │   ├── output-style/               # Output format style
│   │   ├── passes/                     # Multi-pass execution
│   │   ├── perf-issue/                 # Report perf issue
│   │   ├── permissions/                # Manage permissions
│   │   ├── plan/                       # Enter plan mode
│   │   ├── plugin/                     # Plugin management
│   │   ├── pr_comments/                # PR comments
│   │   ├── privacy-settings/           # Privacy configuration
│   │   ├── rate-limit-options/         # Rate limit settings
│   │   ├── release-notes/              # View release notes
│   │   ├── reload-plugins/             # Reload plugins
│   │   ├── remote-env/                 # Remote environment
│   │   ├── remote-setup/               # Remote agent setup
│   │   ├── rename/                     # Rename session
│   │   ├── reset-limits/               # Reset usage limits
│   │   ├── resume/                     # Resume session
│   │   ├── review/                     # Code review
│   │   ├── rewind/                     # Rewind conversation
│   │   ├── sandbox-toggle/             # Toggle sandbox mode
│   │   ├── session/                    # Session management
│   │   ├── share/                      # Share conversation
│   │   ├── skills/                     # Skills management
│   │   ├── stats/                      # Usage statistics
│   │   ├── status/                     # System status
│   │   ├── stickers/                   # Sticker rewards
│   │   ├── summary/                    # Conversation summary
│   │   ├── tag/                        # Tag session
│   │   ├── tasks/                      # Background tasks
│   │   ├── teleport/                   # Repository teleport
│   │   ├── terminalSetup/              # Terminal setup
│   │   ├── theme/                      # UI theme
│   │   ├── thinkback/                  # Thinkback mode
│   │   ├── thinkback-play/             # Replay thinkback
│   │   ├── upgrade/                    # Upgrade CLI
│   │   ├── usage/                      # Token/cost usage
│   │   ├── vim/                        # Vim mode toggle
│   │   └── voice/                      # Voice input
│   │
│   ├── components/                     # React/Ink terminal UI components
│   │   ├── App.tsx                     # Root app component
│   │   ├── PromptInput/                # Terminal input component
│   │   ├── Settings/                   # Settings UI
│   │   ├── Spinner/                    # Loading spinner
│   │   ├── StructuredDiff/             # Diff renderer
│   │   ├── TrustDialog/                # Trust confirmation dialog
│   │   ├── ManagedSettingsSecurityDialog/ # MDM security dialog
│   │   ├── agents/                     # Agent management UI
│   │   │   ├── AgentsList              # List of configured agents
│   │   │   ├── AgentEditor             # Edit agent settings
│   │   │   ├── ColorPicker             # Color selector
│   │   │   ├── ModelSelector           # Model picker
│   │   │   ├── ToolSelector            # Tool access picker
│   │   │   └── new-agent-creation/     # Agent creation wizard
│   │   ├── design-system/              # Reusable UI primitives
│   │   │   ├── ThemeProvider           # Theme context
│   │   │   ├── ThemedBox               # Themed box component
│   │   │   ├── ThemedText              # Themed text component
│   │   │   ├── Dialog                  # Modal dialog
│   │   │   ├── Tabs                    # Tab navigation
│   │   │   ├── ProgressBar             # Progress indicator
│   │   │   ├── KeyboardShortcutHint    # Shortcut display
│   │   │   └── LoadingState            # Loading indicator
│   │   ├── diff/                       # Diff visualization
│   │   │   ├── DiffDetailView          # Full diff view
│   │   │   ├── DiffDialog              # Diff dialog
│   │   │   └── DiffFileList            # File list with diffs
│   │   ├── grove/                      # Grove tree visualization
│   │   ├── mcp/                        # MCP server UI
│   │   ├── memory/                     # Memory UI
│   │   │   ├── MemoryFileSelector      # Pick memory files
│   │   │   └── MemoryUpdateNotification# Memory change alerts
│   │   ├── messages/                   # Message rendering
│   │   │   ├── AssistantTextMessage    # Claude text output
│   │   │   ├── AssistantToolUseMessage # Tool call display
│   │   │   ├── AssistantThinkingMessage# Thinking/reasoning display
│   │   │   ├── UserBashInputMessage    # User bash input
│   │   │   └── SystemTextMessage       # System messages
│   │   ├── permissions/                # Permission request dialogs
│   │   │   ├── BashPermissionRequest   # Bash command approval
│   │   │   ├── FileEditPermissionRequest # File edit approval
│   │   │   ├── FileWritePermissionRequest# File write approval
│   │   │   ├── WebFetchPermissionRequest # Web fetch approval
│   │   │   └── SkillPermissionRequest  # Skill execution approval
│   │   ├── shell/                      # Shell output rendering
│   │   │   ├── OutputLine              # Single output line
│   │   │   ├── ShellProgressMessage    # Progress display
│   │   │   └── ShellTimeDisplay        # Execution time
│   │   ├── skills/                     # Skills menu UI
│   │   ├── tasks/                      # Task UI
│   │   │   ├── BackgroundTask          # Background task display
│   │   │   ├── DreamDetailDialog       # Dream task details
│   │   │   ├── RemoteSessionProgress   # Remote task progress
│   │   │   └── ShellProgress           # Shell task progress
│   │   ├── teams/                      # Team/swarm UI
│   │   │   ├── TeamsDialog             # Team management dialog
│   │   │   └── TeamStatus              # Team status display
│   │   ├── ui/                         # Base UI utilities
│   │   │   ├── OrderedList             # Numbered list
│   │   │   └── TreeSelect              # Tree selection
│   │   └── wizard/                     # Wizard framework
│   │       ├── WizardProvider          # Wizard context
│   │       └── WizardNavigationFooter  # Wizard nav footer
│   │
│   ├── constants/                      # App-wide constants
│   │   ├── apiLimits.ts                # API rate/size limits
│   │   ├── betas.ts                    # Beta feature flags
│   │   ├── common.ts                   # Shared constants
│   │   ├── errorIds.ts                 # Error ID mappings
│   │   ├── figures.ts                  # Unicode/ASCII characters
│   │   ├── messages.ts                 # UI message strings
│   │   ├── oauth.ts                    # OAuth config + URL allowlist
│   │   ├── outputStyles.ts             # Output style definitions
│   │   ├── product.ts                  # Product URLs and info
│   │   ├── prompts.ts                  # System prompt templates
│   │   ├── system.ts                   # System config + version
│   │   ├── systemPromptSections.ts     # Prompt section templates
│   │   ├── toolLimits.ts               # Per-tool limits
│   │   ├── tools.ts                    # Tool registry constants
│   │   └── xml.ts                      # XML parsing constants
│   │
│   ├── context/                        # React context providers
│   │   ├── QueuedMessageContext.tsx    # Message queue context
│   │   ├── fpsMetrics.tsx              # FPS performance context
│   │   ├── mailbox.tsx                 # Mailbox message context
│   │   ├── modalContext.tsx            # Modal dialog context
│   │   ├── notifications.tsx           # Notification context
│   │   ├── overlayContext.tsx          # Overlay UI context
│   │   ├── promptOverlayContext.tsx    # Prompt overlay context
│   │   ├── stats.tsx                   # Statistics context
│   │   └── voice.tsx                   # Voice context
│   │
│   ├── coordinator/
│   │   └── coordinatorMode.ts          # Multi-agent coordinator mode
│   │
│   ├── entrypoints/                    # App entry points
│   │   ├── cli.tsx                     # CLI entry point
│   │   ├── init.ts                     # Init: auth, telemetry, proxy, certs
│   │   ├── mcp.ts                      # MCP server entry point
│   │   ├── agentSdkTypes.ts            # Agent SDK type exports
│   │   ├── sandboxTypes.ts             # Sandbox type exports
│   │   └── sdk/
│   │       ├── controlSchemas.ts       # Control flow schemas
│   │       ├── coreSchemas.ts          # Core data schemas
│   │       └── coreTypes.ts            # Core type definitions
│   │
│   ├── hooks/                          # 85+ custom React hooks
│   │   ├── useSettings.ts              # Settings state
│   │   ├── useVoice.ts                 # Voice mode
│   │   ├── useVim.ts                   # Vim mode
│   │   ├── useGlobalKeybindings.tsx    # Global keyboard shortcuts
│   │   ├── useIDEIntegration.tsx       # IDE bridge integration
│   │   ├── useSwarmInitialization.ts   # Swarm/team setup
│   │   ├── useTasksV2.ts               # Task management
│   │   ├── useRemoteSession.ts         # Remote session handling
│   │   ├── useTeleportResume.tsx       # Teleport session resume
│   │   ├── useSearchInput.ts           # Search input handler
│   │   ├── useHistorySearch.ts         # Command history search
│   │   ├── useMemoryUsage.ts           # Memory usage tracking
│   │   ├── useScheduledTasks.ts        # Scheduled task hook
│   │   ├── notifs/                     # Notification hooks
│   │   │   ├── useStartupNotification
│   │   │   ├── useRateLimitWarningNotification
│   │   │   ├── useFastModeNotification
│   │   │   ├── useModelMigrationNotifications
│   │   │   └── useIDEStatusIndicator
│   │   └── toolPermission/             # Tool permission hooks
│   │       ├── PermissionContext.ts    # Permission context
│   │       ├── permissionLogging.ts    # Permission audit log
│   │       └── handlers/               # Permission handlers
│   │           ├── coordinator         # Coordinator permissions
│   │           ├── interactive         # Interactive permissions
│   │           └── swarmWorker         # Swarm worker permissions
│   │
│   ├── ink/                            # Custom terminal rendering engine
│   │   ├── ink.tsx                     # Main Ink component
│   │   ├── render-to-screen.ts         # Screen rendering
│   │   ├── renderer.ts                 # Core renderer
│   │   ├── terminal.ts                 # Terminal abstraction
│   │   ├── frame.ts                    # Frame management
│   │   ├── reconciler.ts               # React reconciler
│   │   ├── dom.ts                      # Virtual DOM for terminal
│   │   ├── styles.ts                   # Terminal styling
│   │   ├── colorize.ts                 # Color management
│   │   ├── wrapAnsi.ts                 # ANSI code wrapping
│   │   ├── Ansi.tsx                    # ANSI component
│   │   ├── components/                 # Terminal components
│   │   │   ├── Box                     # Layout box
│   │   │   ├── Text                    # Text renderer
│   │   │   ├── Button                  # Button
│   │   │   ├── Link                    # Hyperlink
│   │   │   ├── Newline                 # Newline spacer
│   │   │   ├── ScrollBox               # Scrollable container
│   │   │   ├── AlternateScreen         # Fullscreen mode
│   │   │   └── RawAnsi                 # Raw ANSI output
│   │   ├── events/                     # Terminal event system
│   │   │   ├── dispatcher.ts           # Event dispatcher
│   │   │   ├── event.ts                # Base event
│   │   │   ├── keyboard-event.ts       # Keyboard events
│   │   │   ├── click-event.ts          # Mouse click events
│   │   │   └── terminal-event.ts       # Terminal events
│   │   ├── hooks/                      # Terminal hooks
│   │   │   ├── use-input.ts            # Keyboard input
│   │   │   ├── use-animation-frame.ts  # Animation frame
│   │   │   ├── use-terminal-focus.ts   # Focus tracking
│   │   │   └── use-selection.ts        # Text selection
│   │   ├── layout/                     # Yoga layout engine
│   │   │   ├── engine.ts               # Layout computation
│   │   │   ├── yoga.ts                 # Yoga bindings
│   │   │   └── geometry.ts             # Geometry utilities
│   │   └── termio/                     # Terminal I/O parsing
│   │       ├── parser.ts               # Input parser
│   │       ├── ansi.ts                 # ANSI sequences
│   │       ├── csi.ts                  # CSI sequences
│   │       └── sgr.ts                  # SGR color codes
│   │
│   ├── keybindings/                    # Keyboard shortcut system
│   │   ├── KeybindingContext.tsx        # Keybinding context
│   │   ├── defaultBindings.ts          # Default key mappings
│   │   ├── loadUserBindings.ts         # Load user overrides
│   │   ├── parser.ts                   # Keybinding syntax parser
│   │   ├── resolver.ts                 # Chord/sequence resolver
│   │   ├── schema.ts                   # Keybinding schema
│   │   ├── useKeybinding.ts            # Keybinding hook
│   │   └── validate.ts                 # Binding validation
│   │
│   ├── memdir/                         # Memory and CLAUDE.md system
│   │   ├── memdir.ts                   # Memory directory handler
│   │   ├── findRelevantMemories.ts     # Semantic memory search
│   │   ├── memoryScan.ts               # Memory file scanner
│   │   ├── memoryTypes.ts              # Memory type definitions
│   │   ├── teamMemPaths.ts             # Team memory paths
│   │   └── teamMemPrompts.ts           # Team memory prompts
│   │
│   ├── migrations/                     # Data/config migrations
│   │   ├── migrateAutoUpdatesToSettings.ts
│   │   ├── migrateOpusToOpus1m.ts
│   │   ├── migrateSonnet45ToSonnet46.ts
│   │   └── resetAutoModeOptInForDefaultOffer.ts
│   │
│   ├── moreright/
│   │   └── useMoreRight.tsx            # Right-side panel hook
│   │
│   ├── native-ts/                      # Native module bindings
│   │   ├── color-diff/index.ts         # Color diff (native)
│   │   ├── file-index/index.ts         # File system index (native)
│   │   └── yoga-layout/
│   │       ├── index.ts                # Yoga layout binding
│   │       └── enums.ts                # Yoga enums
│   │
│   ├── outputStyles/
│   │   └── loadOutputStylesDir.ts      # Load output style plugins
│   │
│   ├── plugins/                        # Plugin system
│   │   ├── builtinPlugins.ts           # Built-in plugin registry
│   │   └── bundled/index.ts            # Bundled plugin entry
│   │
│   ├── query/                          # LLM query configuration
│   │   ├── config.ts                   # Query config
│   │   ├── deps.ts                     # Query dependencies
│   │   ├── stopHooks.ts                # Stop sequence hooks
│   │   └── tokenBudget.ts              # Token budget management
│   │
│   ├── remote/                         # Remote agent execution
│   │   ├── RemoteSessionManager.ts     # Remote session lifecycle
│   │   ├── SessionsWebSocket.ts        # WebSocket session channel
│   │   ├── remotePermissionBridge.ts   # Remote permission handling
│   │   └── sdkMessageAdapter.ts        # SDK message adaptation
│   │
│   ├── schemas/
│   │   └── hooks.ts                    # Hook event schemas (Zod)
│   │
│   ├── screens/                        # Full-screen terminal views
│   │   ├── REPL.tsx                    # Main REPL screen
│   │   ├── Doctor.tsx                  # Diagnostics screen
│   │   └── ResumeConversation.tsx      # Session resume screen
│   │
│   ├── server/                         # Local server for IDE connections
│   │   ├── createDirectConnectSession.ts # Create IDE session
│   │   ├── directConnectManager.ts     # Manage IDE connections
│   │   └── types.ts                    # Server types
│   │
│   ├── services/                       # Core business logic
│   │   ├── awaySummary.ts              # Away/idle session summary
│   │   ├── claudeAiLimits.ts           # API limit tracking
│   │   ├── diagnosticTracking.ts       # Diagnostic events
│   │   ├── internalLogging.ts          # Internal log service
│   │   ├── AgentSummary/
│   │   │   └── agentSummary.ts         # Summarize agent sessions
│   │   ├── MagicDocs/
│   │   │   └── magicDocs.ts            # Auto-generate documentation
│   │   ├── PromptSuggestion/
│   │   │   ├── promptSuggestion.ts     # Suggest next prompts
│   │   │   └── speculation.ts          # Speculative suggestions
│   │   ├── SessionMemory/
│   │   │   ├── sessionMemory.ts        # Session memory management
│   │   │   ├── sessionMemoryUtils.ts   # Memory utilities
│   │   │   └── prompts.ts              # Memory prompts
│   │   ├── analytics/
│   │   │   ├── datadog.ts              # Datadog integration
│   │   │   ├── firstPartyEventLogger.ts# Event logging
│   │   │   ├── growthbook.ts           # Feature flags (GrowthBook)
│   │   │   ├── sink.ts                 # Analytics event sink
│   │   │   └── metadata.ts             # Analytics metadata
│   │   ├── api/
│   │   │   ├── claude.ts               # Claude API client
│   │   │   ├── bootstrap.ts            # API bootstrap
│   │   │   ├── filesApi.ts             # Files API
│   │   │   ├── adminRequests.ts        # Admin API
│   │   │   └── errorUtils.ts           # Error handling
│   │   ├── autoDream/
│   │   │   ├── autoDream.ts            # Auto-consolidate sessions
│   │   │   └── consolidationPrompt.ts  # Consolidation prompts
│   │   ├── compact/
│   │   │   ├── compact.ts              # Session compaction
│   │   │   ├── autoCompact.ts          # Automatic compaction
│   │   │   └── apiMicrocompact.ts      # Lightweight compaction
│   │   ├── extractMemories/
│   │   │   └── extractMemories.ts      # Extract key memories
│   │   ├── lsp/
│   │   │   ├── LSPClient.ts            # LSP client
│   │   │   ├── LSPServerManager.ts     # Manage LSP servers
│   │   │   └── LSPDiagnosticRegistry.ts# Diagnostics registry
│   │   ├── mcp/
│   │   │   ├── MCPConnectionManager.tsx# MCP connections
│   │   │   ├── client.ts               # MCP client
│   │   │   ├── config.ts               # MCP config
│   │   │   ├── auth.ts                 # MCP authentication
│   │   │   └── claudeai.ts             # Claude AI MCP server
│   │   ├── oauth/
│   │   │   ├── auth-code-listener.ts   # OAuth callback listener
│   │   │   ├── client.ts               # OAuth client
│   │   │   └── getOauthProfile.ts      # Fetch user profile
│   │   ├── plugins/
│   │   │   ├── PluginInstallationManager.ts # Install plugins
│   │   │   └── pluginOperations.ts     # Plugin CRUD
│   │   ├── policyLimits/
│   │   │   └── index.ts                # Enterprise policy limits
│   │   ├── remoteManagedSettings/
│   │   │   ├── index.ts                # Remote settings sync
│   │   │   └── securityCheck.tsx       # MDM security validation
│   │   ├── settingsSync/
│   │   │   └── index.ts                # Settings sync service
│   │   ├── teamMemorySync/
│   │   │   ├── index.ts                # Team memory sync
│   │   │   ├── teamMemSecretGuard.ts   # Scan for secrets in memory
│   │   │   └── watcher.ts              # Watch memory changes
│   │   ├── tips/
│   │   │   └── tipScheduler.ts         # Schedule UX tips/hints
│   │   ├── toolUseSummary/
│   │   │   └── toolUseSummaryGenerator.ts # Tool usage summaries
│   │   └── tools/
│   │       ├── StreamingToolExecutor.ts# Streaming tool execution
│   │       ├── toolExecution.ts        # Tool execution logic
│   │       └── toolOrchestration.ts    # Multi-tool orchestration
│   │
│   ├── skills/                         # Skills system
│   │   ├── bundledSkills.ts            # Built-in skill registry
│   │   ├── loadSkillsDir.ts            # Load from directory
│   │   ├── mcpSkillBuilders.ts         # Build skills from MCP
│   │   └── bundled/                    # Built-in skills
│   │       ├── batch.ts                # Batch operations
│   │       ├── claudeApi.ts            # Claude API skill
│   │       ├── debug.ts                # Debug skill
│   │       ├── keybindings.ts          # Keybinding config skill
│   │       ├── loop.ts                 # Loop execution
│   │       ├── remember.ts             # Memory management
│   │       ├── scheduleRemoteAgents.ts # Schedule remote agents
│   │       ├── simplify.ts             # Code simplification
│   │       ├── stuck.ts                # Unstick tool
│   │       ├── updateConfig.ts         # Update config skill
│   │       └── verify.ts               # Verification skill
│   │
│   ├── state/                          # Global app state
│   │   ├── AppState.tsx                # Main app state
│   │   ├── AppStateStore.ts            # State store
│   │   ├── onChangeAppState.ts         # State change handlers
│   │   ├── selectors.ts                # State selectors
│   │   └── store.ts                    # Store setup
│   │
│   ├── tasks/                          # Task execution framework
│   │   ├── types.ts                    # Task type definitions
│   │   ├── stopTask.ts                 # Stop task
│   │   ├── pillLabel.ts                # Task label display
│   │   ├── LocalMainSessionTask.ts     # Main session task
│   │   ├── DreamTask/                  # Consolidation tasks
│   │   ├── LocalShellTask/             # Shell task execution
│   │   ├── LocalAgentTask/             # Local agent tasks
│   │   ├── RemoteAgentTask/            # Remote agent tasks
│   │   └── InProcessTeammateTask/      # In-process teammate tasks
│   │
│   ├── tools/                          # All tools Claude can use
│   │   ├── AgentTool/                  # Spawn sub-agents
│   │   │   └── built-in/               # Built-in agent types
│   │   ├── AskUserQuestionTool/        # Ask user for input
│   │   ├── BashTool/                   # Execute bash commands
│   │   │   ├── bashCommandHelpers.ts   # Command helpers
│   │   │   ├── bashSecurity.ts         # 500+ line AST security validator
│   │   │   └── sedEditParser.ts        # sed command parser
│   │   ├── BriefTool/                  # Summarize/brief content
│   │   ├── ConfigTool/                 # Read/write settings
│   │   ├── EnterPlanModeTool/          # Enter planning mode
│   │   ├── EnterWorktreeTool/          # Create git worktree
│   │   ├── ExitPlanModeTool/           # Exit planning mode
│   │   ├── ExitWorktreeTool/           # Exit git worktree
│   │   ├── FileEditTool/               # Edit files (diff-based)
│   │   ├── FileReadTool/               # Read file contents
│   │   ├── FileWriteTool/              # Write/create files
│   │   ├── GlobTool/                   # File pattern matching
│   │   ├── GrepTool/                   # Regex content search
│   │   ├── LSPTool/                    # Language Server Protocol
│   │   ├── ListMcpResourcesTool/       # List MCP resources
│   │   ├── MCPTool/                    # Call MCP server tools
│   │   ├── McpAuthTool/                # MCP authentication
│   │   ├── NotebookEditTool/           # Edit Jupyter notebooks
│   │   ├── PowerShellTool/             # PowerShell execution (Windows)
│   │   ├── REPLTool/                   # Python/Node interactive REPL
│   │   ├── ReadMcpResourceTool/        # Read MCP resources
│   │   ├── RemoteTriggerTool/          # Trigger remote agents
│   │   ├── ScheduleCronTool/           # Schedule cron jobs
│   │   ├── SendMessageTool/            # Send messages to agents
│   │   ├── SkillTool/                  # Execute skills
│   │   ├── SleepTool/                  # Pause execution
│   │   ├── SyntheticOutputTool/        # Synthetic output generation
│   │   ├── TaskCreateTool/             # Create background tasks
│   │   ├── TaskGetTool/                # Get task status/details
│   │   ├── TaskListTool/               # List all tasks
│   │   ├── TaskOutputTool/             # Get task output
│   │   ├── TaskStopTool/               # Stop a running task
│   │   ├── TaskUpdateTool/             # Update task state
│   │   ├── TeamCreateTool/             # Create agent teams
│   │   ├── TeamDeleteTool/             # Delete agent teams
│   │   ├── TodoWriteTool/              # Write todo lists
│   │   ├── ToolSearchTool/             # Search available tools
│   │   ├── WebFetchTool/               # HTTP fetch / web browse
│   │   ├── WebSearchTool/              # Web search
│   │   ├── shared/                     # Shared tool utilities
│   │   └── testing/                    # Tool test utilities
│   │
│   ├── types/                          # TypeScript type definitions
│   │   ├── command.ts                  # Command types
│   │   ├── hooks.ts                    # Hook event types
│   │   ├── ids.ts                      # ID types
│   │   ├── logs.ts                     # Log types
│   │   ├── permissions.ts              # Permission types
│   │   ├── plugin.ts                   # Plugin types
│   │   ├── textInputTypes.ts           # Text input types
│   │   └── generated/
│   │       ├── events_mono/            # Generated event types
│   │       └── google/                 # Google API types
│   │
│   ├── upstreamproxy/                  # Proxy support
│   │   ├── upstreamproxy.ts            # Proxy logic (HTTPS/SOCKS)
│   │   └── relay.ts                    # Relay connections
│   │
│   ├── utils/                          # 100+ utility files
│   │   ├── Shell.ts                    # Shell abstraction
│   │   ├── ShellCommand.ts             # Shell command builder
│   │   ├── QueryGuard.ts               # Query guard
│   │   ├── CircularBuffer.ts           # Circular buffer data structure
│   │   ├── api.ts                      # API utilities
│   │   ├── auth.ts                     # Auth utilities
│   │   ├── aws.ts                      # AWS utilities
│   │   ├── browser.ts                  # Browser detection
│   │   ├── bufferedWriter.ts           # Buffered file writer
│   │   ├── caCerts.ts                  # CA certificate loading
│   │   ├── cachePaths.ts               # Cache path management
│   │   ├── claudemd.ts                 # CLAUDE.md utilities
│   │   ├── cleanup.ts                  # Cleanup on exit
│   │   ├── tokenBudget.ts              # Token budget tracking
│   │   ├── uuid.ts                     # UUID generation
│   │   ├── yaml.ts                     # YAML parsing
│   │   ├── xml.ts                      # XML utilities
│   │   ├── user.ts                     # User info
│   │   ├── worktree.ts                 # Git worktree utilities
│   │   ├── background/                 # Background task utils
│   │   │   └── remote/                 # Remote background tasks
│   │   ├── bash/                       # Bash parsing
│   │   │   ├── bashParser.ts           # Bash AST parser
│   │   │   ├── ast.ts                  # AST definitions
│   │   │   ├── shellCompletion.ts      # Shell tab completion
│   │   │   └── specs/                  # Command specs
│   │   ├── claudeInChrome/             # Chrome extension bridge
│   │   │   ├── chromeNativeHost.ts     # Native messaging host
│   │   │   ├── mcpServer.ts            # MCP server for Chrome
│   │   │   └── setup.ts                # Chrome setup
│   │   ├── computerUse/                # Screen automation
│   │   │   ├── executor.ts             # Action executor
│   │   │   ├── hostAdapter.ts          # Host adapter
│   │   │   ├── swiftLoader.ts          # Swift native loader
│   │   │   └── gates.ts                # Feature gates
│   │   ├── deepLink/                   # Deep link handling
│   │   │   ├── parseDeepLink.ts        # Parse claude:// links
│   │   │   ├── registerProtocol.ts     # Register URL protocol
│   │   │   └── terminalLauncher.ts     # Launch from terminal
│   │   ├── dxt/                        # DXT file utilities
│   │   ├── filePersistence/
│   │   │   └── outputsScanner.ts       # Scan persisted outputs
│   │   ├── git/
│   │   │   ├── gitFilesystem.ts        # Git filesystem ops
│   │   │   └── gitignore.ts            # .gitignore parsing
│   │   ├── github/                     # GitHub API utilities
│   │   ├── hooks/                      # Hook execution system
│   │   │   ├── AsyncHookRegistry.ts    # Async hook registry
│   │   │   ├── sessionHooks.ts         # Session lifecycle hooks
│   │   │   └── hookHelpers.ts          # Hook utilities
│   │   ├── mcp/                        # MCP utilities
│   │   ├── memory/                     # Memory utilities
│   │   ├── messages/                   # Message serialization
│   │   ├── model/
│   │   │   ├── bedrock.ts              # AWS Bedrock config
│   │   │   ├── modelCapabilities.ts    # Model feature matrix
│   │   │   ├── modelStrings.ts         # Model ID strings
│   │   │   └── aliases.ts              # Model aliases
│   │   ├── nativeInstaller/            # Native module installer
│   │   ├── permissions/
│   │   │   ├── PermissionMode.ts       # Permission modes
│   │   │   ├── bashClassifier.ts       # Bash command classifier
│   │   │   └── filesystem.ts           # Filesystem permissions
│   │   ├── plugins/
│   │   │   ├── installedPluginsManager.ts # Manage installed plugins
│   │   │   └── dependencyResolver.ts   # Plugin dependency resolver
│   │   ├── powershell/
│   │   │   ├── parser.ts               # PowerShell parser
│   │   │   └── dangerousCmdlets.ts     # Dangerous cmdlet list
│   │   ├── processUserInput/
│   │   │   ├── processBashCommand.tsx  # Process bash input
│   │   │   └── processSlashCommand.tsx # Process slash commands
│   │   ├── sandbox/                    # Sandbox utilities
│   │   ├── secureStorage/
│   │   │   ├── macOsKeychainStorage.ts # macOS keychain
│   │   │   └── plainTextStorage.ts     # Fallback plain storage
│   │   ├── settings/
│   │   │   ├── settingsCache.ts        # Settings cache
│   │   │   ├── validateEditTool.ts     # Validate edit tool settings
│   │   │   └── mdm/                    # MDM/enterprise settings
│   │   ├── shell/
│   │   │   ├── bashProvider.ts         # Bash shell provider
│   │   │   └── powershellProvider.ts   # PowerShell provider
│   │   ├── skills/                     # Skill utilities
│   │   ├── suggestions/
│   │   │   ├── shellHistoryCompletion.ts # Shell history autocomplete
│   │   │   └── slackChannelSuggestions.ts# Slack channel suggest
│   │   ├── swarm/                      # Swarm/multi-agent utilities
│   │   │   ├── spawnInProcess.ts       # In-process agent spawn
│   │   │   ├── leaderPermissionBridge.ts # Leader permission bridge
│   │   │   └── backends/               # Swarm backends
│   │   ├── task/
│   │   │   ├── TaskOutput.ts           # Task output handling
│   │   │   ├── diskOutput.ts           # Disk-based output
│   │   │   └── outputFormatting.ts     # Output formatting
│   │   ├── telemetry/
│   │   │   ├── betaSessionTracing.ts   # Beta tracing
│   │   │   └── pluginTelemetry.ts      # Plugin analytics
│   │   ├── teleport/
│   │   │   ├── api.ts                  # Teleport API
│   │   │   ├── environmentSelection.ts # Environment picker
│   │   │   └── gitBundle.ts            # Git bundle utilities
│   │   ├── todo/                       # Todo utilities
│   │   └── ultraplan/                  # Ultra plan mode
│   │
│   ├── vim/                            # Vim mode
│   │   ├── motions.ts                  # Vim motion commands (hjkl, w, b, etc.)
│   │   ├── operators.ts                # Vim operators (d, c, y, etc.)
│   │   ├── textObjects.ts              # Vim text objects (iw, a", etc.)
│   │   ├── transitions.ts              # Mode transitions (normal/insert/visual)
│   │   └── types.ts                    # Vim mode types
│   │
│   └── voice/
│       └── voiceModeEnabled.ts         # Voice mode flag and utilities
│
├── package.json                        # Dependencies and scripts
├── tsconfig.json                       # TypeScript configuration
└── .gitignore
```

---

## License

This is proprietary Anthropic source code. Not licensed for redistribution or commercial use.
