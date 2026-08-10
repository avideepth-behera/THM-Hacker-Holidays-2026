# ☀️ TryHackMe — Hacker Holiday 2026

![TryHackMe](https://img.shields.io/badge/TryHackMe-Hacker%20Holidays%202026-orange?style=for-the-badge\&logo=tryhackme)
![Cybersecurity](https://img.shields.io/badge/Cybersecurity-black?style=for-the-badge)

My personal **TryHackMe Hacker Holiday 2026** writeups, documenting the challenges, techniques, tools, and lessons learned throughout the event.

> 🎯 **Goal:** Learn, practice, document, and improve practical cybersecurity skills through CTF-style challenges.

---

## 📅 Event

**TryHackMe Hacker Holiday 2026**

A seasonal TryHackMe event featuring a collection of cybersecurity challenges covering areas such as:

* 🌐 Web Application Security
* 🔐 Authentication & Authorization
* 💉 Injection
* 🕵️ Reconnaissance & Enumeration
* 🐧 Linux
* 🔑 Cryptography
* 📦 API Security
* 🔍 Digital Forensics
* ⚙️ Privilege Escalation
* 🧩 CTF Challenges Problem Solving

---

## 📚 Writeups

| #  | Room / Challenge             | Category  | Level      | Writeup                                                                    |
| -- | ---------------------------- | --------- | ---------- | -------------------------------------------------------------------------- |
| 00 | The Brochure                 | OSINT     | ✅ Easy    | [Read](./0-The-Brochure.md)                                                |
| 01 | The Concierge Knows Too Much | AI        | ✅ Easy    | [Read](./1-The-Concierge-Knows-Too-Much/1-The-Concierge-Knows-Too-Much.md) |
| 02 | Room 404                     | Web       | ✅ Easy    | [Read](./2-Room-404/2-Room-404.md)                                         |
| 03 | Complimentary                | Cloud     | ✅ Easy    | [Read](./3-Complimentary/3-Complimentary.md)                               |
| 04 | Packed Light                 | Forensics | ✅ Easy    | [Read](./4-Packed-Light/4-Packed-Light.md)                                 |
| 05 | Beach Bar                    | Boot2Root | ✅ Easy    | [Read](./5-Beach-Bar/5-Beach-Bar.md)                                       |
| 06 | Overheard at Breakfast       | OSINT     | ✅ Easy    | [Read](./6-Overheard-at-Breakfast/6-Overheard-at-Breakfast.md)             |
| 07 | Do Not Disturb               | Boot2Root | ✅ Medium  | [Read](./7-Do-Not-Disturb/7-Do-Not-Disturb.md)                             |
| 08 | Towel on the Sunbed          | Web       | ✅ Medium  | [Read](./8-Towel-on-the-Sunbed/8-Towel-on-the-Sunbed.md)                   |
| 09 | CryptoCabana                 | Cloud     | ✅ Medium  | [Read](./9-CryptoCabana/9-CryptoCabana.md)                                 |
| 10 | The Hollow Shell             | Web       | ✅ Medium  | [Read](./10-The-Hollow-Shell/10-The-Hollow-Shell.md)                       |
| 11 | Infinity Pool                | Boot2Root | ✅ Medium  | [Read](./11-Infinity-Pool/11-Infinity-Pool.md)                             |
| 12 | After Hours                  | Forensics | ✅ Medium  | [Read](./12-After-Hours/12-After-Hours.md)                                 |
| 13 | The Guestbook                | AI        | ✅ Medium  | [Read](./13-The-Guestbook/13-The-Guestbook.md)                             |
| 14 | Management Wants a Word      | Forensics | ✅ Hard    | [Read](./14-Management-Wants-a-Word/14-Management-Wants-a-Word.md)         |

---

## 🛠️ Tools Used

Some of the tools and technologies used throughout the challenges:

### 🔎 Recon & Enumeration

* `nmap`
* `ffuf`
* `gobuster`
* `curl`
* `wget`
* `whatweb`

### 🌐 Web Security

* Burp Suite
* Browser Developer Tools
* Burp Repeater
* Burp Intruder
* JavaScript analysis
* HTTP request manipulation

### 🐧 Linux

* Bash
* `grep`
* `find`
* `awk`
* `sed`
* `curl`
* `nc`
* `ssh`

### ☁️ Cloud

* AWS CLI
* Azure Cloud Shell

### 🔐 Other

* CyberChef
* JWT analysis
* Base64 decoding
* Python scripting
* Hash analysis

---

## 🧠 What I'm Learning

The purpose of these writeups isn't just to record flags.

For every challenge, I try to document:

```text
Recon
  ↓
Enumeration
  ↓
Identify Attack Surface
  ↓
Analyze Application / Service
  ↓
Find Vulnerability
  ↓
Exploit
  ↓
Capture Flag
  ↓
Understand Why It Worked
```

The focus is on understanding the **underlying vulnerability and methodology**, rather than simply obtaining the flag.

---

## 📂 Repository Structure

```text
Hacker-Holiday-2026/
│
├── README.md
│
├── 00- The Brochure
│
├── 01- The Concierge Knows Too Much
│
├── 02-
│
├── 03-
│
├── 04-
│
├── 05-
│
├── 06-
│
├── 07-
│
├── 08-
│
├── 09-
│
├── 10-
│
├── 11-
│
├── 12-
│
├── 13-
│
└── 14-
```

Each challenge contains its own detailed writeup and supporting screenshots where useful.

---

## 📝 Writeup Format

Each writeup generally follows this structure:

```markdown
# Challenge Name

## 🎯 Objective

What needs to be achieved.

## 🔎 Reconnaissance

Initial enumeration and information gathering.

## 🧭 Enumeration

Interesting endpoints, services, parameters, files, etc.

## 🔬 Analysis

Understanding the application and identifying the vulnerability.

## 💥 Exploitation

Step-by-step exploitation process.

## 🚩 Flag

The captured flag / final objective.

## 🧠 What I Learned

Key concepts and lessons from the challenge.

## 🛡️ Mitigation

How the vulnerability could be prevented.
```

---

## ⚠️ Disclaimer

These writeups are created for **educational purposes and authorized cybersecurity training**.

All testing was performed against TryHackMe machines and environments intended for security practice.

> Do not use the techniques documented here against systems without explicit authorization.

---

## 🎓 Learning Goals

Through Hacker Holiday 2026, I'm focusing on improving my practical skills in:

* Web penetration testing
* API security
* Authentication & authorization testing
* Vulnerability identification
* Linux privilege escalation
* Reconnaissance and enumeration
* Burp Suite
* CTF methodology
* Manual exploitation
* Security research and documentation

---

## ⭐ About This Repository

This repository serves as both a **Hacker Holiday 2026 writeup collection** and a personal learning log.

The goal is to look back later and see how my methodology, understanding, and problem-solving skills have improved over time.

**Learn → Break → Understand → Document → Improve.**

---

<p align="center">
  <b>🏖️ Happy Hacking! 🏖️</b>
  <br>
  <sub>TryHackMe Hacker Holiday 2026</sub>
</p>

