# Cursor + Cloudera AI — Vibe Coding

Connect **Cursor IDE** to your Cloudera AI workbench session using **Remote SSH**, so you can use AI-assisted coding directly against your project environment, data connections, and runtimes.

This guide has two paths:

1. **[Quickstart](quickstart.md)** — minimal checklist: clone, configure, run the script, connect.
2. **[Step by Step Setup](prerequisites.md)** — detailed guides with screenshots:
    - **[Prerequisites](prerequisites.md)** — one-time setup: SSH keys, `cdswctl`, API keys, and SSH config.
    - **[Connecting Cursor to CAI](connecting-cursor.md)** — per-session walkthrough with screenshots.

## Quick overview

```mermaid
flowchart LR
    subgraph once["One-time setup (per tenant & project)"]
        direction LR
        A["Prerequisites<br/>SSH keys · cdswctl · API key"]
        B["Clone repo &<br/>configure connect-cai.env"]
        A --> B
    end

    subgraph session["Each session"]
        direction LR
        C["Run<br/>connect-cursor-cai.sh"]
        D["SSH tunnel<br/>active"]
        E["Remote-SSH<br/>in Cursor"]
        F["Open<br/>/home/cdsw"]
        C --> D --> E --> F
    end

    B --> C

    style once fill:#fff3e0,stroke:#e65100,color:#333
    style session fill:#e3f2fd,stroke:#1565c0,color:#333
    style A fill:#ffe0b2,stroke:#e65100
    style F fill:#c8e6c9,stroke:#2e7d32
```

After one-time setup for a tenant and project, each session is quick: run the script, then connect Cursor via **Remote-SSH: Connect to Host** → `cai-workbench`.

## Repository

Clone the connection scripts from GitHub:

```bash
git clone https://github.com/SuperEllipse/cai-cursor-vibe-coding
```

| File | Purpose |
|------|---------|
| `connect-cursor-cai.sh` | Main connection script |
| `connect-cai.env.example` | Configuration template |
| `connect-cai.env` | Your local config (create from example; not committed) |
