# SHROUDED — macOS

A multiplayer social-deduction game in the style of *Among Us*, written from
scratch in C using SDL2. Up to **6 players** join the same game over a local
network. One player is secretly the **Killer**; everyone else is an innocent
**Crewmate**. Innocents race to finish their tasks or vote the Killer out — the
Killer tries to eliminate enough Innocents to take over.

This folder is the **macOS** version.

> **Read this first:** This build is for **Apple Silicon Macs** (M1, M2, M3, M4).
> It will **not** start on an older **Intel Mac**. To check: Apple menu () →
> **About This Mac** → look at the "Chip" line. If it starts with "Apple",
> you're good.

---

## What's in this folder

```
Shrouded-macOS/
├── client                 ← the game program for players
├── server                 ← the game program for the host
├── Start Client.command   ← double-click THIS to play  (recommended)
├── Start Server.command   ← double-click THIS to host   (recommended)
├── assets/                ← images, sounds, fonts (MUST stay in this folder)
└── README.md              ← this file
```

**Keep every file together in the same folder.** The game looks for `assets/`
right next to the program. If you move `client` or `server` out on their own, or
delete `assets`, the game will not work. You can move or copy the **whole
folder** anywhere, as long as the contents stay together.

---

## Which file do I open? (`client` vs `Start Client.command`)

There are two ways to launch each program — **use the `.command` files**,
`Start Client.command` and `Start Server.command`. Both run the exact same game;
the `.command` file is just a small wrapper that launches `client`/`server` for
you. Why prefer it:

- It always starts the game from the correct folder, so `assets/` is found no
  matter where you click from. (Opening the bare `client` directly can start it
  from the wrong folder on some Macs, so it can't find its images → black
  screen.)
- It keeps the window open at the end so you can read any messages or errors.
- The names make it obvious which file is for hosting and which is for playing.

The bare `client` / `server` files work too if they work for you, but the
`.command` files are the safe choice — especially when sharing the game.

---

## Setup — do this once on EVERY Mac that runs the game

Having the files on the computer is **not enough by itself**: the game's
libraries must be installed on each machine (Steps 1–2). Step 3 unblocks the
programs. You do all three **once** per Mac.

Everything here is typed into the **Terminal** app. Open it with **Cmd + Space**,
type `Terminal`, press Enter.

### Step 1 — Install Homebrew

Homebrew is a free "package manager" that installs software for you. If this Mac
already has it, skip to Step 2. Otherwise paste this and press Enter:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

It may ask for your Mac password — **the password stays invisible as you type,
that's normal.** At the end it may print a line or two to "add Homebrew to your
PATH"; if so, copy and run those too.

### Step 2 — Install the game's libraries (SDL)

Paste this and press Enter:

```bash
brew install sdl2 sdl2_image sdl2_mixer sdl2_net sdl2_ttf
```

This installs SDL **and every smaller library it depends on** (image, sound,
font). This is the macOS equivalent of the library files — that's why none are
bundled in this folder. Confirm it worked with:

```bash
brew list | grep sdl
```

You should see all five: `sdl2`, `sdl2_image`, `sdl2_mixer`, `sdl2_net`,
`sdl2_ttf`.

### Step 3 — Unblock the programs (Gatekeeper)

Because the programs aren't signed by a registered developer, macOS blocks them
the first time. This does **not** mean anything is wrong.

**Terminal way:** move Terminal into the folder — type `cd ` (with a space),
then **drag the game folder from Finder into the Terminal window** and press
Enter. Then run:

```bash
xattr -dr com.apple.quarantine .
chmod +x client server "Start Client.command" "Start Server.command"
```

The first line removes the "downloaded from the internet" block; the second
grants permission to run.

**No-Terminal way:** in Finder, **right-click** `Start Server.command` →
**Open** → **Open** again in the warning. Repeat for `Start Client.command`.
Once per file is enough.

---

## Requirements

- An **Apple Silicon Mac** with SDL installed (Steps 1–2 above).
- All players on the **same network** (same Wi-Fi or LAN).
- One Mac acts as the host by running the server.

---

## How to start a game

### Step 1 — Start the server (host only)

One person double-clicks **`Start Server.command`**. A Terminal window opens and
shows:

```
Server listening on port 2000...
```

**Leave this window open** for the whole session — closing it ends the game for
everyone. (The first time, macOS may ask "Do you want to allow incoming network
connections?" — click **Allow**.)

The host also needs their **local IP address** so others can connect — see the
next section.

### Step 2 — Join as a player (everyone, including the host)

1. Double-click **`Start Client.command`**.
2. On the main menu, choose to start.
3. When prompted for the server address, type the host's IP (e.g.
   `192.168.1.42`) and connect. On the **same Mac** as the server, use
   `127.0.0.1` instead.
4. You'll land in the **lobby**. Wait for everyone to join.

### Step 3 — Start the match

The **host** (the first player who joined) starts the match by pressing
**`SPACE`** in the lobby, once **at least 2 players** have joined. Everyone then
sees their secret role and the round begins.

---

## Finding the host's IP address

Only the **host** needs this. In a Terminal window, run:

```bash
ipconfig getifaddr en0
```

If that prints nothing, try `en1` (Wi-Fi is often `en0`, wired often `en1`, but
it varies). The result looks like `192.168.1.42` — share that with your players.

Tips:
- A home/office IP almost always starts with **`192.168.`** or **`10.`**.
- **Ignore** `127.0.0.1` — that only works on the host's own machine.
- The IP can change when you reconnect or restart, so check it again each
  session.
- Testing alone on one Mac? Players can just use **`127.0.0.1`**.

---

## How to play

### Your goal

- **Innocents** win by either completing **all of their tasks** or by **voting
  out the Killer** in a meeting.
- The **Killer** wins by eliminating Innocents until the Killer is **equal to or
  greater in number** than the remaining Innocents.

### Roles

At the start of each round you're shown your role:

- **Innocent (Crewmate)** — walk around the map and complete your assigned
  tasks. You can call meetings and report bodies, but you cannot kill.
- **Killer (Impostor)** — blend in, and eliminate Innocents when no one is
  looking. After each kill there is a **30-second cooldown** before you can kill
  again.

### Controls

| Key / Action | What it does |
|---|---|
| `W` `A` `S` `D` | Move your character |
| `E` | Interact — start a task on a task spot; open the emergency-meeting screen at the meeting table |
| `K` *(Killer only)* | Kill the nearest valid target in front of you (or click the kill button) |
| `R` | Report a dead body when standing near one |
| `M` | Open / close the task map |
| `TAB` | Show / hide the task list panel |
| `I` | Show / hide the controls overlay |
| `Q` | Cancel / leave a task you've opened |
| `ESC` | Open the pause menu |
| Mouse | Click buttons (kill, report, meeting, vote) |

### Tasks (Innocents)

Walk onto a task spot and press `E` to open the mini-game for that task. Finish
it to mark the task complete. When **all Innocents finish all their tasks**, the
Innocents win — so keep moving and getting them done.

### Meetings and voting

A meeting starts when someone **reports a body** (`R` near a corpse) or **calls
an emergency meeting** (stand on the meeting table, press `E`, then click the
button). During a meeting:

- Everyone gets a short time to discuss.
- You then have a **120-second** voting window.
- Click a player's banner to choose who you think the Killer is, then submit
  your vote. You can also choose to **skip**.
- The player with the most votes is eliminated. A tie (or skip winning) means
  no one is removed.

After the result is shown, survivors return to the map and play continues.

### Winning and playing again

When a round ends you'll see a win screen. From there choose **Play Again** for a
fresh round with new roles, or return to the main menu.

---

## Troubleshooting

**"dyld: Library not loaded: /opt/homebrew/opt/sdl2/..."**
SDL isn't installed on this Mac. Do Step 2:
`brew install sdl2 sdl2_image sdl2_mixer sdl2_net sdl2_ttf`.

**"client cannot be opened because the developer cannot be verified"**
Gatekeeper is blocking it. Do Step 3 (Terminal lines, or right-click → Open).

**Double-clicking does nothing, or it opens in a text editor**
Run the `chmod` line from Step 3. If it still opens in an editor, right-click →
**Open With** → **Terminal**.

**The window shows "[Process completed]" right away and the game doesn't appear**
That line just means the program finished. If it appears immediately, the
program exited instead of starting — read the lines **above** it. Usually it's a
"Library not loaded" message, meaning Step 2 hasn't been done on this Mac.

**Black screen or it closes instantly**
The `assets/` folder must be next to `client`/`server`, with its contents
intact. Don't rename or move it, and prefer the `Start Client.command` launcher.

**Can't connect to the server**
- Check the IP address is typed correctly.
- Make sure everyone is on the same network.
- Confirm the host's server window is still open and shows "Server listening".
- A firewall may be blocking it — when the server first runs, allow incoming
  connections if macOS asks. The game uses **ports 2000 and 2001**.

**Pressing SPACE doesn't start the game**
Only the **host** (first player to join) can start, and you need at least
**2 players** in the lobby.

**"Bad CPU type in executable" / nothing runs at all**
Almost certainly an Intel Mac. This build is for Apple Silicon and would need to
be rebuilt for Intel.

---

## A note on the network design

Shrouded uses a hybrid networking model: fast, frequent player movement is sent
over **UDP** (where an occasional dropped packet doesn't matter, since the next
update overwrites it), while critical, must-not-be-lost events — kills, votes,
meeting triggers, and phase changes — are sent over a reliable **TCP** channel.
The server is authoritative: it validates kills, vote eligibility, and body
reports rather than trusting the clients, which prevents basic cheating.

Have fun, and trust no one.
