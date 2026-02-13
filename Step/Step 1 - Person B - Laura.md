## Step 1 – Person B (Laura) – Data Findings

---

## 1. Clean dataset summary (load & inspect)

- `logs.csv` – 25,000 events, 8 variables  
- `sessions.csv` – 1,200 sessions, 3 variables  
- `questionnaires.csv` – 150 users, 7 variables  

No missing values were detected in any of these three files.

---

## 2. Join keys (identified)

- **logs ↔ sessions:** `session_id`  
  - Each event in `logs.csv` belongs to exactly one `session_id`.  
  - Each `session_id` appears exactly once in `sessions.csv`.  

- **sessions ↔ questionnaires:** `user_id`  
  - Each session in `sessions.csv` is associated with one `user_id`.  
  - Each `user_id` from sessions has a corresponding row in `questionnaires.csv`.  

These keys allow construction of a unified dataset at session level (behavior + experimental context + subjective evaluations).

---

## 3. Session and user consistency (verified)

- Unique `session_id` in `logs.csv`: **1,200**  
- Unique `session_id` in `sessions.csv`: **1,200**  
- Sessions present in logs but not in sessions: **0**  
- Sessions present in sessions but not in logs: **0**  
- Sessions with more than one `user_id` or more than one `interface_version`: **0**  

- Unique `user_id` in `sessions.csv`: **150**  
- Unique `user_id` in `questionnaires.csv`: **150**  
- Users present in sessions but not in questionnaires: **0**  
- Users present in questionnaires but not in sessions: **0**  

Conclusion: **the datasets are fully consistent** for joins on `session_id` and `user_id`; no sessions or users need to be excluded for consistency reasons.

---

## 4. Classify variables (Subjective / Behavioral / Experimental)

### 4.1 Subjective variables (questionnaire-based)

Source: `questionnaires.csv`  
- `SUS_score` – perceived usability  
- `NASA_TLX` – cognitive load  
- `UES_engagement` – engagement with the system  
- `IMI_autonomy` – perceived autonomy  
- `IMI_competence` – perceived competence  
- `intention_reuse` – intention to reuse the system  

These variables capture **user perceptions and attitudes**.

### 4.2 Behavioral variables (observed behavior)

Source: `logs.csv` (event-level, to be aggregated at session level if needed)  
- `log_id` – event identifier  
- `session_id` – link to session and user  
- `timestamp` – time of the event  
- `action_type` – type of action (`click`, `drill_down`, `filter`, `hover`)  
- `element_id` – interface element (`chart_trend`, `KPI_costs`, `KPI_sales`, `table_details`)  
- `duration_sec` – duration of the action (seconds)  
- `error_flag` – whether an error occurred (0/1)  
- `decision_made` – whether a decision was made on this event (0/1)  

These variables describe **what users actually do** with the dashboard.

### 4.3 Experimental variables (design / context)

Source: `sessions.csv`  
- `session_id` – experimental unit, link to logs  
- `user_id` – participant identifier, link to questionnaires  
- `interface_version` – A/B condition (`A` vs `B`)  

These variables represent the **experimental setup and context** (which version of the interface each user saw).

---

## 5. Variable description table (for Person A)

| Variable | Source file | Category | Description |
|---------|-------------|----------|-------------|
| `log_id` | `logs.csv` | Behavioral | Unique identifier of each log event |
| `session_id` | `logs.csv` | Behavioral | Session identifier for the event (links to `sessions.csv`) |
| `timestamp` | `logs.csv` | Behavioral | Date and time when the event occurred |
| `action_type` | `logs.csv` | Behavioral | Type of user action (`click`, `drill_down`, `filter`, `hover`) |
| `element_id` | `logs.csv` | Behavioral | Interface element interacted with (`chart_trend`, `KPI_costs`, `KPI_sales`, `table_details`) |
| `duration_sec` | `logs.csv` | Behavioral | Duration of the action in seconds |
| `error_flag` | `logs.csv` | Behavioral | Indicates whether an error occurred during the event (0/1) |
| `decision_made` | `logs.csv` | Behavioral | Indicates whether a decision was made at this event (0/1) |
| `session_id` | `sessions.csv` | Experimental | Identifier of the session (links to `logs.csv`) |
| `user_id` | `sessions.csv` | Experimental | Identifier of the user (links to `questionnaires.csv`) |
| `interface_version` | `sessions.csv` | Experimental | Version of the dashboard shown to the user (`A` or `B`) |
| `user_id` | `questionnaires.csv` | Key | Identifier of the user (join key with `sessions.csv`) |
| `SUS_score` | `questionnaires.csv` | Subjective | Perceived usability of the system (System Usability Scale) |
| `NASA_TLX` | `questionnaires.csv` | Subjective | Self-reported cognitive load |
| `UES_engagement` | `questionnaires.csv` | Subjective | Self-reported engagement with the system |
| `IMI_autonomy` | `questionnaires.csv` | Subjective | Perceived autonomy using the system |
| `IMI_competence` | `questionnaires.csv` | Subjective | Perceived competence using the system |
| `intention_reuse` | `questionnaires.csv` | Subjective | Intention to reuse the dashboard in the future |


