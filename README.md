# Chromium Canary Submission — Ajay Kumar Mishra (@Aj4y7)

## 1. Loom Walkthrough Video
* **Video URL:** [Watch Walkthrough Video](https://www.loom.com/share/fe64227222f0427a818ce38e2bbcf385)
  *(Link sharing is set to "Anyone with the link can view")*

## 2. Build Info
* **Chromium Commit ID (`git rev-parse HEAD`):** `0847b2e9000537b8c59610eaafb0678cff7ad475`
* **Target Built:** `content_shell`
* **My Laptop OS & RAM:** Arch Linux (64-bit), 16GB RAM + 16GB Swap

## 3. Architecture Notes
* **Feature A (`X-Candidate-Canary` Header):** Registered `--inject-candidate-id` in `content_switches` and forwarded it from the Browser process to child Blink Renderer processes via `kForwardSwitches` in `ShellContentBrowserClient`. Attached `CandidateCanaryThrottle` (`blink::URLLoaderThrottle`) in both `ShellContentBrowserClient` (browser navigations) and `ShellContentRendererUrlLoaderThrottleProvider` (renderer `fetch`/XHR requests), registering `X-Candidate-Canary` in `cors_exempt_header_list` and setting it on `request->cors_exempt_headers` so `DCHECK` CORS preflight checks pass cleanly.
* **Feature B (`[CANARY_ACK]` Console Hook):** Hooked `FrameConsole::ReportMessageToClient` inside `third_party/blink/renderer/core/frame/frame_console.cc` so the entire diff stays within Chromium's main `src/` repo without modifying the `v8/` submodule. When `console.warn("canary-ping")` runs, it captures the current UTC timestamp and logs `[CANARY_ACK] Verified build for Aj4y7 at <Timestamp>` to `stderr` via `LOG(WARNING)` while leaving other console calls unchanged.

## 4. AI Agent Workflow
* **AI Tool & Model Used:** Cline (VS Code extension)
* **How I Used Sub-Agents:** Split exploration into targeted search tasks before editing code—one pass traced `console.warn` dispatch inside `third_party/blink/renderer/core/frame/` and verified allowed `base/` headers against Blink's `DEPS` rules, while a second pass traced `base::CommandLine` flag forwarding across `ShellContentBrowserClient` and `URLLoaderThrottle` attachment points in `content/shell/`. Using exact file paths and line numbers kept the final diff focused on `.cc` implementation files without triggering full-tree header rebuilds.
