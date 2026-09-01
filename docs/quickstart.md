# Quickstart

Get connected in a few minutes. No screenshots — just a checklist, the commands to run, and what you should see.

!!! tip "Need more detail?"
    For step-by-step screenshots, see **[Step by Step Setup](connecting-cursor.md)** → **Connecting Cursor to CAI**. For one-time setup (SSH keys, `cdswctl`, API keys), see **[Prerequisites](prerequisites.md)**.

---

## Prerequisites checklist

Complete every item before running the script.

### One-time setup (per tenant & project)

- [ ] **Cursor IDE** installed ([cursor.com](https://cursor.com))
- [ ] **CAI project** created in the Cloudera AI Workbench
- [ ] **SSH key pair** generated (`ssh-keygen -C cai`) and moved to `~/.ssh/cai` / `~/.ssh/cai.pub`
- [ ] **SSH public key** added in CAI → **User Settings → Keys & Access → Remote Editing**
- [ ] **`cdswctl`** downloaded, unblocked (macOS), and on your `PATH`
- [ ] **`jq`** installed (`brew install jq` on macOS)
- [ ] **API key** created in CAI → **User Settings → Keys & Access → API Keys** (not Legacy)
- [ ] **`cai-workbench`** entry added to `~/.ssh/config` (see [Prerequisites](prerequisites.md#8-create-the-ssh-config-entry))
- [ ] **Repository cloned** and **`connect-cai.env`** configured with domain, username, API key, and project slug

### Each session

- [ ] **Cursor** open with your local project folder
- [ ] Run **`connect-cursor-cai.sh`** (see step 3 below)

---

## 1. Clone the repository

In Cursor's integrated terminal:

```bash
git clone https://github.com/SuperEllipse/cai-cursor-vibe-coding
cd cai-cursor-vibe-coding
```

## 2. Configure your environment

```bash
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

## 3. Run the connection script

```bash
CONNECT_CAI_ENV=./connect-cai.env ./connect-cursor-cai.sh
```

Keep this terminal open while you work in Cursor. Press **Ctrl+C** when finished to stop the tunnel.

## 4. Expected output

When everything is configured correctly, you should see output similar to this:

```text
*******************************************************************************
  STOP — Have you completed all prerequisite steps? (see script header)
*******************************************************************************
  The script will verify each item below before continuing:

  [1] cdswctl installed and on PATH
  [2] jq installed and on PATH
  [3] SSH key pair generated (~/.ssh/cai and ~/.ssh/cai.pub)
  [4] API_KEY set in connect-cai.env (current CAI API key, not Legacy)
  [5] PROJECT_NAME set to <CAI_USERNAME>/<project-name>
  [6] SSH public key uploaded to CAI → User Settings → Remote Editing
      (cannot be verified automatically — you must confirm this yourself)
*******************************************************************************

==> Running prerequisite checks...
==> [1/6] cdswctl: OK
==> [2/6] jq: OK
==> [3/6] SSH key pair: OK (~/.ssh/cai)
==> [4/6] API_KEY configured in ./connect-cai.env: OK
==> [5/6] PROJECT_NAME: OK (your-username/your-project-name)
warning: [6/6] SSH public key in CAI cannot be verified automatically.
warning:       Confirm you have pasted this key into Cloudera AI:
warning:       User Settings → Keys & Access → Remote Editing → SSH Public Key
warning:       Public key file: ~/.ssh/cai.pub
warning:       Preview: ssh-ed25519 AAAA...
==> Prerequisite checks passed (project: your-username/your-project-name)
==> Logging in to https://your-cai-domain.cloudera.site as your-username...
Login succeeded
==> Auto-selecting PBJ Workbench runtime (Python 3.12, standard edition)...
==> Selected runtime ID: 838
==> Starting session (cpu=4, memory=4GB, gpu=0)...
==> Session started: abc123sessionid
==> Updated ~/.ssh/config (Host cai-workbench, Port 3735)
==> Starting SSH endpoint (session=abc123sessionid, local port=3735)...
Forwarding local port 3735 to port 2222 on session abc123sessionid in project your-username/your-project-name.
You can SSH to the session using: ssh -p 3735 cdsw@localhost

===============================================================================
  SSH tunnel is active — keep this terminal open.
================================================================================

Sanity check (optional, in another terminal):
  ssh -i ~/.ssh/cai -p 3735 cdsw@localhost

Connect in Cursor:
  1. Cmd+Shift+P (Mac) or Ctrl+Shift+P (Windows/Linux)
  2. "Remote-SSH: Connect to Host"
  3. Choose: cai-workbench
  4. File → Open Folder → /home/cdsw

When finished:
  Press Ctrl+C here to stop the SSH tunnel.
  The CAI session may still be running — stop it in the Workbench UI if needed.

==> Press Ctrl+C to stop the tunnel.
```

!!! note "Session startup"
    Starting a session can take a minute or two depending on cluster resources and quotas. Verify the session shows **Running** in CAI → **Project → Sessions** if startup seems slow.

## 5. Connect in Cursor

Once you see **SSH tunnel is active**:

1. **Cmd+Shift+P** (Mac) or **Ctrl+Shift+P** (Windows/Linux)
2. Select **Remote-SSH: Connect to Host**
3. Choose **`cai-workbench`**
4. **File → Open Folder → `/home/cdsw`**

Optional sanity check in a second terminal:

```bash
ssh -i ~/.ssh/cai -p 3735 cdsw@localhost
```

## Done

You are now connected. The window title should show `cdsw [SSH: cai-workbench]`.

When finished:

- Press **Ctrl+C** in the script terminal to stop the tunnel
- Stop the CAI session in the Workbench UI if you want to free resources
