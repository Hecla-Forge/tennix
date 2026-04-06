# Tenixx: AI-Powered Visual Testing for Humans and Agents
Released by Hecla Technology Forge

**Tenixx is a command-line visual testing engine that uses AI to verify user interfaces.** It's built for modern development workflows, enabling both human developers and AI agents to write, run, and debug visual tests with unprecedented speed and consistency.

**Keywords:** `Visual Testing`, `AI Testing`, `Playwright`, `Test Automation`, `LLM`, `Vision AI`, `Gemini`, `Claude`, `TDD`, `Agent-Based Testing`, `CLI Tool`, `DevOps`, `CI/CD`, `E2E Testing`, `UI Automation`


## How It Works

Tenixx uses a **Docker sidecar** to run a pinned version of Chromium, ensuring every test runs in an identical, isolated environment. Instead of complex assertions, you write a natural language prompt. Tenixx takes a screenshot, sends it to a Vision LLM (like Google Gemini or Anthropic Claude), and returns a `PASS` or `FAIL` verdict based on the AI's understanding of the UI.

This approach is perfect for testing complex, dynamic, or hard-to-selectorize elements where traditional automation fails.

## Key Features

-   **AI-Powered Visual Verdicts:** Write prompts in plain English. The AI determines if the UI is correct.
-   **Cross-Platform Consistency:** The Docker sidecar guarantees tests render identically on Windows, macOS, and Linux. No more "works on my machine."
-   **Agent-First Design (stdin/stdout):** Tenixx is a composable CLI tool. Pipe test plans in, receive clean JSON reports out. Perfect for integration with AI agents like Claude, Zed, or VS Code Copilot.
-   **Visual TDD Workflow:** Use the `--a11y` flag to inspect a page's accessibility tree, write a failing test for a feature that doesn't exist yet (Red), implement it, and re-run to confirm (Green).
-   **Automatic Failure Analysis:** On any test failure, the full ARIA accessibility tree is automatically captured, giving agents immediate context to debug and self-heal.
-   **Secure by Default:** Secrets are stored in the native OS keyring (`libsecret`, Keychain, Credential Manager), never in files.

---

## Use Cases

Tenixx excels at tests that are difficult or flaky with traditional tools:

-   **Validating complex data visualizations:** "Is there a line chart showing an upward trend?"
-   **Checking for correct branding and styling:** "Is the logo in the top-left and is the primary button blue?"
-   **Verifying dynamic content or A/B tests:** "Does the hero section contain an image of a person or a product?"
-   **End-to-end user journey validation:** "After login, does the dashboard show a welcome message with the user's name?"
-   **Agent-driven development:** An AI agent can write a test, see it fail, inspect the UI structure from the report, write the feature code, and re-run the test to confirm success, all without opening a browser.

---

## Installation & Setup

### 1. Prerequisite: Install Docker

Tenixx requires Docker Desktop. It must be running in the background.
-   **Install Docker Desktop:** [https://docs.docker.com/get-docker/](https://docs.docker.com/get-docker/)

### 2. Download the `tenixx` Binary

Download the latest pre-compiled binary for your OS from the **[GitHub Releases](https://github.com/Hecla-Forge/tennix/releases/tag/latest)** page.

Place the executable in a directory on your system's `PATH`.

### 3. Initialize & Install

```bash
# Must be run from your project directory

# 1. Create the .tenixx/ workspace and download skill files
tenixx init

# 2. Pull the sidecar Docker image
tenixx install

# 3. Configure your AI provider and API key
tenixx config
```

You are now ready to run tests.

---

## Agent-Driven Testing (stdin/stdout)

Tenixx's primary design is as a composable tool for AI agents.

**The Workflow:**
1.  An agent generates a test plan (a simple Markdown file).
2.  The agent pipes this plan into Tenixx via `stdin`.
3.  Tenixx runs the test, prints all progress logs to `stderr`, and prints **only the final JSON report** to `stdout`.
4.  The agent reads the JSON from `stdout` and checks the process `exit code`:
    -   `0`: Test passed.
    -   `1`: Test failed. The agent reads the `brain_verdict` and `accessibility_tree` from the JSON to understand why.
    -   `2`: Infrastructure error. The agent knows there was a problem with Docker or the environment.

**Example: A TDD Cycle Driven by an Agent**

```bash
# Phase 1: RED (Test a feature that doesn't exist yet)
# The agent generates and pipes this test. The --a11y flag is crucial.
cat new_feature_test.md | tenixx test --a11y

# The agent gets exit code 1 (FAIL).
# It parses the accessibility_tree from the JSON report on stdout to learn
# the current page structure.
# Based on the tree, it implements the new feature in the source code.

# Phase 2: GREEN (Confirm the feature now works)
cat new_feature_test.md | tenixx test

# The agent gets exit code 0 (PASS). The cycle is complete.
```

For the complete guide on this workflow, see the skill file downloaded by `tenixx init`:
**`.tenixx/skills/tdd_workflow.md`**

---

## Repeatable Testing with Local Playwright

By default, Tenixx uses the Docker sidecar for maximum consistency. This is ideal for CI/CD and ensuring tests are platform-agnostic.

However, if your development environment warrants it (e.g., you are working offline, have a highly customized local setup, or do not have Docker), you can run tests using a **local Playwright installation**. This enables repeatable local testing without Docker, but sacrifices the cross-platform guarantee.

To enable this, install the local recorder dependencies:

```bash
tenixx install --record
```

This command will download the Playwright browsers and drivers to your local machine. From then on, `tenixx test` will detect and use this local installation instead of the Docker sidecar. This is also required for using the `tenixx record` command.
