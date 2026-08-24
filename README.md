# Kentik ServiceNow Integration

This repository contains Kentik's reference integration package for ServiceNow. The package is distributed as a ServiceNow update set XML file that can be imported into a customer-managed ServiceNow instance.

The imported package provides ServiceNow flows, triggers, automation, and UI actions for connecting ServiceNow incidents and alerts with Kentik alert workflows.

## Integration Capabilities

Imported packages can include automation for:

- sending ServiceNow incident context back to Kentik alerts
- acknowledging Kentik alerts from ServiceNow
- clearing Kentik alerts from ServiceNow
- sending ServiceNow work notes or comments to Kentik
- launching Kentik AI investigation from ServiceNow incident workflows

Package-specific import, configuration, and operation instructions are included with the documentation in the `packages` directory.

## Self-Managed Use

This project is provided as a reference implementation. Customers import, configure, operate, and maintain the package in their own ServiceNow environments.

Before importing into production, review the package in a non-production ServiceNow instance and validate the flows, triggers, permissions, and automation against your organization's ServiceNow configuration and change-management process.

## Kentik Configuration

Configure Kentik to send alerts to ServiceNow using the ServiceNow notification channel and Events API payload. The ServiceNow package expects Kentik alert metadata to be available so it can link ServiceNow records back to the originating Kentik alert.

## License

This project is licensed under the Apache License 2.0. See `LICENSE` for details.

The integration is provided as-is, without warranties or guarantees. Review and test it before using it in production.
