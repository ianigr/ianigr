🇧🇷 [Português](README.pt-BR.md) · 🇦🇷 [Español](README.es-AR.md) · 🇺🇸 English

# Ian Rios — Product Manager & AI Builder

**Product Manager at Mercado Pago** · São Paulo, Brazil

I build AI-native products from zero: identify a real user problem, frame it as a testable hypothesis, ship an MVP using AI, and iterate based on data.

---

## How I work

Product decisions should be traceable — from user pain to hypothesis to decision to outcome. I don't build features; I run bets. And I track whether they pay off.

```
User pain (observed)
  → Problem framing (what's the actual job to be done?)
    → Hypothesis (if [X], then [metric Y moves], because [user insight Z])
      → MVP (minimum to validate the hypothesis, not the product)
        → Data (did it move? why or why not?)
          → Decision (scale, pivot, or kill)
```

I use AI throughout this cycle: for synthesis in discovery, for generating and stress-testing hypotheses, for accelerating development, and for analyzing data faster than any manual process. The goal isn't to use AI — it's to compress the time between user insight and validated decision.

---

## Personal projects

### nutrIAn — Nutritional tracking, zero friction

**The problem:** Most people who try to track nutrition quit within two weeks. Not because they lack willpower — because the friction is too high. Apps require account creation, manual entry, complex food databases, and consistent behavior change. The tool fights the user.

**Jobs to be done:** *"Help me feel in control of what I eat, with as little effort as possible."*

**Hypothesis:** If I meet users in WhatsApp — where they already spend hours daily — and reduce the logging action to a single photo or text message, retention will be significantly higher than nutrition apps that require behavior change on top of habit change.

**What was built:**
- WhatsApp bot that accepts photo or text description of any meal
- GPT-4o Vision analyzes the meal and returns calories + protein, carbs, fat in ~3 seconds
- **LLM-as-a-Judge quality layer:** a second LLM call evaluates each nutritional response before it's sent — checking for realistic portion sizes, plausible caloric ranges, and internal consistency. Acts as an automated quality gate that catches hallucinations before they reach the user
- Conversational goal-setting wizard (2-step caloric target setup)
- Daily history with progress bar vs. personal goal
- Weekly summary with dashboard link
- Retroactive logging (add meals from earlier in the day, by date)
- Correction flow: "actually it was grilled, not fried" — AI re-calculates and updates history
- Proactive engagement engine: messages sent at Brazilian meal time windows (breakfast, lunch, dinner), respects 21h–7h30 silence, max 1 proactive message/day, triggers after 12h of user inactivity
- All data stored per user in Supabase (PostgreSQL), no account creation required

**Key product decisions:**

*Channel over feature:* WhatsApp has ~99% penetration among Brazilian smartphone users. A better UX inside a new app doesn't beat a good-enough UX where the user already spends hours. Acquisition cost is zero; behavior change required is minimal.

*LLM-as-a-Judge for output quality:* Nutritional hallucinations (e.g., "500g of grilled chicken = 200 kcal") erode trust faster than any other failure mode. A second evaluation pass catches implausible outputs before they reach the user. This is the core reliability mechanism of the product — users stop using a nutrition tool the moment they stop trusting the numbers.

*Proactive > reactive engagement:* Tests showed that contextual reminders at meal time outperform expecting the user to log spontaneously. The right trigger is contextual (mealtime), not arbitrary (daily push notification at 9am). Engagement at the moment of decision — the meal — is what drives habit formation.

*Text parity with photo:* Not every meal is photographable (coffee at 7am in a rush, lunch at a client). Text analysis must be equally accurate — otherwise the channel advantage breaks exactly when the user most needs it.

*Zero account:* Registration friction (create password, confirm email, log in) kills conversion before the user sees any value. WhatsApp identity is the account — no separate auth needed.

**Metrics tracked:**
- **Retention:** D7 and D30 active users (primary success metric)
- **Engagement:** meals logged per active user per day (target: ≥2)
- **Quality:** LLM-as-a-Judge rejection rate (% of responses flagged for re-generation)
- **Proactive engine:** open rate of proactive messages; conversion from proactive message to meal log within 30 min
- **Correction rate:** % of meals corrected by user (proxy for analysis accuracy)

**Status:** MVP live since March 2026 · Recruiting closed beta group (10 users) · Validating D7 retention before scaling

`Python · WhatsApp Cloud API · GPT-4o Vision · Supabase · Railway (CI/CD)`

---

### EstudaAI — Active learning from passive content

**The problem:** Medical students have too much content and not enough time. The standard study method is still passive: read, highlight, repeat. The content exists — but it's not in a format that produces durable knowledge. Creating active study materials (flashcards, quizzes, structured summaries) from raw PDFs takes hours that could go to actual practice.

**Jobs to be done:** *"Help me feel prepared for my exam with minimum effort on my part."*

**Hypothesis:** If AI can automatically transform any PDF into active study materials — flashcards, quizzes, structured summaries — students will feel more prepared in less time, and the gap between "having content" and "being ready" collapses.

**What was built:**
- Web platform where students upload any PDF (textbook chapter, article, lecture notes)
- **RAG pipeline:** documents are chunked, embedded, and stored in a vector database. Generation of each output type (flashcards, quiz, summary) retrieves the most semantically relevant chunks for each topic — ensuring coverage across the full document and enabling more precise question targeting than naive full-context approaches
- Asynchronous processing worker — handles long documents without timeout or blocking
- Auto-generation of four output types per document:
  - Structured summary organized by topic
  - Flashcard sets (Q&A pairs) targeting key concepts per chunk
  - Multiple-choice quiz with answer key and explanations
  - Suggested study sequence ordered by topic complexity (derived from embedding similarity clustering)
- Decoupled architecture: upload → queue → worker processes → user notified when ready
- Worker on Cloudflare Workers — serverless, no cold start for async jobs

**Key product decisions:**

*RAG over full-context injection:* Passing an entire 200-page PDF into context is expensive, slow, and produces lower-quality outputs — the model attends to everything equally. RAG chunking + semantic retrieval ensures each flashcard and quiz question is grounded in the most relevant document section, not the average of the full document. It also enables cross-document queries in future versions (study across multiple uploaded materials).

*Asynchronous processing:* Medical PDFs can be hundreds of pages. Synchronous processing creates timeouts and broken experience (user stares at a loading screen). The worker decouples upload from delivery — user uploads, closes the tab, gets a notification when material is ready. Also allows parallel processing of multiple documents.

*Four output formats, not one:* Different study moments require different formats. Quick review before class → flashcard. Mock exam → multiple-choice quiz. Understanding context → summary. Study planning → complexity-ordered sequence. Offering all four eliminates the meta-work of choosing the right tool for the right moment.

*Medicine as entry segment:* High content volume, study methods unchanged for decades, high willingness to pay for time-saving tools, and motivated users with clear recurring need (exams). Clear pain, validated willingness to change behavior. The platform generalizes to any content-heavy field — medicine is the beachhead.

*Claude API for generation:* Long context window handles full chapters without chunking hacks at the generation stage. Quality of flashcard and quiz generation is significantly better than GPT-3.5-class models — and quality is the core of the product; a wrong flashcard is worse than no flashcard.

**Metrics tracked:**
- **Time-to-first-value:** time from upload to first output ready (target: <5 min for standard chapter)
- **Output quality:** user-reported accuracy rate on generated flashcards and quiz questions (survey after first session)
- **Learning outcomes:** quiz scores vs. pre-quiz self-assessed confidence (measures whether the material actually helps)
- **Engagement depth:** % of generated flashcards reviewed; % of quiz completed (not just opened)
- **Return rate:** D7 return after first upload (primary retention metric)
- **RAG coverage:** % of document sections represented in generated outputs (automated check against source chunks)

**Status:** In active development since February 2026

`Next.js · TypeScript · Supabase · Claude API · Cloudflare Workers`

---

### Flight Monitor — Be notified. Don't search.

**The problem:** Finding cheap flights requires daily manual checking across multiple sites. The right price appears in short windows — sometimes hours — and disappears. Monitoring creates zero value; it's pure mechanical repetition of a trivial condition check: *did the price drop below my threshold?*

**Jobs to be done:** *"Tell me when the right flight appears. Don't make me look for it."*

**Hypothesis:** If users can set a price threshold and receive an email when it's matched — with no account required and one-click cancellation — adoption will be significantly higher than alert features that force registration before showing value.

**What was built:**
- Web product where anyone defines: origin airports, destination airports, date range, min/max trip duration, and maximum price in BRL
- Daily automated price monitoring via GitHub Actions cron job
- Email alert with specific flight options, prices in BRL (with exchange rate + IOF applied), and direct booking links
- One-click cancellation via UUID token embedded in every email — no login, no account
- Multi-airport expansion: "São Paulo" automatically monitors GRU + CGH + VCP — users think in cities, not IATA codes
- Exchange rate applied automatically: daily USD→BRL rate via open API + 6.38% IOF — the threshold the user sets is the number they see on their credit card statement
- Optional account creation: users who want to manage multiple alerts get a dashboard to view, pause, and delete all their active alerts in one place — account is optional, not required to use the product
- Alert history table in Supabase with full audit trail of notifications sent

**Key product decisions:**

*No account = more conversions:* Registration friction kills adoption before the user sees value. Cancel tokens solve identity without auth — the user manages their alert from their inbox. Also eliminates support requests about forgotten passwords.

*Price in BRL, final cost:* Brazilian users think in reais. Converting USD manually adds cognitive friction and introduces inaccuracy (people underestimate IOF). The product applies the daily exchange rate and IOF automatically. The threshold is the number that appears on the credit card — no surprises.

*GitHub Actions over always-on server:* The monitor runs once daily. A 24/7 server for a once-daily job is pure operational waste. GitHub Actions handles it with a cron job — zero infrastructure cost, zero maintenance overhead.

*Multi-IATA per city:* Users don't think in airport codes. The product expands to all relevant IATAs automatically and reports per specific airport in the alert email — specificity in output, simplicity in input.

**Metrics tracked:**
- **Alert creation rate:** % of visitors who complete the alert form (conversion metric)
- **Alert-to-notification rate:** % of active alerts that have triggered at least one email (product effectiveness — are we actually finding flights?)
- **Email engagement:** open rate and click-through to booking page per alert email sent
- **Alert lifetime:** average days an alert stays active before cancellation (proxy for user satisfaction — if users cancel quickly, the alerts aren't useful)

**Status:** Live · 4 active users in testing · Validating alert-to-notification conversion rate

`Next.js · Supabase · Python · GitHub Actions · Vercel`

---

## How I use AI in product work

AI isn't only in the products I build — it's in how I work as a PM.

| Stage | How AI accelerates it |
|-------|-----------------------|
| **Discovery** | Synthesize qualitative research at scale, identify patterns across multiple interviews, generate competing explanations for observed user behavior |
| **Problem framing** | Generate alternative framings, stress-test assumptions, identify which hypotheses are most critical to validate first |
| **Hypothesis generation** | Draft explicit if/then/because hypotheses, identify the metric most sensitive to each, surface edge cases before they reach engineering |
| **Spec writing** | Draft PRDs with explicit success criteria and failure modes, generate edge cases, challenge requirements against actual user goals |
| **Data analysis** | Funnel interpretation, cohort analysis, anomaly detection, segmentation hypotheses, experiment design and statistical read-out |
| **Development** | Claude Code for MVP implementation — I ship prototypes and internal tools without waiting for engineering sprints |
| **Decision-making** | Structured reasoning with explicit assumptions, confidence levels, and reversibility assessment before escalating to stakeholders |

---

*São Paulo, Brasil*
