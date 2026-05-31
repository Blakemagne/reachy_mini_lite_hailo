# Reachy Mini Upstream Briefing — Jan 2026 to Apr 2026

> 235 commits merged from `pollen-robotics/reachy_mini` into our fork on 2026-05-31.
> No custom commits on our side — clean fast-forward merge.

---

## Releases

The project went through **8 minor/patch releases** while we were away:

| Release | Key Theme |
|---------|-----------|
| v1.2.8–v1.2.13 | Bug fixes, WiFi improvements, dependency cleanup |
| **v1.3.0** | WebRTC data channels, HuggingFace auth, central signaling relay |
| **v1.4.0** | Cross-platform volume control, motor reflash improvements, REST API docs |
| v1.4.1–v1.7.3 | Tagged but commits not merged to develop yet (likely on `main`) |
| v1.8.0rc1 | Release candidate in progress |

Current develop version: **1.4.0.dev0**

---

## Major New Features

### 1. JavaScript Browser SDK

**`js/reachy-mini.js`**

A full browser SDK for controlling Reachy Mini remotely via WebRTC. Real-time head/antenna control, video streaming, bidirectional audio, and state sync (~500ms). Uses HuggingFace OAuth for auth. This opens up web-based remote control.

### 2. Central Signaling Relay

**`src/reachy_mini/media/central_signaling_relay.py`**

Bridges remote WebRTC signaling (HTTP/SSE) to local GStreamer WebRTC. Handles token refresh, reconnection, and connection health monitoring. This is the backbone of the wireless/remote architecture.

### 3. Bidirectional Audio over WebRTC

Audio streaming now works both directions — you can send audio *to* the robot, not just receive. GStreamer is now the **default media backend** (previously optional).

### 4. HuggingFace Integration

**`src/reachy_mini/apps/sources/hf_auth.py`** and **`src/reachy_mini/apps/sources/app_update_checker.py`**

- OAuth device-flow authentication for private HF Spaces
- Auto-detection of app updates from HuggingFace-hosted apps
- Token refresh and management for remote connections

### 5. Fork Conversation App

**`src/reachy_mini/apps/fork_conversation.py`**

Scaffolding system to clone and customize the Reachy Mini conversation app using Jinja2 templates. Generates unique Python packages with profiles, landing pages, and tools.

### 6. Cross-Platform Volume Control

Separate implementations for Linux (PipeWire/PulseAudio), macOS (CoreAudio), and Windows (pycaw). Sound card auto-detection for Reachy Mini audio and ReSpeaker devices.

- `src/reachy_mini/daemon/app/routers/volume_control.py`
- `src/reachy_mini/daemon/app/routers/volume_control_linux.py`
- `src/reachy_mini/daemon/app/routers/volume_control_macos.py`
- `src/reachy_mini/daemon/app/routers/volume_control_windows.py`

### 7. First Wake-Up Flow

**`src/reachy_mini/daemon/app/routers/first_wake_up.py`**

First-boot flagging so the UI wizard only runs once on initial setup.

---

## Architecture Changes

### Removed / Deprecated

- **`src/reachy_mini/io/`** — `audio_ws.py`, `video_ws.py`, `ws_controller.py` all deleted. WebSocket-based I/O is gone, replaced by WebRTC.
- **`gst-signalling` dependency removed** — signaling is now handled internally.
- **Old documentation** (`docs/old_doc/`) wiped out.
- **Web dashboard has a deprecation banner** — being replaced by a desktop app.
- **Remote control code removed** from the SDK (simplified).
- **`video_udp.py`** in MuJoCo backend removed — MuJoCo now uses GStreamer for camera streaming.

### Restructured

- **Tests** split into `tests/unit_tests/` and `tests/integration_tests/`.
- **Debug examples** moved to integration tests or promoted to proper examples.
- **Docs** fully restructured under `docs/source/` with a proper `_toctree.yml`.
- **Dependencies** moved to `dependency-groups` in `pyproject.toml`.

---

## Developer Experience

### AI Agent Support

They went all-in on AI-assisted development:

- **`AGENTS.md`** — comprehensive guide for AI agents working on the codebase.
- **`CLAUDE.md`** — Claude-specific instructions.
- **`agents.local.md.template`** — per-session context template.
- **`skills/`** directory — 12 markdown guides covering everything from app creation to motion philosophy to safe torque handling.

### CI/CD

- **Physical CI pipeline** added (runs on actual Reachy Mini hardware).
- Docs build/preview CI for PRs.
- Better mypy workflow.
- uv lock check in CI.

### Tooling

- **Camera calibration suite** added (`src/reachy_mini/tools/camera_calibration/`).
- **OpenAPI spec generation** script (`scripts/generate_openapi.py`).
- Motor scan now supports `--wireless` and `--port` CLI args.
- Motor reflash only happens if config is actually incorrect (no more systematic reflash at startup).

---

## Dependencies

| Change | Detail |
|--------|--------|
| NumPy | Bumped to 2.2.5+ |
| HuggingFace Hub | Pinned to 1.3.0 |
| Websockets | Range broadened (12–16) |
| Python CI | Bumped to 3.12 |
| Platform-specific | PyGObject (Linux), pycaw (Windows), pulsectl (Linux audio) |

---

## TL;DR

The project's center of gravity shifted toward **remote/wireless operation** (WebRTC, HF auth, JS SDK) and **cross-platform support**. The old WebSocket I/O layer is gone. GStreamer is now the default media backend. The web dashboard is being deprecated for a desktop app. They also heavily invested in AI-assisted development tooling. Our fork had zero custom commits so the merge was clean — but the WebRTC/media stack is substantially different from 6 months ago.
