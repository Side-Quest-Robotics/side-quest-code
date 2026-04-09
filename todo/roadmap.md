# Side Quest IDE — Full Roadmap Checklist

> Robotics-first agentic coding IDE built on Code-OSS v1.116.0 (MIT License)
> Repository: github.com/Side-Quest-Robotics/side-quest-code

---

## Phase 0: Foundation (COMPLETED)

- [x] **Fork Code-OSS repository**
  - Cloned microsoft/vscode v1.116.0 (commit baa9c85c) via shallow clone
  - Removed .git history, initialized fresh repo
  - Confirmed MIT license in LICENSE.txt
  - Pushed to github.com/Side-Quest-Robotics/side-quest-code

- [x] **Set up local development environment**
  - Installed Node.js v22.22.1 via nvm (required by .nvmrc)
  - Ran `npm install` to install all dependencies
  - Verified `./scripts/code.sh` launches the editor successfully

---

## Phase 1A: Product Rebranding (IN PROGRESS)

- [x] **Rebrand product.json**
  - File: `/product.json`
  - Change `nameShort` from "Code - OSS" to "Side Quest"
  - Change `nameLong` from "Code - OSS" to "Side Quest IDE"
  - Change `applicationName` from "code-oss" to "side-quest"
  - Change `dataFolderName` from ".vscode-oss" to ".side-quest"
  - Change `win32MutexName` from "vscodeoss" to "sidequest"
  - Change `serverApplicationName` from "code-server-oss" to "side-quest-server"
  - Change `serverDataFolderName` from ".vscode-server-oss" to ".side-quest-server"
  - Change `tunnelApplicationName` from "code-tunnel-oss" to "side-quest-tunnel"
  - Change `win32DirName` from "Microsoft Code OSS" to "Side Quest IDE"
  - Change `win32NameVersion` from "Microsoft Code OSS" to "Side Quest IDE"
  - Change `win32RegValueName` from "CodeOSS" to "SideQuestIDE"
  - Change `win32AppUserModelId` from "Microsoft.CodeOSS" to "SideQuest.IDE"
  - Change `win32ShellNameShort` from "C&ode - OSS" to "S&ide Quest"
  - Change `win32TunnelServiceMutex` from "vscodeoss-tunnelservice" to "sidequest-tunnelservice"
  - Change `win32TunnelMutex` from "vscodeoss-tunnel" to "sidequest-tunnel"
  - Change `darwinBundleIdentifier` from "com.visualstudio.code.oss" to "com.sidequest.ide"
  - Change `linuxIconName` from "code-oss" to "side-quest"
  - Change `urlProtocol` from "code-oss" to "side-quest"
  - Change `reportIssueUrl` to "https://github.com/Side-Quest-Robotics/side-quest-code/issues/new"
  - Remove `defaultChatAgent` block (GitHub Copilot config — lines 86-150)
  - Remove `trustedExtensionAuthAccess` block (Copilot-specific)
  - Remove `builtInExtensionsEnabledWithAutoUpdates` block (Copilot-specific)

- [x] **Rename package.json**
  - File: `/package.json`
  - Change `"name": "code-oss-dev"` to `"name": "side-quest-ide"`

- [ ] **Replace application icons** (TODO — needs design work)
  - Replace `resources/darwin/code.icns` with Side Quest icon (macOS app icon, .icns format, 16x16 through 1024x1024)
  - Replace `resources/win32/code.ico` with Side Quest icon (Windows app icon, .ico format, multi-resolution)
  - Replace `resources/linux/code.png` with Side Quest icon (Linux app icon, .png, 512x512)
  - Replace `resources/server/favicon.ico` with Side Quest favicon
  - NOTE: Currently still using Code-OSS icons as placeholders; need Side Quest branding design

- [x] **Disable Microsoft telemetry**
  - Add `"enableTelemetry": false` to `product.json` — this is the master kill switch
  - The OSS build doesn't have `aiConfig.ariaKey` so telemetry isn't actually sent, but this makes intent explicit
  - Telemetry code lives in `src/vs/platform/telemetry/common/` — the `supportsTelemetry()` function in `telemetryUtils.ts` checks `productService.enableTelemetry`
  - Telemetry endpoints (mobile.events.data.microsoft.com) in `src/vs/platform/telemetry/common/1dsAppender.ts` won't fire without ariaKey

- [x] **Build and verify rebranding**
  - Run `npm run compile` — must succeed with no errors
  - Run `./scripts/code.sh` — title bar must say "Side Quest IDE"
  - Check About dialog shows "Side Quest IDE"
  - Check data folder created at `~/.side-quest/` (not `~/.vscode-oss/`)
  - Confirm no telemetry endpoints called

---

## Phase 1B: Robotics Language Support

- [ ] **Create `extensions/robotics-languages/` built-in extension**
  - Follow the pattern of `extensions/yaml/package.json` for structure
  - Create `package.json` with:
    - `contributes.languages` registering: `.urdf`, `.sdf`, `.xacro`, `.world`, `.launch`, `.launch.xml`, `.launch.py`, `.msg`, `.srv`, `.action`
    - `contributes.grammars` with TextMate grammar files — URDF/SDF/xacro are XML-based so inherit from `text.xml` scope with robotics-specific keyword highlighting (robot, link, joint, sensor, plugin, gazebo, transmission, material, geometry, collision, visual, inertial)
    - `contributes.configurationDefaults` for sensible defaults (2-space indent for URDF/SDF, XML auto-closing tags)
  - Create `language-configuration.json` for each language (bracket matching, auto-closing pairs, comment toggling, folding rules)
  - Create TextMate grammar `.tmLanguage.json` files:
    - `urdf.tmLanguage.json` — extends XML with URDF element highlighting
    - `sdf.tmLanguage.json` — extends XML with SDF/SDFormat element highlighting
    - `ros-msg.tmLanguage.json` — ROS message definition syntax (types, field names, constants, comments)
    - `ros-launch-xml.tmLanguage.json` — ROS launch XML with element highlighting (node, include, param, arg, group, remap)
  - Add snippets for common patterns (e.g., `urdf-link`, `urdf-joint`, `sdf-model`, `ros-node`, `launch-node`)

---

## Phase 1C: Robotics File Icons

- [ ] **Add robotics file icons to Seti icon theme**
  - File: `extensions/theme-seti/icons/vs-seti-icon-theme.json`
  - Add SVG icons for robotics file types (or map to existing similar icons as placeholders):
    - `.urdf` → robot/mechanical icon
    - `.sdf` → simulation icon
    - `.bag` / `.mcap` → data/recording icon
    - `.msg` / `.srv` / `.action` → message/interface icon
    - `.launch` / `.launch.xml` / `.launch.py` → rocket/launch icon
    - `.xacro` → macro/template icon
    - `.world` → globe/world icon
    - `package.xml` → ROS package icon
  - Add `fileExtensions` mappings in the icon theme JSON
  - Create or source SVG icon files and place in `extensions/theme-seti/icons/`

---

## Phase 1D: ROS/Colcon Terminal Completions

- [ ] **Create `extensions/robotics-terminal-suggest/` built-in extension**
  - Follow the pattern of `extensions/terminal-suggest/` (see its `package.json` and `src/` directory)
  - Create `package.json` with:
    - `enabledApiProposals: ["terminalCompletionProvider"]`
    - `contributes.terminal.completionProviders` registration
    - `activationEvents: ["onTerminalShellIntegration:*"]`
  - Implement completion providers for:
    - `ros2` — subcommands (topic, node, service, action, param, launch, bag, run, doctor, interface)
    - `ros2 topic` — list, echo, info, pub, hz, bw, delay, find, type
    - `ros2 node` — list, info
    - `ros2 launch` — package name completions (from `~/*_ws/install/` or `ROS_PACKAGE_PATH`)
    - `colcon` — build, test, list, info, graph, metadata
    - `colcon build` — `--packages-select`, `--packages-up-to`, `--cmake-args`, `--event-handlers`
    - `rosdep` — install, check, keys, resolve, update
    - `ament` — subcommands
    - `catkin` — build, make, config, profile, list (for ROS1 users)
  - Static completions first (hardcoded command trees), dynamic completions later (querying `ros2` CLI output)

---

## Phase 1E: Default Configuration for Robotics

- [ ] **Set robotics-friendly defaults**
  - In `extensions/robotics-languages/package.json` `contributes.configurationDefaults`:
    - Exclude `build/`, `install/`, `log/` from file watcher (colcon workspace dirs)
    - Set `files.exclude` defaults for `build/`, `install/`, `log/` directories
    - Set `search.exclude` for `build/`, `install/`, `log/`
  - Add a "ROS2 Terminal" terminal profile that auto-sources workspace `setup.bash`:
    - Detect `install/setup.bash` in workspace root
    - Create terminal profile contribution in `contributes.terminal.profiles`

---

## Phase 2A: ROS Explorer Sidebar

- [ ] **Create `extensions/robotics-ros-explorer/` built-in extension**
  - Register a new Activity Bar view container with robotics icon via `contributes.viewsContainers.activitybar`
  - Register tree views: "Nodes", "Topics", "Services", "Actions", "Parameters" via `contributes.views`
  - Implement `TreeDataProvider` for each view:
    - **Nodes view**: Calls `ros2 node list`, parses output, shows node names as tree items. Expanding a node shows its publishers, subscribers, services, and parameters (via `ros2 node info <name>`)
    - **Topics view**: Calls `ros2 topic list -t`, shows topic name + message type. Expanding shows publishers/subscribers. Right-click → "Echo Topic" opens terminal with `ros2 topic echo`
    - **Services view**: Calls `ros2 service list -t`, shows service name + type
    - **Actions view**: Calls `ros2 action list -t`, shows action name + type
    - **Parameters view**: Calls `ros2 param list`, grouped by node. Shows current values via `ros2 param get`
  - Auto-refresh on configurable timer (default 2 seconds) or manual refresh button
  - Show "ROS2 not detected" welcome view when `ros2` CLI is not found in PATH
  - Follow `extensions/git/` pattern for tree views + external CLI integration

---

## Phase 2B: Real-Time Sensor Dashboard

- [ ] **Create `extensions/robotics-viz/` built-in extension with webview panels**
  - Register a `WebviewViewProvider` for a bottom panel via `contributes.views` with `type: "webview"`
  - Webview HTML loads uPlot (lightweight, ~35KB, built for real-time streaming) or Chart.js for sensor data
  - Extension backend:
    - Subscribes to selected ROS topics via `ros2 topic echo --once` or via rclnodejs (direct DDS library for Node.js)
    - Serializes messages and sends to webview via `postMessage()`
    - Handles backpressure (drop frames if webview can't keep up)
  - Support common message types with automatic visualization:
    - `sensor_msgs/Image` → image display (decode from raw/compressed)
    - `sensor_msgs/LaserScan` → polar plot or top-down 2D scan view
    - `geometry_msgs/Twist` → linear/angular velocity gauges
    - `nav_msgs/Odometry` → XY position plot with heading arrow
    - `std_msgs/Float64` / `Int32` / `Bool` → time series line chart
    - `sensor_msgs/Imu` → orientation visualization (roll/pitch/yaw)
    - `sensor_msgs/JointState` → joint angle bar chart
  - User workflow: right-click topic in ROS Explorer (2A) → "Visualize Topic" → opens/adds to viz panel
  - Support multiple topic streams simultaneously in a grid layout

---

## Phase 2C: URDF/SDF 3D Preview

- [ ] **Create `extensions/robotics-model-preview/` built-in extension with custom editor**
  - Register `CustomEditorProvider` for `.urdf` and `.sdf` files via `contributes.customEditors`
  - Webview uses Three.js with `urdf-loader` library (https://github.com/gkjohnson/urdf-loaders) to render robot models
  - Split view: 3D robot visualization on one side, text editor on the other
  - 3D viewer features:
    - Orbit controls (rotate, zoom, pan)
    - Joint sliders to move robot joints interactively
    - Toggle wireframe / solid / collision geometry views
    - Show link names and joint axes as overlays
    - Grid floor and axis indicator
  - Live reload: as user edits URDF/SDF XML in the text editor, 3D preview updates in real-time (debounced on text change events)
  - Handle xacro files by running `xacro` preprocessor before rendering (call `ros2 run xacro xacro <file>` and render the output)
  - Error handling: show parsing errors inline in the 3D view (e.g., "Invalid joint type at line 42")

---

## Phase 2D: Environment Bootstrapping Commands

- [ ] **Add workspace setup commands to ROS Explorer extension**
  - Register commands via `contributes.commands`:
    - "Side Quest: Create ROS2 Workspace" — creates `src/`, runs `colcon build` to generate `build/`, `install/`, `log/`
    - "Side Quest: Source ROS Environment" — runs `source /opt/ros/<distro>/setup.bash` in all open terminals
    - "Side Quest: Install Dependencies (rosdep)" — runs `rosdep install --from-paths src --ignore-src -y`
    - "Side Quest: Build Workspace" — runs `colcon build` with user-configurable args
    - "Side Quest: Clean Build" — removes `build/`, `install/`, `log/` and rebuilds
  - Register task definitions via `contributes.taskDefinitions`:
    - `colcon` task type (like how `extensions/gulp/` defines the `gulp` task type)
    - `ros2-launch` task type for launch files
  - Terminal profile: "ROS2 Terminal" that auto-sources the workspace setup.bash on creation
  - Auto-detect ROS distro from environment or `/opt/ros/` directory listing

---

## Phase 3A: ROS2 Debug Adapter

- [ ] **Create `extensions/robotics-debug/` built-in extension implementing Debug Adapter Protocol**
  - Register debugger via `contributes.debuggers` with `type: "ros2"`
  - Launch configuration schema supporting:
    - `launch_file` — path to ROS launch file
    - `package` — ROS package name
    - `executable` — node executable name
    - `nodes` — array of node configs for multi-node debugging
    - `args` — command line arguments
    - `env` — environment variables
  - Debug adapter wraps:
    - `debugpy` for Python ROS nodes (most common)
    - `gdb` via `cppdbg` for C++ ROS nodes
    - Manages multiple child debug sessions simultaneously
  - Use VS Code's compound launch configurations for multi-node debugging
  - Integration with ROS Explorer: right-click node → "Attach Debugger"
  - Show ROS-specific variables in Debug sidebar: node parameters, topic subscriptions, timer callbacks
  - Launch file parsing: auto-generate launch configs from `.launch.py` / `.launch.xml` files
  - Pattern: follow `src/vs/workbench/contrib/debug/` for DAP implementation

---

## Phase 3B: Serial/Hardware Monitor

- [ ] **Create `extensions/robotics-hardware/` built-in extension**
  - Use `serialport` npm package for serial port communication (Node.js native module, works in Electron)
  - Webview panel for serial monitor UI:
    - Port selector dropdown (auto-detect connected devices)
    - Baud rate selector (common rates: 9600, 115200, etc.)
    - Send/receive text area with timestamps
    - Hex view toggle
    - Auto-scroll with pause button
    - Line ending selector (CR, LF, CRLF)
    - DTR/RTS toggle for bootloader mode
  - Tree view of connected serial devices with auto-detection (USB vendor/product IDs for common boards: Arduino, ESP32, STM32, Teensy)
  - Commands:
    - "Side Quest: Open Serial Monitor" — opens serial panel
    - "Side Quest: Upload Firmware" — integrates with Arduino CLI or PlatformIO CLI for firmware flashing
    - "Side Quest: Reset Board" — toggles DTR for board reset
  - Status bar item showing connected port and baud rate

---

## Phase 3C: Launch File IntelliSense

- [ ] **Add Language Server Protocol support to `extensions/robotics-languages/`**
  - Create `server/` subdirectory with LSP implementation (follow `extensions/html-language-features/server/` pattern)
  - For `.launch.xml` files:
    - Go-to-definition: click on package name → jump to package.xml; click on included launch file → open it
    - Hover info: show parameter descriptions, node documentation
    - Validation: check that referenced packages exist in workspace, node executables exist, parameter types match
    - Completions: package names (from `ros2 pkg list`), node names, parameter names, remap targets
    - Diagnostics: red squiggles for missing packages, unknown nodes, type mismatches
  - For `.launch.py` files:
    - Static analysis of `LaunchDescription`, `Node()`, `IncludeLaunchDescription()` calls
    - Completions for `package` and `executable` arguments
    - Go-to-definition for included launch files
  - For `.msg` / `.srv` / `.action` files:
    - Validation of field types (must be valid ROS primitive or imported message type)
    - Go-to-definition for imported message types
    - Hover shows type documentation

---

## Phase 3D: Rosbag Viewer

- [ ] **Create `extensions/robotics-bag-viewer/` built-in extension with custom editor**
  - Register custom editor for `.bag` (ROS1 format) and `.mcap` (ROS2 format) files
  - Use `@mcap/core` JavaScript library for parsing .mcap files
  - Use a ROS1 bag parser (or convert to mcap first) for .bag files
  - Webview renders:
    - Topic list with message counts and frequency
    - Timeline scrubber with play/pause/speed controls
    - Topic data visualization (reuse viz components from Phase 2B)
    - Message inspector: click any message to see full JSON/structured view
    - Time range selection for filtering
  - Export capabilities: export selected topics to CSV, extract images to files
  - Integration with viz panel (2B): "Play bag into viz dashboard"

---

## Phase 4A: Robotics AI Context Provider

- [ ] **Create AI context integration for robotics workspace awareness**
  - Register chat context provider via `chatContextProvider` proposed API (see `src/vscode-dts/vscode.proposed.chatContextProvider.d.ts`)
  - Auto-inject into AI conversations:
    - Current ROS graph state (active nodes, topics, services) from ROS Explorer (2A)
    - Robot model info from URDF files in workspace (parsed joint/link structure)
    - Installed ROS packages and their versions
    - Workspace package structure (from `package.xml` files)
    - Hardware state (connected serial devices) from hardware monitor (3B)
    - Current sensor readings summary (from viz panel, 2B)
  - If proposed API is insufficient, modify `src/vs/workbench/contrib/chat/common/attachments/chatVariables.ts` to register a built-in robotics variable resolver
  - Key files: `src/vs/workbench/contrib/chat/browser/chat.contribution.ts` (chat entry point), `src/vs/workbench/api/common/extHostChatContext.ts` (extension host API)

---

## Phase 4B: @robotics Chat Participant

- [ ] **Create AI-powered robotics code generation chat participant**
  - Register `@robotics` chat participant via `vscode.chat.createChatParticipant` extension API
  - System prompts with deep ROS2 API knowledge:
    - Node creation patterns (publishers, subscribers, services, actions, timers)
    - Message type definitions and common interfaces
    - Launch file patterns (both XML and Python)
    - Control theory patterns (PID controllers, state machines, behavior trees)
    - Motion planning patterns (MoveIt2 integration, trajectory planning)
    - Navigation patterns (Nav2 stack configuration)
  - Code generation capabilities:
    - "Generate a ROS2 node that subscribes to /cmd_vel and publishes to /odom"
    - "Create a URDF for a 6-DOF arm with revolute joints"
    - "Write a launch file that starts navigation2 with my custom parameters"
    - "Generate a PID controller for motor speed regulation"
    - "Create a behavior tree for pick-and-place task"
  - Uses workspace context from 4A to generate code that fits the existing project
  - Works with any AI backend (GitHub Copilot, Claude, local models via Ollama)

---

## Phase 4C: Simulation Integration Bridge

- [ ] **Create core workbench contribution for simulation management**
  - New contribution directory: `src/vs/workbench/contrib/simulation/`
  - Register in `src/vs/workbench/workbench.common.main.ts`
  - Features:
    - Launch Gazebo / Isaac Sim / Webots from IDE command
    - Stream simulation viewport into a webview panel (via gzweb for Gazebo, or VNC/WebRTC bridge)
    - Simulation controls: play, pause, reset, step, speed adjustment
    - Status bar indicator showing simulation state (running/paused/stopped/not connected)
    - World file editor integration: edit .world files and hot-reload in simulation
  - Enable agentic loop: code → simulate → analyze results → refine code → re-simulate
  - Spawn model commands: drag robot model from URDF preview into simulation
  - Sensor data bridge: route simulated sensor data through same viz panel as real data (2B)

---

## Phase 4D: Safety Validation Layer

- [ ] **Create safety analysis service for AI-generated robotics code**
  - New platform service: `src/vs/platform/roboticsSafety/` implementing `IRoboticsSafetyService`
  - Static analysis checks before hardware deployment:
    - Unbounded velocities (velocity commands without limits)
    - Missing emergency stop handlers
    - No timeout on blocking service calls
    - Infinite loops without sleep/rate control
    - Direct hardware access without safety wrappers
    - Missing collision checking before motion execution
    - Hardcoded IP addresses or ports
  - Integration with chat tool pipeline: intercept code-apply actions when target is a ROS node or hardware controller (`src/vs/workbench/contrib/chat/common/tools/`)
  - Diagnostics provider: flag safety issues with warning/error squiggles (like ESLint)
  - "Deploy to Robot" command with confirmation dialog:
    - Shows safety check results (pass/warn/fail)
    - Requires user acknowledgment before deployment
    - Option to run in simulation first
  - Severity levels: INFO (best practice), WARNING (potential issue), ERROR (must fix before deploy)

---

## Phase 5A: Remote Robot Connection

- [ ] **Extend VS Code Remote capabilities for robot-specific workflows**
  - Build on `src/vs/workbench/contrib/remote/` infrastructure
  - "Connect to Robot" command: SSH to robot's onboard computer (Jetson, Raspberry Pi, Intel NUC)
  - Robot-specific remote features:
    - Auto-detect ROS workspace on remote machine
    - Port forwarding for ROS topic visualization
    - Remote terminal with ROS environment pre-sourced
    - Deploy local workspace to remote robot via rsync/scp
    - Remote debugging (attach to running nodes on robot)
  - Robot connection profiles: save robot hostname, username, workspace path for quick reconnect
  - Status bar: show connected robot name and connection quality

---

## Phase 5B: Fleet Management Dashboard

- [ ] **Create multi-robot management webview**
  - Dashboard showing all connected robots in a fleet
  - Per-robot status: online/offline, CPU/memory usage, battery level, active nodes, errors
  - Per-robot logs: stream stdout/stderr from each robot
  - Fleet-wide operations:
    - Deploy code to all robots simultaneously
    - Restart specific nodes across fleet
    - Collect rosbag data from all robots
  - Map view: show robot positions on a floor plan or satellite map (if GPS/localization data available)
  - Alerting: notifications when a robot goes offline, errors spike, or battery is low

---

## Phase 5C: Project Template Gallery

- [ ] **Create "New Robot Project" wizard with templates**
  - Templates for common robot types:
    - "Navigation Robot" — Nav2 stack, AMCL, map server, costmap, controller
    - "Manipulation Arm" — MoveIt2, joint controllers, gripper driver, pick-and-place demo
    - "Drone/UAV" — MAVSDK, PX4 integration, mission planner
    - "Mobile Manipulator" — combined navigation + manipulation
    - "Swarm Robotics" — multi-robot communication, formation control
    - "Custom Robot" — blank ROS2 workspace with sensible defaults
  - Each template includes:
    - Pre-configured `package.xml`, `CMakeLists.txt`, `setup.py`
    - Example nodes with common patterns
    - Launch files for simulation and real hardware
    - URDF/SDF model (if applicable)
    - README with getting-started guide
    - Docker/devcontainer configuration
  - Command: "Side Quest: New Robot Project" → wizard UI with template selection, project name, ROS distro

---

## Phase 5D: Extension Marketplace

- [ ] **Set up Side Quest extension marketplace**
  - Configure product.json to point to Open VSX Registry (https://open-vsx.org/) instead of VS Code Marketplace
  - Alternatively, host a custom marketplace using Open VSX server
  - Curate "Robotics" category with recommended extensions
  - Partner with hardware vendors for official extensions:
    - NVIDIA (Isaac Sim, Jetson development)
    - Universal Robots (UR driver configuration)
    - Boston Dynamics (Spot SDK integration)
    - Intel (RealSense camera configuration)
    - Clearpath Robotics (platform drivers)
  - Extension publishing guide for robotics community
