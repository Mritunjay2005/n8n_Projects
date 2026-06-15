# n8n Local Setup Guide (Docker Desktop + Git Bash)

This guide walks you through setting up **n8n** (a workflow automation tool) on your Windows PC using **Docker Desktop** and **Git Bash**, with data that persists every time you stop and restart it.

---

## 0. Download and Set Up Git Bash

Git Bash gives you a terminal where you can run Docker commands.

1. Go to https://git-scm.com/downloads
2. Download the Windows installer.
3. Run the installer and click **Next** through the setup screens (default options are fine for everything).
4. Once installed, open **Git Bash** from the Start Menu (search "Git Bash").
5. You should see a terminal window with a prompt like:
   ```
   yourname@LAPTOP MINGW64 ~
   $
   ```

---

## 1. Download and Set Up Docker Desktop

Docker Desktop runs the containers (including n8n) on your machine.

1. Go to https://www.docker.com/products/docker-desktop/
2. Download Docker Desktop for Windows.
3. Run the installer (keep default settings, including WSL 2 backend if prompted).
4. Restart your computer if asked.
5. Open **Docker Desktop** from the Start Menu and wait until it shows **"Engine running"** (usually a green status near the bottom left).
6. Keep Docker Desktop running in the background whenever you want to use n8n.

---

## 2. Create a Folder and Set Up n8n (with Docker)

We'll create a project folder, a data folder for persistence, and run n8n so it can be stopped and restarted anytime without losing data.

### 2.1 Create the project folder

In Git Bash, navigate to where you want your project (example: Desktop), then create folders:

```bash
cd ~/Desktop
mkdir n8n-project
cd n8n-project
mkdir n8n-data
```

Your structure should now look like:
```
Desktop/
└── n8n-project/
    └── n8n-data/
```

### 2.2 Run n8n for the first time

While inside the `n8n-project` folder, run:

```bash
docker run -d --name n8n -p 5678:5678 --restart unless-stopped -v ~/Desktop/n8n-project/n8n-data:/home/node/.n8n docker.n8n.io/n8nio/n8n
```

What each part means:
- `-d` → runs in the background (detached mode)
- `--name n8n` → names the container "n8n" for easy reference
- `-p 5678:5678` → makes n8n available on port 5678
- `--restart unless-stopped` → n8n auto-starts when Docker Desktop starts
- `-v ~/Desktop/n8n-project/n8n-data:/home/node/.n8n` → saves all your workflows, credentials, and settings to your `n8n-data` folder (this is what makes your data persistent)

The first time, it will download the n8n image — this may take a few minutes depending on your internet speed.

### 2.3 Stop n8n (when you're done for the day)

```bash
docker stop n8n
```

This stops the container but **keeps all your data** safe.

### 2.4 Start n8n again (next time, e.g. tomorrow)

```bash
docker start n8n
```

That's it — n8n comes back exactly as you left it (same login, same workflows).

### 2.5 Check if n8n is running

```bash
docker ps
```

If you see `n8n` listed with status "Up", it's running.

---

## 3. Open n8n in Your Browser

1. Open any web browser (Chrome, Edge, Firefox, etc.).
2. In the address bar, type and go to:
   ```
   http://localhost:5678
   ```
3. **First time only**: you'll be asked to create an owner account (name, email, password). Fill this in and remember it — this is your login for n8n.
4. **Every time after that**: just go to `http://localhost:5678` and log in with that account.
5. You're now in the n8n editor — start building your workflows!

---

## Quick Reference (Daily Use)

| Action | Command (in Git Bash) |
|---|---|
| Start n8n | `docker start n8n` |
| Stop n8n | `docker stop n8n` |
| Check status | `docker ps` |
| Open n8n | Visit `http://localhost:5678` in browser |

**Note:** Docker Desktop must be open and running for any of these commands to work.

---

## Troubleshooting

- **"No such container: n8n"** → The container was deleted (likely created with `--rm`). Re-run the command in step 2.2 — your data in `n8n-data` will still be loaded since it's saved on disk.
- **Page doesn't load at localhost:5678** → Make sure Docker Desktop is running and `docker ps` shows `n8n` as "Up".
- **Forgot your n8n login** → There's no easy reset for local setups; you may need to delete the contents of `n8n-data` and start fresh (this erases all workflows/credentials).
