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

## Authorization Model

This self-managed integration uses the Kentik API credential configured by the ServiceNow administrator. Actions performed through the integration use that Kentik identity, even when different ServiceNow users trigger those actions from incidents, alerts, flows, or UI actions.

Use a dedicated least-privilege Kentik user for this integration. This should be a regular Kentik user reserved for the ServiceNow integration, with only the Kentik permissions needed for the workflows you enable, such as AI investigation, alert comments, acknowledge, clear, and incident context updates. If a workflow is not needed, leave the related ServiceNow flow, trigger, or UI action inactive or unavailable.

ServiceNow roles, ACLs, and application access controls still determine who can view or use ServiceNow records and actions, but they do not automatically map each ServiceNow user to a distinct Kentik user. Administrators should restrict integration configuration and Kentik-affecting actions to trusted users whose ServiceNow access is appropriate for the Kentik permissions exposed by the configured credential.

## License

This project is licensed under the Apache License 2.0. See `LICENSE` for details.

The integration is provided as-is, without warranties or guarantees. Review and test it before using it in production.
