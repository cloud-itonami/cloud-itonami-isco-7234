# Business Model: Bicycle Repair Workshop Scheduling and Logistics Coordination Practice

## Classification

- Repository: `cloud-itonami-isco-7234`
- ISCO-08: `7234`
- Occupation: Bicycle and Related Repairers
- Social impact: worker-safety, consumer-safety, sustainable-mobility

## Customer

- bicycle repair shop operators / independent workshops
- bicycle repair crews / repair-crew cooperatives

## Offer

- crew shift/task/bay scheduling coordination
- repair-job/inventory/progress-record logging
- bicycle-parts supply-order coordination
- safety-concern surfacing to shop safety officers

## Revenue

- monthly retainer
- per-crew coordination fee

## Trust Controls

- no direct finalization of a repair-execution decision (e.g.
  deciding to proceed with or complete a specific repair job), ever
- no finalization of a roadworthiness-clearance decision (e.g.
  declaring a bicycle roadworthy or cleared for sale), ever
- no override of a shop safety officer's judgment, ever
- flagged safety concerns (mechanical hazard, equipment condition)
  always route to human sign-off, regardless of confidence
- no supply order above the registered cost threshold without
  governor-gated human sign-off
- operating and coordination records are auditable, not editable
