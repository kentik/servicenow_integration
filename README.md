# Kentik ServiceNow Integration

This repository contains Kentik's reference integration packages for ServiceNow. Each package is distributed as a ServiceNow update set XML file that can be imported into a customer-managed ServiceNow instance.

## Packages

| Package | Description |
|---|---|
| [alerting](packages/alerting) | Flows, triggers, and UI actions connecting ServiceNow incidents/alerts with Kentik alert workflows (acknowledge, clear, AI investigation, incident context sync). |
| [metadata-sync](packages/metadata-sync) | Import Set staging table and transform map that syncs Kentik device metadata into the ServiceNow CMDB (`cmdb_ci_netgear`). |

Each package directory contains its own `README.md` with capabilities and authorization model, and a deployment/user guide with step-by-step import, configuration, and operation instructions.

## Self-Managed Use

These projects are provided as reference implementations. Customers import, configure, operate, and maintain each package in their own ServiceNow environments.

Before importing into production, review the package in a non-production ServiceNow instance and validate it against your organization's ServiceNow configuration and change-management process.

## License

This project is licensed under the Apache License 2.0. See `LICENSE` for details.

The integrations are provided as-is, without warranties or guarantees. Review and test them before using them in production.
