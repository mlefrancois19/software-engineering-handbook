# 09 — Infrastructure (Cloud & On-Prem)

Infrastructure is software: versioned, reviewed, applied through CI, and repaired like application code. Cloud and on-prem differ in control and lead times, not in fundamentals.

## Infrastructure as code

- All provisioning comes from code (Terraform/OpenTofu, Pulumi, CloudFormation) and configuration from code (Ansible and similar); no click-ops, no one-off manual changes in production.
- Remote state with locking; plan output is reviewed in the PR; apply runs through CI. Drift between environments is a bug (chapter 07).
- Secrets never live in IaC code or state — injected from a secret manager at runtime (chapter 05).

## Immutable & disposable

- Everything is built once and replaced, never mutated in place: images are built in CI (chapter 07) and patched by rebuilding and redeploying.
- SSH-ing into a running instance to "fix" it is forbidden — the fix would be unproven and lost; rebuild instead.
- Auto-healing: failed health checks replace the instance via the orchestrator, no humans in the loop.

## Security by default

- Least privilege: per-service IAM roles/service accounts, default-deny network segmentation, no standing admin credentials.
- Encryption everywhere: TLS in transit (mTLS on sensitive paths), encryption at rest for disks, databases, and object storage, keys in a managed KMS/HSM.
- Audit logging for all access and changes — immutable, retained, monitored.

## Resilience

- N+1 redundancy minimum within a failure domain; multi-AZ (and multi-region where users demand); failover paths are exercised, not just configured. On-prem, the failure domains are your own: redundant power, network paths, and cooling.
- Autoscaling with headroom and connection-draining scale-down; scale on utilization and queue depth, not just raw traffic.
- Degrade gracefully: circuit breakers and bulkheads keep one failing dependency from taking down the service (chapter 03).
- Game days on a schedule: kill an instance, partition a network, revoke a dependency — in staging first. If the response is not a rehearsed runbook, the plan did not exist.

## Backups & disaster recovery

- RPO and RTO defined per system, owned by the team, documented in the service catalog.
- Backups are restored on a schedule — an untested backup is a hope, not a plan. Restore drills at least quarterly for critical systems.
- DR runbooks exist and are exercised; on-prem adds off-site media copies and test restores on the target hardware class.

## Patching & vulnerability management

- Automated image/rebuild cadence; critical CVEs get a fix SLA (chapter 05).
- On-prem adds a hardware lifecycle: firmware and driver patching, disk health monitoring (SMART), and a refresh plan ahead of vendor EOL.

## Capacity & cost

- Capacity is planned, not discovered in an incident: trend usage, forecast growth, and know your procurement lead time — the cloud adds capacity in minutes, on-prem in weeks to months (hardware, power, space).
- Tag/label every resource with owner, service, environment: for cost allocation, chargeback, and finding things.
- Right-size instances, use relevant pricing models (spot/reserved) deliberately, and clean up orphans.

## Cloud vs on-prem — where practices differ

| Concern | Cloud | On-prem |
|---------|-------|---------|
| Provisioning | API, minutes, pay-per-use | Procurement + racking, weeks to months, sunk cost |
| Failure domains | AZ/region, managed by provider | Redundant power/network/cooling — your design |
| Patching | Rebuild images; vendor patches managed services | Maintenance windows; OS + firmware + hardware lifecycle |
| Capacity | Scale on demand (watch unit economics) | Forecast, plan lead times, overbuild or wait |
| Lock-in | Prefer standards (k8s, OTel, S3-compatible APIs) | Same standards — constraints are self-imposed |
| DR | Cross-region replication, provider tooling | Off-site media, tested restores on target hardware |