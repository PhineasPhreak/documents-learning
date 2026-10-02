# Opencode and LM Studio Installation and Configuration Guide

This guide explains how to install Opencode on various operating systems (Linux and Windows), configure it to use local models served by LM Studio, and verify that everything is working correctly.

## 1. Prerequisites

Before starting, ensure you have installed the following:
*   **Operating System:** Linux (Ubuntu, Fedora, Arch, etc.) or Windows.
*   **Base Tools:** `curl` (for Linux/macOS) and PowerShell or CMD (for Windows).
*   **LM Studio:** Download and install LM Studio from [https://lmstudio.ai/](https://lmstudio.ai/).

## 2. Opencode Installation

### For Linux (Ubuntu, Fedora, Arch)

Use the official installation script via `curl`.

```bash
curl -fsSL https://opencode.ai/install | bash
```

**After Installation:**
Restart your terminal or reload your shell configuration for the changes to take effect:
```bash
source ~/.bashrc  # For Bash
# OR
source ~/.zshrc   # For Zsh
```

**Verification:**
```bash
opencode --version
```

### For Windows

The installation method may vary. Use the official installer or follow the specific instructions provided on the Opencode website for Windows environments. If using WSL (Windows Subsystem for Linux), use the Linux installation steps above within your WSL environment.

**Verification:**
Ensure the `opencode` command is accessible from your terminal (CMD, PowerShell, or WSL).

## 3. LM Studio Configuration (Local Server Setup)

This step must be performed **within LM Studio**, regardless of your operating system.

1.  **Download a Model:** Open LM Studio and download the desired compatible model (e.g., Gemma 4B, Qwen 3 32B Instruct, Llama 3.3 70B Instruct).
2.  **Enable the API Server:**
    *   Navigate to the **Developer** section $\rightarrow$ **Local Server**.
    *   Enable the option: **Start Server**.
3.  **Default Endpoint:** The server will typically run at: `http://127.0.0.1:1234`.
    *   The full API endpoint for requests is: `http://127.0.0.1:1234/v1`

**Server Verification (From your Terminal):**
Use `curl` to confirm that the server is running and exposing models:
```bash
curl http://127.0.0.1:1234/v1/models
```
*   **Success:** You should receive a JSON response listing the loaded models (e.g., `{"data": [{"id": "qwen3-32b"}]}`).
*   **Issue (Connection refused):** The LM Studio server is not running or is listening on a different port. Verify that the server is active in the LM Studio interface.

## 4. Opencode Configuration

You must configure Opencode to connect to the local LM Studio server via its configuration file.

### For Linux (Ubuntu/Fedora)

1.  **Create the configuration directory:**
    ```bash
    mkdir -p ~/.config/opencode
    ```
2.  **Create or edit the configuration file:**
    ```bash
    nano ~/.config/opencode/opencode.jsonc
    ```
3.  **Insert the Configuration (Example with Qwen 3 32B):**

    ```jsonc
    {
      "$schema": "https://opencode.ai/config.json",
      "provider": {
        "lmstudio": {
          "type": "openai",
          "name": "LM Studio (local)",
          "options": {
            "baseURL": "http://localhost:1234/v1"
          },
          "models": {
            "google/gemma-4-e4b": {
              "name": "Gemma4 E4B Q6_K"
            }
          }
        }
      }
    }
    ```

### For Windows

1.  **Create the configuration directory:**
    The path will typically be under your user profile, such as `%USERPROFILE%\.config\opencode` (check specific installation paths).
2.  **Create or edit the configuration file:**
    Use a text editor (like VS Code or Notepad) to create `opencode.jsonc` in that directory.
3.  **Insert the Configuration:** The JSON/JSONC content remains **identical** to the Linux example provided above.

## 5. Final Verification

Run Opencode from your terminal to check the direct connection to the local server:

```bash
opencode
```
If everything is configured correctly, Opencode should connect directly to the local LM Studio API and begin functioning.

## Useful Commands (Cross-Platform)

| Command                                      | Description                                                                               |
|:---------------------------------------------|:------------------------------------------------------------------------------------------|
| `opencode --help`                            | Display the complete help documentation for the Opencode command.                         |
| `opencode --version`                         | Display the current version of Opencode.                                                  |
| `curl http://127.0.0.1:1234/v1/models \| jq` | List all models currently loaded by the LM Studio server (Requires `jq` to be installed). |

## Troubleshooting

| Issue                   | Probable Cause                                                           | Solution                                                                                                                               |
|:------------------------|:-------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------|
| **Connection refused**  | The LM Studio server is not running or is listening on a different port. | Check the LM Studio interface and ensure that `Start Server` is activated.                                                             |
| **Model not found**     | The model identifier in `opencode.jsonc` is incorrect.                   | Execute `curl http://127.0.0.1:1234/v1/models` to get the exact list and update the `"model": "..."` entry in your configuration file. |
| **Port already in use** | Another service is using the default port (1234).                        | Change the port in your configuration. Example: `"baseURL": "http://127.0.0.1:8080/v1"`.                                               |
