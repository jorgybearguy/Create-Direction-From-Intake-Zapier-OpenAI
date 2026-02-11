# Create-Direction-From-Intake-Zapier-OpenAI
Booking → Intake → AI service brief automation (Zapier + OpenAI) with T-60 delivery, fallback, and dead-letter logging.

# Mock-Interview Direction Generator (Zapier + OpenAI)

**TL;DR**  
When a client books, they receive an intake form, pre-filled with their (hidden) booking info. On submission, an LLM compiles a clean, custom-tailored brief that’s delivered to the service provider **~1 hour before the session** (or immediately if the intake arrives late). There’s a failure path that alerts admin, sends the service provider the raw intake as a fallback, and logs to a dead-letter sheet.

**Demo (90s Loom):** [LOOM_LINK]  
**Strict prompt template:** [prompt/system_prompt.template.txt] 

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

```mermaid
flowchart TD
  subgraph Stage_1["Stage 1 – Intake Request"]
    A[SimplyBook: New Booking] --> B{Service name contains "Interview"?}
    B -- No --> X[Stop]
    B -- Yes --> C[Email client pre-filled intake link]
  end

  subgraph Stage_2["Stage 2 – Direction & Delivery"]
    D[Google Sheets: Intake submitted (new row)] --> E[OpenAI: Generate actor direction]
    E --> F{AI output exists?}
    F -- Yes --> G[Write direction to sheet]
    G --> H[Compute session time minus 60m]
    H --> I{More than 60m left?}
    I -- No (within 60m) --> J[Send now to actor]
    I -- Yes (>60m) --> K[Delay until T-60m] --> L[Send to actor]

    F -- No (empty/failed) --> M[Email admin: generation failed]
    M --> N[Email actor: raw intake fallback]
    N --> O[Append to Dead_Letter sheet]
  end

