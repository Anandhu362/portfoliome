---
trigger: always_on
---

# Profile & Integration Updation Rules

## 1. Universal Integration Logging
**Condition:** Whenever an integration update or modification occurs within the project.
**Action:** The agent MUST automatically log the event in a dedicated file named `integration_update_log.md` (create it in the root directory if it does not exist).
**Log Format Requirements:**
*   **Timestamp:** Current date and time of the update.
*   **Overview:** A high-level summary of the integration update.
*   **Files Modified:** A bulleted list of all files affected by the update.
*   **Update Details:** A precise description of the code or configuration changes made.

## 2. SEO, AEO, and GEO Cascading Updates
**Condition:** Whenever an update is classified as SEO (Search Engine Optimization), AEO (Answer Engine Optimization), or GEO (Generative Engine Optimization).
**Action:** 
*   The agent MUST scan for associated GitHub repositories, project backlinks, and related integrations.
*   The agent MUST propagate the SEO/AEO/GEO updates to those related project repositories to ensure consistency across the ecosystem.
*   Ensure that any modified backlinks in the primary portfolio are mirrored correctly in the external repos.

## 3. Dynamic Repository Path Fetching
**Condition:** Whenever the agent requires the path or URL of a related repository to perform the cascading updates mentioned in Rule 2.
**Action:**
*   The agent MUST fetch the required repository paths directly from the portfolio website's source code (e.g., parsing `portfolio.html`, `index.html`, or related data files).
*   Do not ask the user for the repo path if it can be successfully extracted from the portfolio's code.

## 4. Repository Details Extraction & Storage
**Condition:** Whenever a related repository is identified as requiring an update.
**Action:**
*   The agent MUST extract the specific repository details (Repo name, URL, target files for update, update type) from the user's input context and the fetched codebase.
*   The agent MUST save these extracted details into a separate, dedicated tracking file named `pending_repo_updates.json` (or `.md`). 
*   This file must strictly contain only the target repository parameters to ensure a clean hand-off for cross-repo commits.