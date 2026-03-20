# 🤖 AI Resume Tailor

> **Automatically tailor your LaTeX resume and generate a personalized cover letter for any job — powered by AI and n8n automation.**

---

## ✨ What It Does

Paste a job URL or description into a form, and within seconds this workflow:

1. 📥 **Fetches the job description** — supports raw text or any public job URL
2. 🧠 **Tailors your LaTeX resume** — keeps your real info, template, and packages intact while matching keywords and skills to the job
3. ✉️ **Writes a personalized cover letter** — human-sounding, specific to the company and role
4. 🌐 **Displays results in the browser** — clean HTML page with both outputs ready to copy

No more spending hours rewriting your resume for every application. One click, two outputs, ready to send.

---

## 🖼️ Workflow Overview

```
Form Trigger
     │
     ▼
IF Node (URL or raw text?)
     │
     ├── URL ──► HTTP Request ──► Code (clean HTML) ──► Merge
     │                                                     │
     └── Raw Text ───────────────────────────────────────► Merge
                                                           │
                                                           ▼
                                                     Resume Store (Code Node)
                                                           │
                                                           ▼
                                                    Edit Fields (Set)
                                                           │
                                              ┌────────────┴────────────┐
                                              ▼                         ▼
                                     AI - Tailor Resume        AI - Cover Letter
                                     (Groq API)                (Groq API)
                                              │                         │
                                              ▼                         ▼
                                     Extract Resume             Extract Cover Letter
                                              │                         │
                                              └────────────┬────────────┘
                                                           ▼
                                                     HTML Output
                                                  (rendered in browser)
```

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Automation Platform | [n8n Cloud](https://n8n.io) |
| AI Model | [Groq API](https://console.groq.com) — Llama 3.3 70B Versatile |
| Resume Format | LaTeX |
| Scripting | JavaScript (n8n Code nodes) |
| Job Fetching | HTTP Request node + HTML cleaning |
| Output | HTML rendered in browser |

---

## 📁 Project Structure

```
ai-resume-tailor/
│
├── workflow.json          # n8n workflow — import this into your n8n instance
└── README.md              # This file
```

---

## 🚀 How to Use

### 1. Prerequisites
- An [n8n Cloud](https://cloud.n8n.io) account (or self-hosted n8n)
- A free [Groq API key](https://console.groq.com) — no credit card needed

### 2. Import the Workflow
1. Download `workflow.json` from this repository
2. Open your n8n instance
3. Click **"Import from file"** and select `workflow.json`

### 3. Add Your API Key
1. In the workflow, find the **`AI - Tailor Resume`** node
2. In the `Authorization` header, replace `YOUR_GROQ_API_KEY` with your real key
3. Do the same in the **`AI - Cover Letter`** node

### 4. Add Your Resume
1. Open the **`Resume Store`** Code node
2. Replace the LaTeX content inside `String.raw` with your own LaTeX resume
3. Keep the `String.raw` wrapper — it handles LaTeX backslashes correctly

### 5. Run It
1. Activate the workflow using the toggle in the top right
2. Open the **Production Form URL** from the Form Trigger node
3. Fill in:
   - **Job Input** — paste a job URL or raw job description
   - **Your Name** — your full name
4. Submit and wait ~5 seconds
5. Your tailored resume and cover letter appear in the browser ✅

---

## 💡 Key Technical Decisions

### Why `String.raw` for the Resume?
LaTeX uses backslashes (`\`) everywhere. JavaScript template literals interpret `\n`, `\t`, `\u` as escape sequences, which corrupts LaTeX code. `String.raw` treats all backslashes as literal characters — solving the corruption problem completely.

### Why Groq Instead of OpenAI or Gemini?
Groq offers a **genuinely free tier** with fast inference using Llama 3.3 70B. It also uses the OpenAI-compatible message format, making it easy to switch to any other provider by just changing the URL and model name.

### Why Two Separate AI Calls?
Each task needs a focused prompt. A resume tailoring prompt is very different from a cover letter prompt. Mixing them into one call degrades the quality of both outputs significantly.

### Why Bypass the Form for Resume Input?
n8n form textareas encode special characters differently, causing LaTeX backslashes to arrive as literal `\n` strings. Storing the resume directly in a Code node bypasses this entirely and gives the AI clean, uncorrupted LaTeX every time.

---

## ⚙️ Customization

### Change the AI Model
In both HTTP Request nodes, change the `model` field:
```
llama-3.3-70b-versatile     ← current (free, fast)
llama-3.1-8b-instant        ← faster, lighter
mixtral-8x7b-32768          ← alternative
```

### Switch to OpenAI
Change the URL to:
```
https://api.openai.com/v1/chat/completions
```
And set model to `gpt-4o`. Everything else stays the same.

### Switch to Claude (Anthropic)
Change the URL to:
```
https://api.anthropic.com/v1/messages
```
Add header `anthropic-version: 2023-06-01` and set model to `claude-sonnet-4-20250514`.

### Adjust AI Creativity
- `temperature: 0.7` → resume (precise, structured)
- `temperature: 0.8` → cover letter (more natural, human)
- Lower = more consistent, Higher = more creative

---

## 🔒 Security Notes

- ⚠️ **Never commit your API key** to a public repository
- n8n stores credentials separately from workflow JSON — your key is safe when exporting
- Double-check your HTTP Request nodes before pushing to GitHub to ensure no raw keys are visible

---

## 🗺️ Roadmap

- [ ] Email delivery via Gmail node
- [ ] PDF compilation from LaTeX automatically
- [ ] Google Docs output for cover letter
- [ ] Job fit score — AI rates match % before tailoring
- [ ] Google Sheets application tracker
- [ ] Multi-resume support — different resumes for different job types
- [ ] Automatic job board monitoring via RSS

---

## 🧠 What I Learned Building This

- How to call any AI API from n8n using HTTP Request nodes
- How to handle special characters (LaTeX backslashes) safely in JavaScript
- How to design branching workflows with IF and Merge nodes
- How to write effective system prompts for structured AI output
- How to debug n8n workflows node by node systematically
- The OpenAI message format (`system` / `user` / `assistant`) used by most AI APIs

---

## 📄 License

MIT — free to use, modify, and share.

---

*Built with n8n + Groq + a lot of debugging 🔧*
