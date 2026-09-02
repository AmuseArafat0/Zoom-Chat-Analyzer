# Zoom Chat Analyzer

Ask plain-language questions of a Zoom chat and get back the people, the messages and a CSV.

- **who asked for refund**
- **who has LMS problem**
- **who is unable to join the whatsapp group**
- **positive feedback**, **complaints**, **who wants the recording**, **"chatgpt plus"**, **payment after 10:30**

Everything runs in the browser. No server, no upload, no build step. One `index.html`.

## Live app

Publish this repo on GitHub Pages (steps below) and open `https://<your-username>.github.io/<repo-name>/`.

## What it reads

| Source | How to get it | Notes |
|---|---|---|
| Zoom saved chat (`.txt`) | In Zoom, chat panel, three dots, **Save chat**; or enable auto-save in Zoom settings | All three Zoom formats are handled: the current `10:02:14<tab>Name:<tab>message` format, the 2021-2023 `From Name To Everyone:` multi-line format, and the old `From Name to Everyone : message` format |
| CSV / TSV | Any export with a name column and a message column (time and "to" optional) | Columns are auto-detected. If it isn't sure, a column picker opens. Zoom webinar Q&A reports work too |
| Chat pasted into Google Sheets / Excel | The two-column shape where `19:45:55 From Name to Hosts and panelists:` sits on one row and the message on the next | Recognised automatically and read exactly like the `.txt` |
| Excel (`.xlsx`) | Same as CSV | Each sheet becomes a source |
| Paste | Copy the chat text into the paste box | Handy for a quick check mid-session |

Load as many files as you want. Each one becomes a source you can switch on or off. If you load the same chat twice (say the `.txt` and a CSV copy of it), the second one is detected and starts switched off so nobody is counted twice.

Webinar chats work too. In a webinar attendees can only write "to Hosts and panelists", so anyone who writes "to Everyone" or replies "to a name, Hosts and panelists" is on the host side; those senders are detected and hidden by the "Hide hosts" filter. A 22,000-message chat loads and answers in about a second.

## How asking works

The search box understands three things, and you can mix them:

1. **Topics** in your words. "refund", "money back", "LMS", "portal", "whatsapp group", "recording", "certificate", "payment", "login", "audio", "screen", "support", "timing", "assignment", "positive feedback", "complaints", "phone number", "email", "link" and so on. The line under the box shows how your question was read; click the × on a topic to drop it.
2. **Exact text** in quotes: `"chatgpt plus"`, `"module 3"`. Combine with a topic to narrow it: `refund "28 aug"` means refund messages that also contain 28 aug.
3. **Filters**
   - `from: priya` — only that person
   - `session: batch 36` — only sources whose file name contains that text
   - `private` / `dm` — only direct messages to the host
   - `after 10:30`, `before 11`, `between 10 and 10:15`
   - `everything` / `show all` — every message (useful with a filter, e.g. `dm from: karan`)

Anything with no known topic is treated as a keyword search, so `sept` finds every message containing "sept".

**Problems only.** If your question contains words like problem, issue, unable, can't, not working, not added, missing, full, expired or error, results are narrowed to messages that actually describe trouble. "who is unable to join the whatsapp group" returns "not part of community", "group is full", "only admin can add", "unable to join community", but not "will the recording be in the community group?". Plain "whatsapp group" returns every mention. If nothing describes a problem, every mention is shown and the line under the search box says so.

Results come in two views:

- **By person** — one row per participant with all their matching messages, first time seen and topics. This is the "who" answer.
- **By message** — one row per message in time order.

Then **Copy names**, **Copy list** (names with their messages, ready to paste into WhatsApp or a ticket) or **Download CSV**.

The left panel shows a topic breakdown of the whole chat. Click any topic to run it.

## Filters worth setting once

- **Hide these people** — put your host, trainer and support names here so they never show up as "asked for refund". Saved in your browser.
- **Hide names tagged Host / Co-host / Panelist** — on by default.
- **Hide direct / private messages** — off by default; private messages are usually where refund requests live.
- **Hide reactions and deleted messages** — on by default.

## Customising the topic dictionary

Open **Settings**. Each topic is JSON like this:

```json
{
  "id": "refund",
  "label": "Refund request",
  "color": "#B42318",
  "ask":   ["refund", "money back", "paisa wapas"],
  "match": ["refun", "money back", "paisa wapas", "/cancel my (course|enrol)/"]
}
```

- `ask` — words you type in the search box that select this topic.
- `match` — what to look for inside messages. Plain words match from the start of a word (`refun` catches refund, refunds, refunded). Wrap a pattern in slashes for a regex, e.g. `/\btest\b/`.
- Add Hinglish spellings people actually use. Add new topics for your own programme (batch names, tool names, trainer names).

Saved in your browser. **Export JSON** to share a dictionary with your team; paste it into Settings on another machine.

## Publish on GitHub Pages

1. Create a new repository, e.g. `zoom-chat-analyzer`.
2. Upload `index.html`, `README.md`, `LICENSE` and the `samples/` folder to the repository root. The file must be named `index.html` at the root or Pages returns a 404.
3. Repository **Settings → Pages → Build and deployment**: Source **Deploy from a branch**, Branch **main**, Folder **/ (root)**. Save.
4. Wait a minute, then open `https://<your-username>.github.io/zoom-chat-analyzer/`.

To update the tool later, just replace `index.html`.

## Privacy

The page never sends chat data anywhere. Files are read with the browser's File API and stay in memory. The only network requests are Google Fonts and, only when you open an `.xlsx`, the SheetJS library from cdnjs. Your settings (hidden names, dictionary) are stored in the browser's local storage on your own machine.

## Repository layout

```
index.html   the whole app
README.md
LICENSE
samples/     example chats in every supported format, for testing
```

## Licence

MIT.
