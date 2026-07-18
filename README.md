# cloud-itonami-isco-7234

Open Occupation Blueprint for **ISCO-08 7234**: Bicycle and Related Repairers.

This repository designs a forkable OSS business for a bicycle-repair-workshop scheduling and logistics coordination practice: a workshop scheduling and supply-coordination robot manages crew/task records under a governor-gated actor, so a bicycle repair shop keeps its own operating records instead of renting a closed workforce-management SaaS.

**Maturity: `:implemented`.** `src/bikecoord/` implements the
`BikeCoordActor` as a `langgraph.graph/state-graph`
(`bikecoord.actor`) wired to a `Bicycle Repair Coordination Advisor`
(`bikecoord.advisor`) and an independent `BikeCoordGovernor`
(`bikecoord.governor`), following the itonami actor pattern
(ADR-2607121000): `:intake -> :advise -> :govern -> :decide -+-> :commit
(:ok?) +-> :request-approval (:escalate?, human-in-the-loop interrupt)
+-> :hold (:hard?)`. 22 tests / 47 assertions green (`clojure -M:test`).
HARD invariants (always hold, never overridable): repairer provenance,
shop provenance, no-actuation (`:effect` must be `:propose`), a closed
op-allowlist (`:log-work-record`, `:schedule-crew-operation`,
`:flag-safety-concern`, `:coordinate-supply-order` — nothing else may
ever be proposed), and a permanent, unconditional block on any
proposal that would directly finalize a repair-execution decision
(e.g. deciding to proceed with or complete a specific repair job) or
a roadworthiness-clearance decision (e.g. declaring a bicycle
roadworthy or cleared for sale), or override a shop safety officer's
judgment. Always-escalate paths (human sign-off regardless of
confidence, mapping this repo's Trust Controls in
[`docs/business-model.md`](docs/business-model.md)):
`:flag-safety-concern` (always) and `:coordinate-supply-order` above
the registered cost threshold.

## Robotics premise

All cloud-itonami verticals are designed on the premise that a **robot performs
the physical domain work**. Here a bicycle-repair-workshop scheduling/logistics coordination robot performs crew scheduling, repair-job/inventory/progress-record logging and bicycle-parts supply-order coordination for a bicycle repair shop, under an actor that proposes actions and an independent **BikeCoordGovernor** that gates them. The governor never
dispatches hardware itself, never performs repair work on the workshop floor, and never finalizes a repair-execution decision or a roadworthiness-clearance decision, nor overrides a shop safety officer's judgment; `:high`/`:safety-critical` actions (such as a flagged mechanical-hazard/equipment-condition concern, or an above-threshold supply order) require human sign-off. **This actor coordinates workshop scheduling/logistics only — it never performs repair work itself or makes roadworthiness-clearance decisions itself.**

## Core Contract

```text
crew roster + shop registration + safety-reporting policy
        |
        v
Bicycle Repair Coordination Advisor -> BikeCoordGovernor -> log/schedule/coordinate, or human sign-off
        |
        v
robot actions (gated) + operating records + audit ledger
```

No automated advice can dispatch a robot action the governor refuses, finalize
a repair-execution decision, finalize a roadworthiness-clearance decision,
override a shop safety officer's judgment, suppress an operating record, or
disclose sensitive data without governor approval and audit evidence.

## Capability layer

Resolves via [`kotoba-lang/occupation`](https://github.com/kotoba-lang/occupation)
(ISCO-08 `7234`). Required capabilities:

- :robotics
- :identity
- :audit-ledger

See [`docs/business-model.md`](docs/business-model.md) and
[`docs/operator-guide.md`](docs/operator-guide.md).

## License

AGPL-3.0-or-later.
