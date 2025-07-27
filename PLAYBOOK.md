# PLAYBOOK.md

This document serves as a comprehensive guide for maintaining and
troubleshooting our SaaS Golden Template project, built on the SVN
Stack (Supabase, Vercel, Next.js), with a focus on AI agent
integration via Gemini CLI and n8n. It also provides critical
context for AI assistants working within this environment.

## Top 5 Most Common Issues

1.  **AI Agent (Gemini CLI) Inability to Directly Access
    Localhost:** The primary challenge of AI models not being able to
    directly interact with local services (like the n8n MCP server
    running on `localhost`) was consistently encountered and resolved
    by guiding the user to manually execute commands and provide
    output.
2.  **Achieving Cost-Effective AI-Driven Workflow Generation:**
    Successfully establishing a local, free alternative to paid AI
    desktop applications (like Claude Desktop) for n8n workflow
    generation was a recurring problem that was effectively addressed.
3.  **Integration of Disparate Systems:** Bridging Gemini CLI, the
    n8n Documentation MCP Server, and the n8n application required
    careful configuration and scripting, which was a frequent point of
    resolution.
4.  **Environment Variable Configuration for SaaS Application:**
    Issues related to correctly setting up `NEXT_PUBLIC_SUPABASE_URL`
    and `NEXT_PUBLIC_SUPABASE_ANON_KEY` in `.env.local` for local
    development and in Vercel for deployment were common.
5.  **Ensuring n8n and MCP Server Operational Status:** Verifying
    that both the n8n application and the n8n Documentation MCP Server
    were running correctly and accessible was a foundational and
    frequently addressed issue.

## 🏗️ Critical Context for AI Assistants

* **Operating System:** Arch Linux
* **SaaS Golden Template Stack:** SVN (Supabase, Vercel, Next.js)
* **Primary AI Agent:** Gemini CLI
* **Workflow Automation Platform:** n8n (accessible at
    `http://localhost:5678` with credentials `admin`/`strong-password`)
* **AI-n8n Bridge:** n8n Documentation MCP Server (running on
    `http://localhost:3000`)
* **MCP Authentication Token:**
    `776c70c597e5ca9d2b5d7ca886b12740b5f4c66a5865c764552ffb988cb75229`
    (required for POST requests to `/mcp`)
* **Project Goal:** To provide a cost-effective, local, and
    AI-driven solution for n8n workflow generation, minimizing reliance
    on external paid services.
* **Shell Environment:** Users may be using Bash or Fish. If
    switching between Bash and Fish, do not source `~/.bashrc` from
    Fish; instead, copy/translate what is needed into Fish syntax and
    place it in `~/.config/fish/config.fish`.

## 🔧 Categorized Troubleshooting Guide

### SaaS App Setup

* **Symptom:** Next.js application fails to start or connect to
    Supabase locally.
    * **Cause:** Missing or incorrect environment variables in
    `.env.local`.
    * **Solution:** Create a `.env.local` file in the project
    root (if it doesnt exist) and populate it with
    `NEXT_PUBLIC_SUPABASE_URL` and `NEXT_PUBLIC_SUPABASE_ANON_KEY` from
    your Supabase project settings. Ensure the values are accurate.
* **Symptom:** Vercel deployment fails or the deployed
    application cannot connect to Supabase.
    * **Cause:** Environment variables are not configured in the
    Vercel project settings.
    * **Solution:** In your Vercel dashboard, navigate to your
    project settings and add `NEXT_PUBLIC_SUPABASE_URL` and
    `NEXT_PUBLIC_SUPABASE_ANON_KEY` as environment variables for all
    relevant environments (Production, Preview, Development).

### Local AI CLI Tools

* **Symptom:** Gemini CLI reports it cannot access
    `http://localhost:3000` (n8n MCP Server).
    * **Cause:** AI models operate in a sandboxed environment and
    cannot directly access services running on your local machines
    `localhost`.
    * **Solution:** The user must manually execute shell commands
    (e.g., `curl http://localhost:3000/health` or `curl -X POST -H
    "Authorization: Bearer <token>" -H "Content-Type: application/json"
    -d {"jsonrpc":"2.0","method":"getNodes","id":1}
    http://localhost:3000/mcp`) on their local terminal and then
    provide the output to the AI.
* **Symptom:** n8n Documentation MCP Server is not responding or
    accessible on port 3000.
    * **Cause:** The MCP server process is not running, has
    crashed, or is blocked by a local firewall.
    * **Solution:** Verify the MCP server process is active.
    Check its logs for errors. Ensure no firewall rules are blocking
    access to port 3000. Restart the MCP server if necessary.
* **Symptom:** Gemini CLI integration scripts (e.g.,
    `mcp_interface.sh`, `generate_workflow.sh`) are not executing
    correctly or producing expected output.
    * **Cause:** Incorrect script paths, missing shell
    dependencies (like `curl`, `jq`), or syntax errors within the
    scripts.
    * **Solution:** Confirm the scripts are in the correct
    directory and have execute permissions (`chmod +x script_name.sh`).
    Ensure all necessary command-line tools are installed (`sudo pacman
    -S curl jq`). Review the script content for any typos or logical
    errors.

### Arch Linux System

* **Symptom:** Essential development tools (e.g., `npm`, `git`,
    `node`) are not found or are outdated.
    * **Cause:** Required packages are not installed or have not
    been updated recently on the Arch Linux system.
    * **Solution:** Use the `pacman` package manager to install
    or update necessary software: `sudo pacman -S nodejs npm git`.
    Regularly run `sudo pacman -Syu` to keep the system and packages
    up-to-date.
* **Symptom:** Environment variables (e.g., `PATH`) are not
    correctly configured, leading to command not found errors for newly
    installed tools.
    * **Cause:** The shells configuration file (e.g.,
    `~/.bashrc`, `~/.zshrc`, `~/.config/fish/config.fish`) does not
    correctly define or export the `PATH`.
    * **Solution:** Edit the appropriate shell configuration file
    to ensure the directories containing executables are included in
    the `PATH`. After editing, `source` the file (e.g., `source
    ~/.bashrc`) or restart the terminal.
* **Symptom:** General system sluggishness or resource exhaustion
    during development tasks.
    * **Cause:** High CPU/memory usage by background processes,
    insufficient RAM, or unoptimized system settings.
    * **Solution:** Use tools like `htop` or `top` to identify
    resource-intensive processes. Consider closing unnecessary
    applications. Ensure sufficient swap space is configured. Review
    system logs for recurring errors that might indicate underlying
    issues.
