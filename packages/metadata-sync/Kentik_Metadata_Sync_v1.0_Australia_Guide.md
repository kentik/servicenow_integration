# Kentik Metadata Sync for ServiceNow
## Deployment & User Guide

### 1. Prerequisites

#### 1a. ServiceNow-Side Prerequisites
Confirm these on the target instance before importing:

* **CMDB:** The **cmdb_ci_netgear** table (CI Class Models / CMDB plugin) must be active. Kentik device records are transformed into this table as Network Gear configuration items.
* **Import Set / Table APIs:** No additional plugin is required — the package uses the standard **Import Set API** (`/api/now/import`) and **Table API** (`/api/now/table`), which are part of the core platform.
* **Application Scope:** The package installs into its own scoped application, `Kentik Metadata Sync` (`x_2088674_kentik_0`).

#### 1b. Kentik-Side Prerequisites (Data Source Setup)
This package is push-based: a Kentik-side process (configure and enable on Kentik platform) reads device data from the Kentik API and posts it to the ServiceNow Import Set endpoint (`/api/now/import/x_2088674_kentik_0_u_kentik_devices`), authenticated with the REST API key created in section 3. The JSON body sent to ServiceNow should use the same attribute names as Kentik's device metadata (e.g. `deviceName`, `deviceDescription`, `site/id`, `site/siteName`, etc.) — these map 1:1 to the staging table's `import_attribute_name` values. The `rawPayload` contains the raw JSON response from Kentik Devices API which can be used to extract out other available fields if staging table columns doesn't support them. Each device payload must include Kentik's device `id`, since it is used as the unique key for updating existing CMDB records.

The XML config file is built with ServiceNow Studio and is named `Kentik_Metadata_Sync_v1.0.Australia.xml`. Importing it does the following:

1. Creates a role, `x_2088674_kentik_0.kentik_metadata_syncer`, with full CRUD permission on the staging table `x_2088674_kentik_0_u_kentik_devices`, plus read permission on `sys_dictionary` (so the role can look up the available columns for a given table).
2. Creates the staging table, pre-populated with the Kentik device columns.
3. Creates a default transform map that converts staging rows into `cmdb_ci_netgear` records.
4. Creates an API access policy that allows calls to the Import Set API endpoints.

---

### 2. Installation & Initial Setup
1.  Navigate to **System Update Sets** > **Retrieved Update Sets** > **Import Update Set from XML**.
2.  Choose the XML file and click **Upload**.
3.  **Preview Update Set**, then **Commit Update Set**.
4.  Navigate to **System Import Sets** > **Administration** > **Data Sources**. You should see `kentik_devices` in the table — click on it.
5.  Copy down the Import Set table name shown on the data source: `x_2088674_kentik_0_u_kentik_devices`.
6.  From the data source, click `transform_kentik_device_to_netgear` in the related Transform Maps list to review/configure the field mapping.

---

### 3. Configuration (Administrative Setup)
The integration accepts inbound API calls secured by a REST API key. Treat this key as a secret and restrict access to it to administrators only.

1.  Navigate to **System Web Services** > **API Access Policies** > **REST API Key** > **New**.
2.  Give the key a name.
3.  Create a new user then tie to the user to the API key. When creating the user, give it User ID, First name and Last name. Make Identity type **Machine** and check **Internal Integration User**.
3.  **Submit**. Select the created API key, copy down the generated token — this is the API key value Kentik needs on every request.
4.  Navigate to **System Security** > **Users and Groups** > **Users**, filter for the user created in step 2, and open it.
5.  Edit **Roles** and assign the `x_2088674_kentik_0.kentik_metadata_syncer` role to it — this is what grants the API user access to the staging table and `sys_dictionary`.
6.  **Endpoint to send device data to:**
    ```
    POST https://<instance>.service-now.com/api/now/import/x_2088674_kentik_0_u_kentik_devices
    ```
    Submit one JSON object per device using Kentik's device attribute names as the keys, and the token from step 3 as the API key header.

---

### 4. Automation (Transform Map)
Unlike the alerting package, this package does not use Flow Designer — records are converted automatically by a **Transform Map** as soon as they land in the staging table, so there is nothing to manually activate after import.

* **Transform Map:** `transform_kentik_device_to_netgear` (staging table `x_2088674_kentik_0_u_kentik_devices` → `cmdb_ci_netgear`) is active by default.
* **Matching key:** Kentik's device `id` (`u_id`) is coalesced against `correlation_id` on `cmdb_ci_netgear`, so re-sending the same device updates the existing CI instead of creating a duplicate.
* **Soft-delete handling:** If an incoming record has `deleted: true`, the transform map does not delete the CI. Instead it sets `operational_status` and `install_status` to **Retired** and prefixes the CI name with `[RETIRED]`, so historical CMDB relationships and audit history are preserved.

---

### 5. User Guide: Field Mapping
Only a subset of the staging table's columns are currently mapped onto the CI record. 

| Kentik Attribute (staging field)     | `cmdb_ci_netgear` Field |
|---------------------------------------|-------------------------|
| `deviceName` (`u_devicename`)         | `name`                  |
| `deviceDescription` (`u_devicedescription`) | `short_description` |
| `id` (`u_id`)                         | `correlation_id` (coalesce key) |

#### Reviewing Staged Data
* Navigate to **System Import Sets** > **Administration** > **Data Sources** > `kentik_devices` to review the source, or search the table `x_2088674_kentik_0_u_kentik_devices` directly to see the raw rows as received from Kentik before transformation.
* Navigate to **Configuration** > **Network Gear** (or search `cmdb_ci_netgear`) to see the resulting CI records.

---

### 6. Maintenance & Support
* **Import Logs:** Check **System Import Sets** > **Load History**, or open a row on the `kentik_devices` staging table and view **Related Links** > **Transform History** for per-record transform status and errors.
* **Access Errors (401/403):** Confirm the caller is sending the correct token for the REST API Key created in section 3, and that the user tied to that key is assigned the `x_2088674_kentik_0.kentik_metadata_syncer` role.
