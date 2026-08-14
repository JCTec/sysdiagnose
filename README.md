# How to Check Your iPhone or iPad for Problems

### Using Apple’s `sysdiagnose` + AI

A **sysdiagnose** is Apple’s official diagnostic dump. It is a snapshot of processes, network config, crashes, configuration profiles, Wi-Fi, and more.

It is **not** a full forensic image. It is the best free, on-device snapshot you can get.

This guide walks you through three things:

1. **Generate** the sysdiagnose on the iPhone or iPad
2. **Export** it to a computer and unpack it
3. **Analyze** it with [Grok](https://grok.com) (or any strong LLM) using a careful forensic prompt

> Treat the AI report as a strong second opinion, not absolute proof.

---

## Contents

- [What a sysdiagnose can (and cannot) tell you](#what-a-sysdiagnose-can-and-cannot-tell-you)
- [1. Generate the sysdiagnose](#1-generate-the-sysdiagnose)
  - [Method A — Physical buttons](#method-a--physical-buttons-fastest)
  - [Method B — AssistiveTouch](#method-b--assistivetouch-recommended-if-buttons-are-annoying)
- [2. Find and export the file](#2-find-and-export-the-file)
- [3. Unpack it on your computer](#3-unpack-it-on-your-computer)
- [4. Analyze it with Grok](#4-analyze-it-with-grok-or-any-strong-llm)
- [What the prompt is careful about](#what-the-prompt-is-careful-about)
- [Tips for better results](#tips-for-better-results)
- [Privacy](#privacy)
- [The prompt](#the-prompt)

---

## What a sysdiagnose can (and cannot) tell you

| Usually **yes** | Usually **no** |
| --- | --- |
| Crashes, battery, everyday bugs | A brand-new self-deleting implant |
| Jailbreak / broken OS integrity | “This phone can never have been hacked” |
| MDM or sneaky configuration profiles | A full disk image or backup |
| Unexpected VPN, odd processes, network health | Absolute proof of innocence or guilt |

Empty profiles + a sealed OS + normal processes is a strong **“no obvious infection.”**  
It is **not** “impossible to have been targeted.”

---

## 1. Generate the sysdiagnose

You have two methods. Use whichever is easier.

If you can, **reproduce the weird behavior first**, then capture the dump.

Wait the full **5–10 minutes** after you trigger it. Do not unplug, reboot, or start another capture early.

### Method A — Physical buttons (fastest)

1. Reproduce the weird behavior if you can (or just do it now).
2. Press and hold **Volume Up + Volume Down + Side button** at the same time  
   (use the **Top** button on older iPads).
3. Hold for about **0.5 to 1.5 seconds**, then release.
4. On iPhone you should feel a short vibration. A screenshot is often taken as well.
5. Wait **5–10 minutes**. You may see a banner saying diagnostics are running.

> **Tip:** Do not hold too long or you will trigger the power-off / SOS screen.

### Method B — AssistiveTouch (recommended if buttons are annoying)

1. Go to **Settings → Accessibility → Touch → AssistiveTouch** and turn it **On**.
2. Tap **Customize Top Level Menu**.
3. Add a new item and choose **Analytics**.
4. *(Optional)* Go back and set **Single-Tap** or **Double-Tap** to **Analytics**.
5. Close Settings.
6. Tap the floating AssistiveTouch button → choose **Analytics**.
7. You will see **“Gathering analytics…”**, then later **“Successfully completed gathering analytics.”**

Wait the full **5–10 minutes**.

---

## 2. Find and export the file

1. Go to **Settings → Privacy & Security → Analytics & Improvements → Analytics Data**.
2. Scroll down (or use search) until you find files that start with `sysdiagnose_`.
3. Choose the **most recent** one (the name includes the current date and time).
4. Tap it → tap the **Share** button (square with an arrow).

**Best ways to get it off the device**

| Option | When to use it |
| --- | --- |
| **AirDrop to your Mac** | Fastest if you have a Mac nearby |
| **Save to Files** | Then move it later by cable or cloud |
| **Send it to yourself** | Mail, Messages, or another share target if needed |

The file is a large `.tar.gz` — often **300–800 MB+**.

---

## 3. Unpack it on your computer

On a Mac or Linux:

```bash
tar -xzf sysdiagnose_YYYY.MM.DD_....tar.gz
```

You will get a folder that contains things like:

| Path | What it is |
| --- | --- |
| `sysdiagnose.log` | Capture metadata |
| `ps.txt` | Process snapshot |
| `logs/` | System, network, profiles, battery, … |
| `WiFi/` | Wi-Fi status, reachability, diagnostics |
| `crashes_and_spins/` | Crashes, jetsam, analytics reports |
| `errors/` / `summaries/` | Collector gaps and indexes |

That folder is what we analyze.

---

## 4. Analyze it with Grok (or any strong LLM)

Use this exact workflow:

1. Open a **new chat** with [Grok](https://grok.com) (or another strong model that can read your filesystem).
2. Paste the **entire** prompt from [`PROMPT-FOR-OTHER-LLM.md`](PROMPT-FOR-OTHER-LLM.md).
3. The model will greet you and ask only three questions:

   | # | Question | What to answer |
   | --- | --- | --- |
   | 1 | **Where is the dump?** | Full path to the **extracted** folder |
   | 2 | **What should lead the report?** | **Bugs** / **Security** / **Network** |
   | 3 | **Are we on the same Wi-Fi right now?** | Yes or no |

4. Answer those three questions.
5. The model will read the files and produce a calm, structured report.

When it finishes, it writes two files **inside the sysdiagnose folder**:

| File | What it is |
| --- | --- |
| `FINDINGS.md` | The report |
| `HOW-THE-ANALYSIS-WAS-DONE.md` | What was opened, and why |

---

## What the prompt is careful about

The prompt in this repo is written so the model:

- **Does not invent threats**
- Treats empty profiles + sealed OS + normal processes as **“no obvious infection”** — not “impossible to have been targeted”
- Treats analytics files carefully and **does not call them crashes**
- Focuses on real crashes, jailbreak indicators, MDM / profiles, unexpected VPN, and network health
- Does not paste secrets (serials, MACs, tokens, VPN auth) into the report

---

## Tips for better results

- Prefer analyzing on the **same computer / same Wi-Fi** if you can. The prompt can then cross-check DNS and reachability.
- If the dump is huge, still give the path. A model with filesystem access should only open what it needs.
- Always treat the AI report as a **strong second opinion**, not absolute proof.
- For maximum privacy, run the same prompt with a **local** model (Ollama + Llama 3.1 / Qwen / etc.).

---

## Privacy

A sysdiagnose can contain network names, crash snippets, installed-app inventory, and other device details.

- Keep the `.tar.gz` and the unpacked folder on a machine you trust.
- Do not publish the dump.
- If you use a cloud model, you are sending whatever that model reads to that service. A local model never leaves your computer.

---

## The prompt

Copy everything below the first line in:

**[PROMPT-FOR-OTHER-LLM.md](PROMPT-FOR-OTHER-LLM.md)**

Give the model filesystem access (or a path it can open). Do **not** hardcode a folder. The model must greet first and ask before it opens files.

---

## License

The guide and prompt are released under the [MIT License](LICENSE).
