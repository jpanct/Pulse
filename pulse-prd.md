# Product Requirements Document
## Garmin Health Coach App — *Pulse*
**Version:** 1.0 | **Status:** Draft | **Date:** March 3, 2026

---

## 1. Overview

### 1.1 Problem Statement
Garmin collects rich health data — stress, sleep, HRV, steps, body battery, SpO2, and more — but surfaces it as raw metrics without telling users *what to do about it*. Users can see that their stress was high on Tuesday, but have no guidance on why or how to reduce it, especially given the realities of a typical workday. The data exists. The actionability doesn't.

### 1.2 Vision
**Pulse** is an AI-powered health companion that syncs directly with Garmin, presents your daily health data in a clean and digestible format, and turns passive metrics into a personalized daily action plan — for stress, sleep, weight loss, and fitness goals that matter to real people with real schedules.

### 1.3 Target Users
- Garmin device owners (any wearable with health tracking)
- Working adults (25–50) who are health-conscious but time-constrained
- People who want to improve specific outcomes (stress, sleep, weight, fitness) without overhauling their life
- Users who are overwhelmed by raw data and want clear, practical guidance

---

## 2. Goals & Success Metrics

| Goal | Metric | Target (6 months post-launch) |
|---|---|---|
| User engagement | DAU/MAU ratio | ≥ 55% |
| Insight utility | "Was this helpful?" rating | ≥ 80% positive |
| Garmin sync reliability | Successful sync rate | ≥ 99% |
| Goal progress | Users reporting improvement in primary goal | ≥ 60% at 90 days |
| Retention | 30-day retention | ≥ 45% |

---

## 3. User Goals & Use Cases

### Understanding the User
Goals are never explicitly selected. Instead, the app infers what matters to the user through a short conversational onboarding questionnaire — and continues refining its understanding through check-in data over time.

The questionnaire is presented as a casual conversation, one question at a time, not a form. Tone is warm and human, not medical.

**Onboarding Questions:**

| Question | What It Informs |
|---|---|
| "What does a typical workday look like for you?" (desk job, physical labor, on the go, variable) | Baseline activity expectations, stress context |
| "How would you describe your work environment — is it usually high pressure?" | Stress sensitivity, check-in trigger thresholds |
| "How has your sleep been lately — do you feel rested most mornings?" | Sleep priority, baseline sleep satisfaction |
| "Do you do anything currently to take care of your health? (exercise, diet, etc.)" | Existing habits to build around, not replace |
| "How do you usually wind down after work?" | Evening routine baseline, stress recovery patterns |
| "Do you drink alcohol regularly?" | Contextualizes check-in responses; adjusts alcohol question trigger thresholds |
| "Is there anything specific you've been wanting to improve about how you feel day to day?" | Open-ended — captures weight, energy, mood, fitness, mindfulness, flexibility, yoga, or any other personal goal in the user's own words |
| "Are there any practices you've been wanting to build into your routine but haven't been consistent with? (e.g. yoga, meditation, stretching, walks)" | Surfaces lifestyle habit goals beyond fitness metrics — used to generate routine suggestions and calendar blocks |

The last question is intentionally open-ended and freeform. If a user says *"I just want to feel less exhausted"* the app focuses on Body Battery and sleep. If they say *"I've been trying to lose weight"* it brings in calorie burn and step trends. The app maps their language to the relevant Garmin data silently — the user never has to pick from a list.

> Goals are never shown to the user as labels. The app just starts surfacing what's relevant to them.

---

## 4. Features & Requirements

### 4.1 Authentication & Garmin Sync

**FR-01 — Garmin OAuth Login**
- Users log in via Garmin Connect OAuth 2.0
- App requests read permissions for: stress, sleep, HRV, steps, calories, Body Battery, SpO2, heart rate, activity data, weight logs
- No Garmin credentials are stored by Pulse; tokens are stored securely

**FR-02 — Automatic Daily Sync**
- Background sync triggers every morning at a user-configured time (default: 6:00 AM)
- Manual refresh available via pull-to-refresh
- Sync status indicator visible on the dashboard ("Last synced: 7 min ago")
- Graceful fallback messaging if Garmin API is unavailable

**FR-03 — First-Time Setup**
- Users connect their Garmin account via OAuth before anything else
- Onboarding questionnaire follows immediately after — conversational, one question at a time, no progress bar or form UI
- Users set their typical wake time and work schedule (used for check-in timing and work-week awareness)
- Optional: age, sex, weight for more accurate Garmin metric benchmarking
- Permissions walkthrough explaining what Garmin data is pulled, what calendar access is used for, and what is never stored or shared
- Onboarding takes 2–3 minutes; all questions are skippable with a "I'd rather just see my data" option

---

### 4.2 Daily Dashboard

**FR-04 — Today's Health Summary Card**
A top-of-screen card summarizing the day's most important signal, e.g.:
> *"Your Body Battery is at 42 — lower than your usual Monday. Your sleep was disrupted twice last night. Today's focus: recovery."*

**FR-05 — Core Metrics Display**
Each metric is shown as a visual tile with a plain-English label, trend arrow, and color coding (good / moderate / needs attention):

| Metric | Source | Display |
|---|---|---|
| Stress Level | Garmin HRV-based | Score + time-of-day chart |
| Sleep Score | Garmin Sleep | Score, stages breakdown, duration |
| Body Battery | Garmin | Current charge + charge/drain chart |
| Resting Heart Rate | Garmin | bpm + 7-day trend |
| Steps | Garmin | Count + goal % |
| HRV Status | Garmin | Status label + baseline trend |
| SpO2 | Garmin (if supported) | % + nightly average |
| Calories Burned | Garmin | Active + resting |
| Active Minutes | Garmin | vs. weekly goal |
| Weight | Garmin Connect / manual | Current + trend chart |

**FR-06 — Weekly & Monthly Trends**
- Swipeable trend view for each metric (7-day and 30-day)
- Highlights best and worst days with contextual labels

---

### 4.3 Behavioral Check-In System

This is the core differentiator. Rather than telling users what they *should* do, the app asks short, contextual questions about what they *actually did* — then connects those behaviors directly to their Garmin data to explain the "why" behind their metrics.

**FR-07 — Evening Check-In (Pre-Sleep)**
Triggered via a gentle push notification ~60–90 min before the user's typical bedtime. Presented as a quick card-swipe or tap-to-answer flow — max 4 questions, takes under 60 seconds.

*Example questions (shown only when relevant to that night's data):*

| Question | Triggered When |
|---|---|
| "Did you look at your phone or TV within 30 min of getting into bed?" | Shown if previous night's sleep score was poor |
| "Was your bedroom noticeably loud or bright when you fell asleep?" | Shown if sleep onset was delayed |
| "Did you eat a heavy meal within 2 hours of bedtime?" | Shown if RHR was elevated at sleep start |
| "How would you rate your stress level today?" | Shown if daytime stress score was elevated |
| "Did you have caffeine after 2 PM?" | Shown if sleep latency or restlessness was high |
| "Did you feel anxious or have a lot on your mind at bedtime?" | Shown if HRV was low at sleep onset |
| "Did you have any alcohol tonight?" | Shown only if **both** daytime stress score was elevated **and** previous night's sleep score was poor |

Questions are phrased casually (not clinical). Users answer with simple taps: **Yes / No / Not sure**, or a 1–5 scale for mood/stress.

**FR-08 — Morning Check-In (Post-Wake)**
A lighter 1–2 question check-in after waking, surfaced on the dashboard when the user opens the app:

| Question | Purpose |
|---|---|
| "How do you feel this morning?" (1–5 scale) | Cross-reference with sleep score to calibrate subjective vs. objective rest |
| "Do you have a high-pressure day ahead?" | Flag for stress pattern tracking; primes the day's insights |

**FR-09 — Insight Cards Powered by Check-In Answers**
Each morning, 3–5 insight cards are generated by combining Garmin metrics *with* the previous night's check-in responses. The insight explains the likely cause — not just the outcome.

*Example Cards:*

> **😴 Sleep — Deep sleep was cut short last night**
> You got 6h 20min total, but only 48 min of deep sleep (your average is 1h 15min). You mentioned having a drink last night — alcohol is one of the most common suppressors of deep sleep, even in small amounts. Your body processes it during the night, pulling you out of deeper stages.

> **🧠 Stress — Your recovery overnight was low**
> Your HRV stayed below baseline all night. You flagged that your bedroom was loud when you fell asleep — environmental noise keeps your nervous system partially alert even when you're unconscious, which limits recovery.

> **📱 Screen time may have delayed your sleep**
> Your sleep onset took ~35 min (your usual is ~12 min). You mentioned using your phone right before bed — blue light and mental stimulation near bedtime are directly tied to longer sleep latency. This likely explains the difference.

> **⚖️ Weight — Up slightly, likely not fat**
> You're up 0.8 lbs over 7 days, but your calorie burn has been consistent and you've logged exercise 4x this week. You've also had elevated stress scores and reported 3 nights of disrupted sleep — cortisol from stress and poor sleep is a well-known driver of water retention and scale fluctuation.

The card never says "you should do X" — it explains what happened and why, in plain language. Users who want to act on it can tap **"What can I do about this?"** to expand optional suggestions.

**FR-10 — Stress Routine Builder (Triggered by Persistent Patterns)**
Once the app detects elevated stress for 3+ consecutive days *and* has at least 2 nights of check-in data confirming behavioral patterns, it shifts from purely explaining to offering a lightweight, personalized stress reduction plan.

This is never surfaced on Day 1 — it has to be earned through enough data to make the plan feel specific, not generic.

**Trigger conditions:**
- Average stress score ≥ 65 for 3+ days in a row
- At least 2 check-ins completed
- User has confirmed at least one behavioral contributor (e.g., anxious at bedtime, high-pressure days)

**What the app surfaces:**

> **📋 We've seen enough to suggest a routine**
> Your stress has been elevated for 5 days in a row. Based on your check-ins, high-pressure workdays are carrying into your evenings and delaying your sleep onset. Here's a simple routine built around your schedule — nothing drastic, just small anchors to help your nervous system shift gears between work and rest.

The plan is broken into three windows based on the user's work schedule:

| Window | Suggested Anchor | Why |
|---|---|---|
| **End of workday** | 5-min "shutdown ritual" — close tabs, write tomorrow's top 3 tasks | Signals to your brain that work is done; reduces ruminative thinking at bedtime |
| **Evening** | 10-min walk or low-effort movement within 2 hrs of finishing work | Clears residual cortisol without being stimulating |
| **Pre-bed (30 min out)** | Phone face-down, low lighting | Directly tied to your sleep onset pattern |

**FR-10a — Calendar Integration**
The app connects to the user's Google Calendar or Apple Calendar and uses it two ways:

1. **Read:** Scans for meeting-heavy days, back-to-back blocks, or late meetings — and pre-flags those evenings as high-risk stress nights. On those mornings, the dashboard surfaces a heads-up: *"You have a packed afternoon today. Your stress scores tend to be higher on days like this — your check-in tonight will matter more than usual."*

2. **Write (opt-in):** With user permission, the app can add recurring low-friction calendar blocks to protect the routine anchors:
   - *"Workday Shutdown"* — 5 min block at the end of the user's typical workday
   - *"Evening Reset"* — 10 min block 1–2 hrs after work
   - *"Wind-Down"* — 30 min block before the user's target bedtime
   
   These are added as private, low-priority events. The user can edit or remove them at any time. The app never reschedules or moves existing events.

**What the calendar write feature is NOT:**
- It does not create rigid schedules or block off large chunks of time
- It does not send reminders that feel like work tasks
- Blocks are intentionally short (5–10 min) so they don't feel like obligations

**FR-10b — Routine Check-In**
Once a routine is suggested, the evening check-in adds one lightweight question:

*"Did you get a chance to wind down before bed tonight?"* — Yes / Somewhat / No

Over time, the app correlates routine adherence with HRV and sleep onset data, and reflects that back: *"On nights you wound down intentionally, your sleep onset was 3x faster than on nights you didn't."*

**FR-10 — Work-Week Awareness**
- App is aware of the user's work schedule; any optional suggestions surfaced via the expand button are scoped to what's realistic (5-min actions during work, longer ones for evenings/weekends)
- Monday insights acknowledge common end-of-weekend disruption patterns
- Friday insights surface recovery opportunity signals for the weekend ahead

**FR-11 — Insight Personalization Over Time**
- The app tracks which check-in answers correlate most strongly with good vs. bad metric nights *for that specific user*
- After ~2 weeks, patterns are surfaced: e.g., *"On nights you have alcohol, your deep sleep drops an average of 31 min"* — shown as a personal pattern card, not a generic fact
- Users can rate each insight (helpful / not helpful) to tune card relevance over time

**FR-12 — Weekly Review**
Every Sunday evening, a "Weekly Wrap" is generated:
- Best and worst metric nights of the week + the check-in answers that corresponded to each
- Patterns detected that week (e.g., "Stress was highest on days you flagged no exercise")
- One focus question for the upcoming week (e.g., "This week, notice if caffeine after 2 PM affects your sleep onset")

---

### 4.4 Goal-Specific Features

**FR-15 — Lifestyle & Mindfulness Goal Hubs**
Beyond the core health metrics, users can pursue habit-based goals surfaced from their onboarding answers. Each hub provides a structured but flexible routine tied to their Garmin data.

**Yoga Hub**
- Suggested yoga routines based on Body Battery and HRV status — e.g., restorative flow on low-energy days, more active vinyasa when recovery is strong
- Session length options: 10 / 20 / 30 min so it fits any schedule
- Evening check-in adds: *"Did you do any yoga or stretching today?"* — shown if user flagged yoga as a goal
- Tracks consistency over time; celebrates streaks without being punitive about missed days
- On high-stress days flagged by the calendar, the dashboard surfaces a yoga prompt: *"Tough day ahead — a 10-min morning flow could help lower your baseline stress before it starts"*

**Meditation Hub**
- Guided breathing and meditation sessions (2–10 min) built into the app
- Recommended based on HRV and stress score: e.g., if HRV is low and stress was elevated yesterday, a morning meditation is surfaced as a priority
- Evening check-in adds: *"Did you take any intentional mental breaks today?"*

**Stretching & Mobility Hub**
- Short daily stretching routines (5–15 min) suggested on rest days or after high-activity days detected by Garmin
- Framed around recovery, not performance — especially relevant for desk workers with sedentary activity patterns detected via steps and active minutes

**General Habit Builder**
- Any practice a user mentions in onboarding (walks, journaling, cold showers, etc.) can be added as a trackable habit
- Evening check-in includes one rotating habit question per night — never more than one, so it doesn't feel like a survey
- All habits feed into the Weekly Review: consistency trends shown alongside Garmin metrics to surface correlations (e.g., *"On weeks you did yoga 3+ times, your average stress score was 12 points lower"*)
- Daily stress timeline (when peaks occurred)
- Pattern analysis built from check-in history: e.g., *"On days you flagged high-pressure mornings, your average stress score was 68 vs. 44 on other days"*
- Check-in questions specific to stress: "Did you take any real breaks today?", "Did you eat lunch away from your desk?", "Did you get outside at all?"
- Breathing exercise library (box breathing, 4-7-8, physiological sigh) accessible via the optional "What can I do about this?" expand — never pushed unsolicited

**FR-14 — Sleep Improvement Hub**
- Sleep score history + stage breakdown
- Pattern cards built from check-in data: correlations between the user's specific behaviors (alcohol, screens, noise, caffeine timing) and their personal sleep outcomes — not generic advice
- HRV trend to show whether sleep quality is improving over time as behaviors change
- Evening check-in notification at a user-set time — the check-in *is* the wind-down reminder, not a separate nag

**FR-13 — Weight Management Hub**
- Calorie burn trend (from Garmin) with optional manual food logging or integration with MyFitnessPal
- NEAT (Non-Exercise Activity Thermogenesis) coaching: small movement tips that accumulate across a workday
- Step goal progression: starts realistic, ramps up weekly
- Correlation view: show how sleep and stress affect weight fluctuation

**FR-14 — Fitness & Energy Hub**
- Body Battery optimization guide (when to train hard vs. recover)
- VO2 Max trend + plain-language explanation of what it means
- Readiness score (composite of HRV, Body Battery, RHR) with a clear train/recover recommendation
- Weekly activity summary vs. goal

---

### 4.5 Notifications & Nudges

**FR-15 — Smart Daily Briefing**
- Push notification each morning: "Your Pulse Briefing is ready — Body Battery: 67, Sleep: Fair"
- Tapping opens the daily dashboard

**FR-16 — Contextual Micro-Nudges** (opt-in)
- Midday check-in if morning stress was high: "It's noon — how are you feeling? Try a 2-min reset."
- Evening nudge based on Body Battery: "You're at 28% — wind down early tonight for a better tomorrow."
- Step nudge at 3 PM if user is below 40% of daily goal

**FR-17 — Notification Controls**
- Full control over which nudge types are on/off
- Quiet hours setting (default: 10 PM – 7 AM)
- "Focus Mode" toggle that pauses all non-critical notifications

---

### 4.6 Settings & Account

- Garmin account connection management (connect/disconnect/re-sync)
- Goal editing at any time
- Work schedule configuration (standard, shift work, remote, variable)
- Units preference (imperial / metric)
- Data deletion and account removal (GDPR/CCPA compliant)

---

## 5. Non-Functional Requirements

| Category | Requirement |
|---|---|
| **Performance** | Dashboard loads in < 2s after sync; insight generation < 3s |
| **Sync Reliability** | Garmin API retry logic with exponential backoff; user-visible sync status |
| **Privacy** | Health data encrypted at rest (AES-256) and in transit (TLS 1.3); no data sold to third parties |
| **Compliance** | HIPAA-aware data handling practices; GDPR/CCPA compliant |
| **Offline** | Last synced data and insights viewable offline |
| **Accessibility** | WCAG 2.1 AA — screen reader support, sufficient contrast, scalable text |
| **Platforms** | iOS 16+ and Android 10+ at launch |

---

## 6. Integration Requirements

| Integration | Purpose | Priority |
|---|---|---|
| **Garmin Connect API** | All health metrics sync | P0 |
| **Push Notifications** (APNs / FCM) | Daily briefings, nudges | P0 |
| **Google Calendar / Apple Calendar** | Read schedule to pre-flag high-stress days; write optional routine blocks | P1 |
| **MyFitnessPal API** | Optional food logging for weight goal | P2 |
| **Apple Health / Google Fit** | Read supplement data if Garmin is missing fields | P2 |

---

## 7. Out of Scope (v1.0)

- Medical diagnoses or clinical recommendations
- Social / community features
- Coaching via live chat or video
- Non-Garmin wearable support (Fitbit, Apple Watch, Whoop) — future versions
- Meal planning or nutrition database
- Integration with third-party therapy or mental health platforms

---

## 8. Risks & Mitigations

| Risk | Likelihood | Mitigation |
|---|---|---|
| Garmin API rate limits or deprecation | Medium | Cache data locally; monitor Garmin developer updates |
| Users feel insights are generic | High | Robust personalization loop; feedback rating on every card |
| Health data privacy concerns | Medium | Transparent data policy; no third-party data sharing by default |
| Low D2+ retention | Medium | Weekly Review feature; goal progress celebrations to reinforce habit |
| Insight fatigue | Medium | Cap at 5 cards/day; allow users to set preferred insight volume |

---

## 9. Open Questions

1. Should Pulse offer a **paid tier** with advanced AI coaching, or stay fully free and monetize via premium features? If premium, what's the paywall?
2. Should we support **manual data entry** for users whose Garmin device doesn't capture certain metrics (e.g., no SpO2)?
3. How should the app handle **Garmin API outages** — show stale data with a warning or block the dashboard?
4. Is **MyFitnessPal integration** a v1 requirement or a fast-follow, given its complexity?
5. Should the **Weekly Review** be generative (AI-written summary) or template-based for v1?

---

## 10. Appendix — Garmin API Data Points Referenced

Garmin Connect IQ & Health API surfaces the following data used in this product:
- Daily stress levels (0–100 score, time-of-day breakdown)
- Sleep stages (light, deep, REM, awake) + sleep score
- Heart Rate Variability (HRV) status and nightly average
- Body Battery (energy reserve 0–100)
- Resting Heart Rate
- Step count + active minutes
- Calories (active + resting)
- SpO2 (blood oxygen, device-dependent)
- VO2 Max estimate
- Weight (if logged via Garmin Index scale or manually)
- Respiration rate
