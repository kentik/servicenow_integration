# Kentik Integration for ServiceNow
## Deployment & User Guide

### 1. Prerequisites

#### 1a. ServiceNow-Side Prerequisites
Confirm these on the target instance before importing:

* **Required Plugins:** The following must be active:
    * **Event Management** — required for the `em_alert` table used by alert-related flows and lookups.
    * Any Event Management dependencies your instance prompts for during activation.
* **Flow Designer / Workflow Studio Version:** Ensure Flow Designer and Workflow Studio are updated to the latest available version for your release family. Older versions may not render trigger conditions (e.g., "Changes") correctly in the UI, even though committed flows continue to run as designed.

#### 1b. Kentik-Side Prerequisites (Data Source Setup)
For the integration to correctly identify and link alerts, the data sent from Kentik must include specific metadata.

* **ServiceNow Notification Channel:** When configuring Kentik to send alerts to ServiceNow, ensure the Events API selection is used. This ensures the payload structure is compatible with the ITOM `em_alert` table.
* **Custom Webhooks:** If using custom webhooks, the payload must include the Kentik Alarm ID and metadata within the additional information field.

---

### 2. Installation & Initial Setup
1.  **Import:** Navigate to **System Update Sets** > **Retrieved Update Sets**.
2.  Click **Import Update Set from XML** and upload the provided file.
3.  **Preview:** Click on the Kentik Integration record that appears in the list, then click the **Preview Update Set** button.
4.  **Commit:** Once previewed and any potential conflicts are reviewed, click **Commit Update Set**.

---

### 3. Configuration (Administrative Setup)
The integration uses a dedicated settings page to manage authentication via System Properties. Treat the API token as a secret and restrict access to the configuration/token property to administrators only.

1.  Navigate to **Kentik Integration** > **Configuration**.
2.  **Portal Base URL:** Enter your Kentik UI instance URL (e.g., `https://portal.kentik.com`).
3.  **API Base URL:** Enter the endpoint used for API calls (e.g., `https://grpc.api.kentik.com`).
4.  **API User Email:** Enter the email associated with your Kentik API token.
5.  **API Token:** Enter your Kentik API authentication token.

---

### 4. Activating Automation (Flows)
Flows are imported in an inactive state. You must manually activate them to enable the sync:

1.  Navigate to **Process Automation** > **Flow Designer**.
2.  Locate the following flows:
    * `Send Comment To Kentik`
    * `Kentik Sync Alert Acknowledge`
    * `Send Incident Information To Kentik`
3.  For each flow: Click the name, click **Edit**, and click **Activate**.

> **Note:** If a flow trigger appears to be missing conditions or fields after import (e.g., no "Changes" operator, or a table like `em_alert` is not selectable), verify Event Management is activated and that Flow Designer/Workflow Studio are on their latest version before troubleshooting further.

---

### 5. User Guide: Button Actions & Logic

#### Incident Data Sync
* **Automatic Context Sharing:** When an Incident is created from a ServiceNow Alert associated with Kentik, the integration automatically sends the Incident ID and Incident URL back to Kentik. These details are displayed under the Kentik Alert Details section in the Kentik portal for seamless cross-platform navigation.

#### Incident Form Actions
* **Ask Kentik AI:** Opens a chat modal to interact with Kentik's AI Advisor. You can post the analysis and a deep-link to the session directly into the Incident Work Notes.
* **Ack Kentik Alerts:** Bulk-acknowledges every Kentik alert associated with the incident.
* **Clear Kentik Alerts:** Mass-clears all associated Kentik alerts. Use this when the incident is resolved.

#### Alert Form Logic
* **Acknowledge button:** When a technician acknowledges a Kentik-sourced alert, the background flow automatically triggers the corresponding action in the Kentik portal.

---

### 6. Maintenance & Support
* **Logs:** Filter **System Logs** > **All** for `KentikDebug`.
* **Flow Errors:** If actions are not syncing, check **Flow Designer** > **Executions**.