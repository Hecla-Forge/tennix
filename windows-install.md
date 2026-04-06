### Step 1: Install & Start Docker Desktop

This is the only major dependency for running tests.
*   **Download and install Docker Desktop for Windows** from the official site: [https://docs.docker.com/get-docker/](https://docs.docker.com/get-docker/)
*   **Start Docker Desktop.** You must have it running in the background before using Tenixx.

*(This step enables the consistent Playwright and its browser environment locally.)*

### Step 2: Download `tenixx.exe`

*   Download the `tenixx.exe` binary.
*   Place it in a folder and add that folder to your system's `PATH` environment variable. This allows you to run `tenixx` from any command prompt.

### Step 3: Install the Tenixx Test Engine

Open a Command Prompt or PowerShell and run:
```bash
tenixx install
```
This command communicates with your running Docker instance and pulls the `tenixx-sidecar:latest` image from the Docker repository. This contains the programmable browser and all necessary drivers, ensuring a consistent test environment.

### Step 4: Configure your AI Provider

Run the interactive configuration to select your AI vision provider (Gemini or Claude) and securely save your API key:
```bash
tenixx config
```

At this point, you are fully set up to **run tests**.

---

### Step 5 (Optional): Install the Recorder

If, and only if, you want to use the interactive recorder to generate new test scripts, you also need to install Playwright locally.

Run the install command again with the `--record` flag:
```bash
tenixx install --record
```
This will download and set up the local Playwright browsers and drivers needed specifically for the `tenixx record` command.

---

### Summary for a Windows User

**To run tests:**
1.  Install and run Docker Desktop.
2.  Download `tenixx.exe` and add to PATH.
3.  Run `tenixx install`.
4.  Run `tenixx config`.

**To *also* record new tests:**
5.  Run `tenixx install --record`.
