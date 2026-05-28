# Alliance Technical Group - PoC Plan

## Overview

**Customer:** Alliance Technical Group (ATG)  
**Primary use case:** Salesforce-connected workspace enabling natural-language queries over opportunity data - from basic revenue reporting through operational pipeline analytics to a "Bookings to Billings" model foundation.  
**Platform:** DevRev Computer with Salesforce integration

---

## Success Criteria

PoC is considered successful when the following quantitative thresholds are met across all use cases.

| Metric | Target | How we measure |
|--------|--------|----------------|
| **Data accuracy** | ≥ 95% match vs. SF native report | Run each PoC question in Computer AND pull the same report from SF; compare totals, averages, and rank orders |
| **Query coverage** | ≥ 80% of PoC questions answerable without workaround | Out of all listed questions below, at least 80% return a valid, accurate answer on first or second attempt |
| **Response time** | ≤ 30 seconds per question | Measured from prompt submission to full answer rendered |
| **First-attempt accuracy** | ≥ 70% of questions answered correctly on first try | No follow-up prompt or rephrasing needed to get the right answer |
| **BU bucketing accuracy** | 100% correct grouping | Every opportunity assigned to the correct BU and sub-service-line bucket vs. ATG-provided mapping |

---

## Use cases

### Use case 1: Revenue validation & reporting

**Goal:** Prove Computer can answer closed-won revenue questions accurately against SF data.

| # | Question | Quantitative success criteria |
|---|----------|-------------------------------|
| 1.1 | Total closed-won revenue by service line for Q1 2026 | Dollar totals per service line within ±1% of SF report |
| 1.2 | Top 10 accounts by closed-won dollar volume in Q1 2026 | 10/10 correct accounts in correct rank order; amounts within ±1% |

**Dependencies:**
- Salesforce sync active with opportunity object imported
- Fields available: Amount (or TCV/ACV), Stage, Close Date, Service Line, Account Name
- At least one closed-won opportunity exists in the date range

---

### Use case 2: Operational & pipeline analytics

**Goal:** Demonstrate Computer can perform multi-dimensional pipeline analysis without pre-built dashboards.

| # | Question | Quantitative success criteria |
|---|----------|-------------------------------|
| 2.1 | Average deal size by service line, trended QoQ (last 5 quarters) | Average per service line per quarter within ±2% of SF calculation |
| 2.2 | YTD win rate by service line (closed-won / total closed) | Win rate percentage within ±1 percentage point of SF report |
| 2.3 | Average days from creation to close (Q4 2025 deals) | Average within ±1 day of manual SF calculation |
| 2.4 | Average time in "Initiate Contact" and "Assess" stages by service line | Duration within ±2 days of SF stage history report (if available) |

**Dependencies:**
- All Use Case 1 dependencies, plus:
- Opportunity Created Date field synced
- Stage values include "Initiate Contact" and "Assess" (confirm exact naming)
- **Stage history / duration data synced** (critical for time-in-stage questions - if SF tracks OpportunityHistory or a stage-change log, this must be included in the sync)

---

### Use case 3: Bookings to Billings foundation

**Goal:** Show Computer can support forward-looking revenue modeling by analyzing close-date distribution and lag metrics.

| # | Question | Quantitative success criteria |
|---|----------|-------------------------------|
| 3.1 | Distribution of Q1 2026 close dates (Jan vs. Feb vs. Mar) | Monthly counts match SF exactly (0% variance - these are counts, not estimates) |
| 3.2 | Average lag from creation to close by service line, year-over-year | Average within ±1 day; YoY trend direction 100% correct |
| 3.3 | Average lag from close date to anticipated start date | Average within ±1 day of SF calculation |
| 3.4 | Current open pipeline by service line and expected close date | Total pipeline value within ±1% of live SF; opportunity count exact match |

**Dependencies:**
- All Use Case 1 dependencies, plus:
- **"Anticipated Start Date" custom field** synced from SF (confirm field API name)
- Open opportunities with populated expected close dates

---

### Use case 4: BU bucketing & sub-service-line parsing (skill-powered)

**Goal:** Enable users to ask questions at Business Unit or sub-service-line level without manually specifying grouping rules each time.

| # | Question | Quantitative success criteria |
|---|----------|-------------------------------|
| 4.1 | "What's Q1 revenue by BU?" | 100% of service lines assigned to correct BU; BU totals within ±1% of manual sum |
| 4.2 | "Break down ACS into sub-service lines" | 100% of opportunities classified into correct sub-bucket; sub-totals within ±1% |
| 4.3 | "How does CEMS Integration pipeline compare to CEMS Service?" | Correct split with 0 misclassified opportunities |

**Dependencies:**
- All Use Case 1 dependencies, plus:
- **BU grouping rules provided by ATG** (which service lines map to which BU)
- **Sub-bucket parsing rules provided by ATG** (keyword in opp name? Secondary field? Naming convention?)
- Custom skill built and installed in workspace

---

## Dependency summary

| Dependency | Required for | Status | Owner |
|------------|-------------|--------|-------|
| Salesforce sync configured and active | All use cases | TBD | DevRev / ATG Admin |
| Opportunity object with standard fields (Amount, Stage, Close Date, Created Date, Account) | All use cases | TBD | Verified during sync setup |
| Service Line field identified and synced | All use cases | TBD | ATG to confirm field API name |
| Stage history / duration data synced | Use Case 2 (time-in-stage) | TBD | Confirm SF tracks OpportunityHistory |
| "Anticipated Start Date" custom field synced | Use Case 3 (close-to-start lag) | TBD | ATG to confirm field API name |
| BU grouping taxonomy provided | Use Case 4 | TBD | ATG stakeholder |
| Sub-service-line parsing rules provided | Use Case 4 | TBD | ATG stakeholder |
| Custom BU bucketing skill built | Use Case 4 | TBD | DevRev (post-mapping receipt) |

---

## Phased rollout recommendation

**Phase 1 - Validate the pipe (Week 1)**  
Connect SF, verify field mapping, run Use Case 1 questions, confirm numbers match.

**Phase 2 - Operational analytics (Week 1-2)**  
Run Use Case 2 and 3 questions. Identify any missing fields (stage history, custom dates). Resolve gaps.

**Phase 3 - BU skill build (Week 2-3)**  
Collect mapping rules from ATG. Build and install the bucketing skill. Validate Use Case 4.

**Phase 4 - Demo & expand (Week 3-4)**  
Full walkthrough with ATG stakeholders. Collect feedback. Identify additional questions or data sources for production rollout.

---

## Risks & mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| Stage history not available in SF sync | Cannot answer time-in-stage questions | Pre-validate during setup; if unavailable, calculate estimated stage duration from snapshot timestamps |
| "Anticipated Start Date" field doesn't exist or is rarely populated | Use Case 3 partially blocked | Confirm field existence upfront; if sparse, flag data quality to ATG |
| Service line values are inconsistent (typos, variations) | Grouping logic breaks | Pull distinct values early; build normalization into the skill |
