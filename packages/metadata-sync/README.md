# Kentik Metadata Sync for ServiceNow

This package is Kentik's reference integration for syncing device metadata into the ServiceNow CMDB. It is distributed as a ServiceNow update set XML file that can be imported into a customer-managed ServiceNow instance.

The imported package provides a staging (import set) table, a transform map, and the API access policies/roles needed to accept Kentik device data and turn it into `cmdb_ci_netgear` configuration items.

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

This self-managed integration uses a dedicated ServiceNow role, `x_2088674_kentik_0.kentik_metadata_syncer`, scoped to the minimum access needed to post device data (CRUD on the staging table, plus read on `sys_dictionary` to look up table columns) and read back the resulting CI records. Assign this role only to the user created alongside the REST API Key used by the Kentik-side export job — do not use an administrator account for this integration.

## License

This project is licensed under the Apache License 2.0. See `LICENSE` for details.

The integration is provided as-is, without warranties or guarantees. Review and test it before using it in production.
