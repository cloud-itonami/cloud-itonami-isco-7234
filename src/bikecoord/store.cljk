(ns bikecoord.store
  "SSoT for the ISCO-08 7234 bicycle and related repairers workshop
  scheduling/logistics coordination actor (itonami actor pattern,
  ADR-2607121000 / CLAUDE.md Actors section; README's 'Robotics
  premise' — a bicycle-repair-workshop scheduling/logistics
  coordination robot performs crew scheduling, repair-job/inventory/
  progress record logging and bicycle-parts supply-order coordination
  for a bicycle repair shop under this advisor/governor pair, which
  never dispatches hardware itself, never performs repair work
  itself, and never finalizes a repair-execution decision or a
  roadworthiness-clearance decision (declaring a bicycle safe to ride
  or sell), and never overrides a shop safety officer's judgment —
  those remain the shop safety officer's exclusive judgment). Modeled
  closely on cloud-itonami-isco-7211's foundrycoord.store (closest
  workshop-mechanical-safety domain shape).

  Domain:

    repairer — a registered bicycle-repair-shop crew member
               (:repairer-id, :name)
    shop     — a registered bicycle-repair shop/workshop {:shop-id
               :name :max-supply-cost}. `:max-supply-cost` is an
               informational registered ceiling used only to decide
               whether a `:coordinate-supply-order` proposal escalates
               to human sign-off (the governor never blocks a
               within-threshold order outright; it only decides
               commit vs. escalate).
    record   — a committed operating record (a logged repair-job/
               inventory/progress entry, a scheduled crew/bay
               operation, a flagged safety concern, or a coordinated
               bicycle-parts supply order) — written ONLY via
               commit-record!.
    ledger   — append-only audit trail, commit or hold.")

(defprotocol Store
  (repairer [s repairer-id])
  (shop [s shop-id])
  (records-of [s repairer-id])
  (ledger [s])
  (register-repairer! [s repairer])
  (register-shop! [s shop])
  (commit-record! [s record])
  (append-ledger! [s fact]))

(defrecord MemStore [a]
  Store
  (repairer [_ repairer-id] (get-in @a [:repairers repairer-id]))
  (shop [_ shop-id] (get-in @a [:shops shop-id]))
  (records-of [_ repairer-id] (filter #(= repairer-id (:repairer-id %)) (:records @a)))
  (ledger [_] (:ledger @a))
  (register-repairer! [s r]
    (swap! a assoc-in [:repairers (:repairer-id r)] r) s)
  (register-shop! [s sh]
    (swap! a assoc-in [:shops (:shop-id sh)] sh) s)
  (commit-record! [s record]
    (swap! a update :records (fnil conj []) record) s)
  (append-ledger! [s fact]
    (swap! a update :ledger (fnil conj []) fact) s))

(defn mem-store
  ([] (mem-store {}))
  ([seed] (->MemStore (atom (merge {:repairers {} :shops {} :records [] :ledger []}
                                    seed)))))
