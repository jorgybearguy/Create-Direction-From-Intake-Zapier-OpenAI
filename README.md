# Create-Direction-From-Intake-Zapier-OpenAI
Booking → Intake → AI service brief automation (Zapier + OpenAI) with T-60 delivery, fallback, and dead-letter logging.

# Mock-Interview Direction Generator (Zapier + OpenAI)

**TL;DR**  
When a client books, they receive a pre-filled intake form (hidden fields carry booking metadata). On submission, an LLM compiles a clean, custom-tailored brief that’s delivered to the service provider **~1 hour before the session** (or immediately if the intake arrives late). There’s a failure path that alerts admin, sends the service provider the raw intake as a fallback, and logs to a dead-letter sheet.

**Demo (90s Loom):** [LOOM_LINK]  

**Prompt & Samples**

[System prompt (template)](prompt/system_prompt.template.txt)
[System prompt (filled sample)](prompt/system_prompt.sample_filled.txt)
[Service brief (sample AI output)](samples/openAI_output.sample.txt)

**Screenshots**

[Stage 1 screenshot](exports/stage1.png)
[Stage 2 screenshot](exports/stage2.png)

---

## Problem
Intermediaries were spending ~1 hour per session prepping service direction from scratch (chasing details, writing notes). We needed a repeatable, low-touch way to collect context from the client and deliver a standardized brief to the service provider, without manual ops.

## Solution — two simple automations

**Stage 1 — Intake request**  
Trigger on new booking (SimplyBook). Filter for the relevant service-type and email the client a **pre-filled intake** link (hidden fields carry booking metadata).

**Stage 2 — Direction generation & delivery**  
Trigger on intake submission (Google Sheets). Generate the actor brief (OpenAI), write it back to the sheet, and either **send now** (if < 1 hr to start) or **delay** so it lands **T-60m**. If the AI step fails, email **admin**, email the **actor the raw intake** as a fallback, and append to a **Dead_Letter** sheet.

---

## Flow diagram

![Flow diagram](readme_assets/flow.png)

