# Kentik Metadata Sync for ServiceNow

This package is Kentik's reference integration for syncing device metadata into the ServiceNow CMDB. It is distributed as a ServiceNow update set XML file that can be imported into a customer-managed ServiceNow instance.

The imported package provides a staging (import set) table, a transform map, and the role needed to accept Kentik device data and turn it into `cmdb_ci_netgear` configuration items. Authentication (the REST API Key and its access policy) is set up manually after import — see the deployment guide.

## Integration Capabilities

Importing this package enables:

- an authenticated Import Set API endpoint that accepts Kentik device metadata
- automatic transformation of staged device records into `cmdb_ci_netgear` CIs, matched by Kentik device ID
- soft-delete handling that retires a CI (rather than deleting it) when Kentik reports a device as removed

See [Kentik_Metadata_Sync_v1.0_Australia_Guide.md](Kentik_Metadata_Sync_v1.0_Australia_Guide.md) for import, configuration, and operation instructions.

## Self-Managed Use

This project is provided as a reference implementation. Customers import, configure, operate, and maintain the package in their own ServiceNow environments.

Before importing into production, review the package in a non-production ServiceNow instance and validate the staging table, transform map, and access policies against your organization's ServiceNow configuration and change-management process.

## Kentik Configuration

Configure a Kentik-side export process that reads device metadata from the Kentik API and posts it to the ServiceNow Import Set API endpoint for the `x_2088674_kentik_0_u_kentik_devices` table, authenticated with the token from the REST API Key created during setup.

## Authorization Model

This self-managed integration uses a dedicated ServiceNow role, `x_2088674_kentik_0.kentik_metadata_syncer`. The role is not grantable or delegable, so it can only be assigned to a user by an administrator. Its actual permissions are:

* **Staging table** (`x_2088674_kentik_0_u_kentik_devices`): create, read, and write (write is required so that field values submitted on insert are actually persisted — `create` alone only permits an empty row), but not delete. The role has no way to delete staged rows, and the only exposed entry point (the Import Set `insertMultiple` API) always inserts a new row rather than updating an existing one — re-sending a device simply inserts a new staging row, which the transform map then reconciles into the CI record.
* **`cmdb_ci_netgear`**: read access to the entire table (not scoped to Kentik-managed records). This is intentionally broad so integration tests can read back and verify any CI written by the transform map; if your environment doesn't need that read-back, consider further restricting or removing this ACL.
* **`sys_dictionary`**: read-only, used to look up the available columns on a given table.

Assign this role only to the user created alongside the REST API Key used by the Kentik-side export job — do not use an administrator account for this integration.

## License

This project is licensed under the Apache License 2.0. See `LICENSE` for details.

The integration is provided as-is, without warranties or guarantees. Review and test it before using it in production.
