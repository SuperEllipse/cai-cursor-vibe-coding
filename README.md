# Cursor + Cloudera AI (Remote SSH)

Scripts to connect **Cursor IDE** to a Cloudera AI project session via Remote SSH, automating the per-session steps from the [Cursor Remote SSH guide](https://superellipse.github.io/zeta-hol/vibe-coding/cursor-remote-ssh/).

## STOP — check prerequisites before running

You **must** complete every item below. The script will not work otherwise.

1. **`cdswctl`** — download for your OS from **User Settings → Keys & Access → Remote Editing**, add to `$PATH`, and allow it to run on macOS if prompted.
2. **SSH key** — generate with `ssh-keygen -C cai` (save as `cai`), move to `~/.ssh/cai` and `~/.ssh/cai.pub`, and add the public key under **Remote Editing** in Cloudera AI.
3. **API key** — create under **User Settings → Keys & Access → API Keys** (save it immediately; shown only once). **Do not use a Legacy API key** — create a new API key in Cloudera AI if you see a 401 on login.
4. **CAI project (mandatory)** — create a project in the Cloudera AI Workbench **before** running this script. Set `PROJECT_NAME` in `connect-cai.env` to the **full project slug**:

   ```
   <CAI_USERNAME>/<project-name>
   ```

   Example: if `CAI_USERNAME` is `jane` and your project is `my-demo`, use `PROJECT_NAME="jane/my-demo"`. **Do not** use just the project name (`my-demo` alone will fail). For team projects, use the owner slug from the browser URL (e.g. `team-owner/shared-project`).

5. **`jq`** — required for automatic runtime selection (`brew install jq` on macOS).

### What the script checks automatically

When you run the script, it verifies:

| Check | Verified automatically? |
|-------|-------------------------|
| `cdswctl` installed and runnable | Yes |
| `jq` installed and runnable | Yes |
| SSH private + public key files exist | Yes |
| SSH key is valid and not passphrase-protected | Yes |
| `API_KEY` set in `connect-cai.env` (not a placeholder) | Yes |
| API key works against CAI | Yes (at login) |
| `PROJECT_NAME` is full slug `<username>/<project>` | Yes |
| SSH public key uploaded to CAI Remote Editing | **No** — you must confirm this manually |

If the SSH public key was not uploaded to CAI, login may succeed but SSH/Cursor connection will fail.

See the [full documentation](https://superellipse.github.io/cai-cursor-vibe-coding/) for screenshots and troubleshooting (quickstart, prerequisites, and step-by-step connecting guide).

## Quick start

```bash
git clone https://github.com/SuperEllipse/cai-cursor-vibe-coding
cd cai-cursor-vibe-coding

cp connect-cai.env.example connect-cai.env
```

Edit `connect-cai.env` and set these **mandatory** values:

```bash
CAI_DOMAIN="https://your-cai-domain.cloudera.site"
CAI_USERNAME="your-username"
API_KEY="your-api-key"
PROJECT_NAME="your-username/your-project-name"
```

`PROJECT_NAME` must be the full slug (`<username>/<project-name>`), not just the project name.

```bash
CONNECT_CAI_ENV=./connect-cai.env ./connect-cursor-cai.sh
```

Keep the terminal open while using Cursor. Press **Ctrl+C** to stop the SSH tunnel.

## Connect in Cursor

Once you see **SSH tunnel is active**:

1. **Cmd+Shift+P** (Mac) or **Ctrl+Shift+P** (Windows/Linux) → **Remote-SSH: Connect to Host**
2. Choose **`cai-workbench`**
3. **File → Open Folder** → `/home/cdsw`

Optional sanity check in a second terminal:

```bash
ssh -i ~/.ssh/cai -p 3735 cdsw@localhost
```

The script creates or updates the `cai-workbench` entry in `~/.ssh/config`. It prefers port `3735` but automatically picks the next free port if that one is busy, then updates the config so Cursor always connects via `cai-workbench`.

## Optional flags

| Flag | Description |
|------|-------------|
| `--gpu` | Select an Nvidia GPU runtime edition (default: Standard PBJ Workbench) |
| `--spark` | Attach a Spark 3.4 runtime add-on |
| `--new-session` | Start a new session instead of reusing an existing one |
| `--tunnel-only` | Reconnect the SSH tunnel to `SESSION_ID` in `connect-cai.env` |
| `--help` | Show usage |

Example:

```bash
CONNECT_CAI_ENV=./connect-cai.env ./connect-cursor-cai.sh --spark
```

## Configuration

All settings live in `connect-cai.env` (gitignored). See `connect-cai.env.example` for available variables.

| Variable | Default | Description |
|----------|---------|-------------|
| `PYTHON_VERSION` | `3.12` | Python kernel to match |
| `SESSION_CPU` | `2` | CPU cores |
| `SESSION_MEMORY` | `4` | Memory (GB) |
| `SESSION_GPU` | `0` | GPU count |
| `SSH_LOCAL_PORT` | `3735` | Fixed local SSH port |
| `USE_SPARK` | `false` | Set `true` or pass `--spark` to enable Spark |

## What the script does

1. Validates prerequisites (`cdswctl`, `jq`, SSH key)
2. Logs in to Cloudera AI
3. Auto-selects a **PBJ Workbench** runtime (Python 3.12, Standard edition)
4. Reuses an existing running session when possible, or starts a new one
5. Updates `~/.ssh/config` with the local port in use and starts the SSH tunnel
6. Prints Cursor connection instructions once the tunnel is active

## Files

| File | Purpose |
|------|---------|
| `connect-cursor-cai.sh` | Main script |
| `connect-cai.env.example` | Config template |
| `connect-cai.env` | Your local config (create from example; not committed) |
