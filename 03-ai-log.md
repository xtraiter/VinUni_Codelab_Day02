# 03 — AI Log & Reflection

> **Member:** Nguyễn Trường Thành (`thanhntda`)
> **AI tool used:** Claude
> **How I used it:** as a thought-partner to categorize and stress-test my problem ideas and to brainstorm pain points, not to decide for me.

---

## 1. Phase 1 — Problem scan & lenses

**What I asked:** whether I needed problems for *each* lens, and to categorize my own ideas.

**How AI helped:**
- Clarified: at least 5 problems **in total**, each tagged with one lens, covering **at least 3 different lenses**.
- Idea 1 (VinFast parts forecasting & supplier matching) → *Time-consuming*. It pointed out this is really **two problems** in one and that demand forecasting is usually classic ML/statistics, not an LLM.
- Reviewed my lens coverage after each idea and reminded me which lenses were still missing.

**My own decisions (not the AI's):**
- My first Idea 2 (Vinhomes FAQ ticket matching) felt **too basic**, so I replaced it with **urgency-based ticket prioritization** ("no water, can't cook" should come before "where does the moving truck park").
- AI added a risk I hadn't considered: the worst error is an urgent ticket marked low priority (e.g. a gas leak), so danger keywords should always go to the top and low-confidence tickets go to a human.

---

## 2. Using the worksheet's brainstorm prompt

**What I did:** tried the suggested prompt *"Tôi là AI Engineer tại Vin Smart Future… cho mảng [Chọn một…]"*.

**Problem 1 — placeholder:** I first sent it with the `[Chọn một: …]` placeholder still in it. I had to choose a company. I picked **Vinmec** and then **Xanh SM** to spread my ideas across companies.

**Problem 2 — hallucinated statistics (most important lesson):**
- The prompt asks for "con số thống kê ước tính về tổn thất". The AI produced numbers like "15–20 min per claim" and "5–10 min hotline wait".
- These sound confident but are **estimates, not real Vinmec / Xanh SM data**. I treat them as **assumptions to verify**, not facts.

**Other useful corrections:**
- For Xanh SM, it avoided dispatching problems because the worked example already covers that.
- It flagged that one Xanh SM idea (safety complaint triage) overlapped with my Vinhomes idea, so I chose **lost-item recovery** instead for variety.
- For my lost-item idea (car camera checks the back seat at drop-off), it raised a **privacy boundary**: camera use only at drop-off time, only the back seat, only to detect objects.

**What I kept:** Vinmec insurance claim checks (Repetitive), Vinmec appointment hotline (AI-upgrade), Xanh SM lost items (Repetitive). I rewrote them in my own words.

---

## 3. Reflection

**AI helped most with:** _TODO — in my own words_

**Where AI was wrong or risky:** _TODO — e.g. made-up statistics presented confidently_

**How I'll use AI differently next time:** _TODO — e.g. always fill placeholders, ask for sources, verify numbers_

---

## 4. Phase 2 — Quick Problem Cards

**How I used AI:** as a "pen". The card format is slow to get right, so the AI asked me questions for each card (actor, workflow, slowest step, where AI helps, metric, architecture, boundary). I answered in my own words, and the AI only formatted my answers into the card template. I chose the 3 ideas myself: VinFast parts forecasting, Vinmec insurance claim checks and the Vinmec appointment hotline.

**Where the AI's follow-up questions changed my cards:**
- **Card #1 (VinFast):** I wanted forecasting + supplier matching as one integrated system. The AI reminded me these are two problems in the same workflow, so I **focused on forecasting** only.
- **Card #1 architecture:** I wasn't sure what No AI / Rule / LLM / Agent meant. The AI explained that if the production plan is structured data, plain Rule code (plan × parts list − stock) is enough. I chose **LLM** because I assume plans arrive as messy text, and I wrote that down as an **assumption**, since VinFast may already use software for this and I can't verify it as a student.
- **Metrics:** several of my first answers had a direction but no number (e.g. "reduce the time"). The AI pushed me for **targets**, so I set them: 30–60 min → 10 min per production batch, peak 1 h → 10 min per claim, hotline wait 5–10 min → 10–15 s and hang-ups 10% → 1%. These numbers are my own estimates.
- **Card #3 (hotline):** I described the **future** flow (voicemail + AI summary) in the "current workflow" line. The AI pointed this out, and I confirmed the real current process (call → wait in queue → staff take details → book → confirm).

**My own decisions on boundaries:**
- **Card #2:** AI must never accept/reject a claim or even hint at it, because that would pass the AI's bias to staff. The AI then noticed that my own feature, "suggest similar past cases", could leak that bias (e.g. "5 similar claims were rejected"). I agreed and changed it: past cases show **only where the evidence was found, with outcomes hidden**.
- **Card #3:** AI must never add or leave out crucial information from a voice message; unclear recordings go to a human.

**Open question I noticed:** all 3 of my cards ended up as LLM. A strict reviewer could argue that parts of Card #1 are better done with Rule.

---

## 5. Later entries (Python prototype, team discussion)

_TODO — add notes here as I work on `prompt_prototype.py`._
