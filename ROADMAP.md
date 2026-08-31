# SNCF Roadmap & Gaps

The foundation-wide roadmap: per-project roadmaps, the foundation roadmap, the certification
roadmap, and the projects we're still missing. Drafted, then reviewed by five independent
teammates; their corrections are folded in. It's a joke — but every item is buildable on a
spreadsheet, or it says why it isn't.

Horizons: **Now** (next), **Next** (this cycle), **Later** (someday, before the heat death).

---

## 0. The spine — do these first

Three keystones unblock most of the roadmap. Build these before the things that depend on them.

1. **Sheet-Native Resource Model** — a versioned manifest schema (the YAML-in-cells contract every
   project reads/writes). *Nothing else has a stable API without it.* **Now.**
2. **Sheetheus exposition-format tab** — the `/metrics` contract every project publishes. Unblocks
   Sheetfana, SheetFinOps, SheetAIOps, SheetQL, connectors, and conformance. **Now.**
3. **Shared reconciliation-loop framework** (Sheeternetes) — chunked reconcile + continuation token
   to beat the Apps Script 6-min cap, with `LockService` leader election. Reused by Sheetlux,
   Sheetstor, KEDA-style scalers, cloud-connectors. **Now.**

Then: **multi-host overlay networking** (lifts the one-sheet-one-node ceiling → unblocks Sheetstor
resync, Sheetmesh, SheetDNS, SheetLB, cloud connectors); **RBAC via protected ranges + real
Secrets** (the trust boundary under Secrets/Vault, multi-tenant FinOps, org-cert); **SheetHub
registry-in-cells** (unblocks SheetAssembly registry, SheetyStack air-gap, SheetSign);
**conformance suite** (legitimacy anchor before cert badges); **SheetCon logistics** (hard external
deadline — end of September).

**Known-limits doc (required):** Google Sheets caps — 10M cells/workbook, 50k chars/cell, Apps
Script 6-min execution + ~1/min triggers + UrlFetch quotas. This is both the load-bearing joke and
the real ceiling; every project states how it behaves at the wall.

---

## A. Per-project roadmaps

### Sheeternetes 🏛️ — orchestration (Kubernetes)
- **Now:** the shared reconcile framework (above); a real scheduler (row→node binding); an
  audit/events tab.
- **Next:** **real Secrets** — not hidden sheets (visible to any editor) but a *separate* spreadsheet
  under a distinct service-account ACL, or envelope-encrypted values, referenced by handle.
  RBAC via protected ranges. On-prem Excel/OpenOffice backend.
- **Later:** autoscaling = a **trigger-driven reconciler** that reads a Sheetheus metric and writes
  desired replicas (NOT conditional formatting — that only colours cells). Consistency story beyond
  `LockService`. DR references SheetVelero.

### Sheetlium 💾 — L3/L4 networking (Cilium)
- **Owns the datapath** (overlay networking lives here, not in Sheeternetes).
- **Next:** identity-based NetworkPolicy (not IP-based); a **Hubble analog = a flow-log tab** (highest-value add).
- Boundary: Sheetlium = L3/4 + policy; a basic Ingress may stub here but L7 belongs to SheetGate.

### Sheetlux CD 💾 — delivery (Argo CD)
- **Next:** drift detection + **auto-heal**, and **sync-waves** (ordering) — Argo's defining behaviours.
- Source mechanism, stated: `UrlFetchApp` polling of raw git HTTP under trigger quotas (no webhooks
  from a Sheet) — this caps sync latency. Prune; sync/health status back to a tab.

### Sheetstor 📝 — storage (LINSTOR)
- **Reframe:** no block layer exists → **async row-replication with a resync watermark**, not DRBD.
  No consensus → no quorum/split-brain safety; say so.
- Capacity accounting (cells-as-bytes) so the scheduler can place volumes. Snapshots reference SheetVelero.

### SheetOS 📝 — node OS (Talos)
- **Add the defining property:** declarative-only machine-config; **reject manual cell edits** and
  reconcile back to config (Talos has no interactive editing). `sheetstrap upgrade`; join-token security.

### Sheetmesh 📝 — L7 service mesh (Istio)
- Relabel: L7 traffic management + identity, distinct from Sheetlium (L3/4). Retries/timeouts/circuit-breaking.
- Identity: a per-workload ID + **shared-secret HMAC in a signature column** (buildable) rather than "mTLS-ish".
  Hybrid Excel↔Sheets peers.

### SheetHub 📝 — forge (GitLab)
- **Add the core:** merge-request/review flow + issue tracking (not only the read path).
- Read-only dumb-http clone; **chunked-blob registry-in-cells** (base64 sharded across many 50k-char cells) as an
  explicit milestone; SheetHub Actions (triggers as CI).

### Sheetelligence 📝 — AI / prompt-native
- **Next:** structured tool-use; a **plan/dry-run gate before apply**; an **eval/regression harness**
  (golden-manifest set) so prompt changes don't silently regress.

### SheetAIOps 📝 — AIOps
- Scope v1 to EWMA/threshold baselines (thin in-sheet history). **Cross-signal correlation / root-cause**;
  **human-approval gating** on auto-remediation (no flap loops). Owns anomaly detection, not scaling.

### SheetAssembly 📝 — WASM
- **Unblock the runtime question:** Apps Script's V8 doesn't expose `WebAssembly` → execute in the
  **browser (skctl-wasm)** or a wasm-aware kubelet on a Docker host, not in the Sheet. Lead with a
  single-module runner; component model is Later.

### skctl-wasm 📝 — browser CLI + the wasm runtime home
- Tie to SheetAssembly (this is where wasm runs). A **watch/stream** verb (long-poll); the concrete
  OAuth-to-Sheets-API auth flow. Full verb parity.

### SheetFinOps 📝 — cost (OpenCost)
- **Add the cost model first:** define the unit price (Apps-Script-second, cell, row, UrlFetch call) —
  nothing to allocate without it. Per-tenant breakdown; showback vs chargeback; trend from Sheetheus.

### Cloud connectors 📝 — (Cluster API — reframed)
- Reframe: Cloud Run/ACI are serverless runtimes (closer to Knative/Crossplane). Either be a
  **serverless broker**, or make it genuine CAPI (Machine/MachineSet lifecycle: create/scale/delete).
  Working GCP/Yandex/Azure; AWS via a SigV4 agent; a connector conformance test.

### SheetyStack 💾 — platform (Rancher/OpenShift)
- **Add Rancher's defining feature:** multi-cluster **fleet** — one control Sheet managing many
  downstream cluster-Sheets with a rollup view. `install --offline` (air-gap); channels/pinning;
  `upgrade`; a catalog/marketplace (ties to Shelm).

### Sheetheus 📝 — metrics (Prometheus)
- **Keystone:** the exposition-format tab schema (see §0). Then SheetQL, alerting **rules**,
  remote-write, retention/compaction. Resolution ceiling: scrape ≥60s (triggers ~1/min); cell caps
  bound cardinality — state both.

### Sheetfana 📝 — dashboards (Grafana)
- **Fastest visible win:** use **Sheets' native Chart API** (rendered charts for free). Dashboard-as-JSON;
  templated variables. Alerting = *notify* on Sheetheus rules (Prometheus fires, Grafana notifies).

### Language clients — PowerShell / Lisp / Brainfuck
- **Prerequisite:** a shared **API/verb spec** (OpenAPI-equivalent over the Sheets contract) + a
  **client conformance suite** all three (and skctl-wasm) target — otherwise "parity" is untestable.
- PowerShell: DSC resource + Gallery. Lisp: ASDF + Quicklisp dist. Brainfuck: a heroic `get pods` reader.

**Top-5 highest-leverage milestones:** (1) Sheetheus exposition schema; (2) shared reconcile
framework; (3) real Secrets; (4) API/verb spec + client conformance; (5) native Sheets charts in Sheetfana.

**Cross-cutting ownership (resolve the triple-claims):** networking → Sheeternetes(orchestration) /
Sheetlium(L3-4) / Sheetmesh(L7) / SheetGate(L7 ingress); autoscaling → HPA(metric-driven) /
SheetAIOps(detect only) / SheetKnative(event/scale-to-zero); backup/DR → **SheetVelero owns it**,
others reference; alerting → **Sheetheus rules fire → Sheetfana/AIOps notify** (one path).

---

## B. Foundation roadmap

**Must-have (the apparatus that makes it an ecosystem):**
- **Project maturity ladder** — Sandbox → Incubating → Graduated with *written* promotion criteria
  (adopters, committer diversity, cadence, security self-assessment). Reconcile with the 🏛️/💾/📝
  tiers on the landscape (define them).
- **TAGs** — TAG-Formulas, TAG-Storage-and-Data, TAG-Security, TAG-Observability, TAG-DevRel — a layer
  beneath the TOC to shepherd projects/reviews.
- **Security as a program** — Product Security Committee, coordinated disclosure/embargo, advisories,
  a "SheetVE" ID scheme, and a joint security self-assessment as a graduation gate (beyond SECURITY.md).
- **Code of Conduct enforcement** — a CoC Committee, `conduct@` inbox, published response timeline.
- **End-user community** — an End-User TAG/council + case studies + adopter intake (distinct from the wall).
- **Contributor onboarding** — New Contributor Guide, office hours, `good-first-issue` sweep, a quickstart
  ("run a node in 5 minutes"), a `#new-contributors` channel.
- **Meeting cadence** — a public calendar: recurring TOC/TAG/project meetings + notes/recordings.
- **TOC legitimacy** — defined seats, elections, term limits.

**Nice-to-have (growth / DevRel):**
- Mentorship — "Summer of Sheets" (GSoC/Outreachy parody, stipend-in-cells).
- Membership tiers (parody) — Platinum/Gold/Silver priced in named-range quota; a members wall.
- Events beyond SheetCon — regional "SheetDays", virtual meetups, co-located Day-Zero, a project pavilion.
- DevRel — a monthly "Sheet-Native Weekly" newsletter; an ambassador content pipeline; a CFP kit.
- i18n/localization SIG (the locale-number-format and RTL-sheet jokes write themselves).
- Swag store + press/brand kit + sticker packs.
- Annual community survey + transparency/funding reports (feed the Annual Report with real signal).
- Docs/tutorial site (not just "a blog") — getting-started + dev-loop.
- Secure the SNCF / Sheeternetes / SheetStorm names + domains.
- **Portability note:** a CNCF anti-lock-in parody that runs entirely on Google Apps Script needs a
  stated escape hatch (Excel/OpenOffice/LibreOffice editions) — own the irony.

---

## C. Certification roadmap

**Fix the load-bearing hole first — exam integrity.** Today "file an issue → Action mints a serial"
means answers sit in the public thread and nothing grades them. Required:
- **Autograder** that scores submissions against an answer key held as an encrypted repo secret /
  private repo — correct answers never appear in the public issue.
- **Per-candidate question set** seeded from the GitHub username (cribbing a neighbour's open issue fails).
- **Identity binding + dedup** — issuer = credential subject; one serial per (person, program); reject sockpuppets.
- **Revocation** — a `revoked.yaml` tombstone the verifier + badge consult (cheated/rescinded → red, not forever).
- **Rate-limit** — one attempt per identity per N days + cooldown after a fail (keep $0, kill spam).

**Levels & mechanics:**
- **Fundamentals → Professional → Expert** per program. **Pro is performance-based** — submit a Sheet/manifest
  URL an Action actually `skctl apply`s and checks for convergence (CKA-style). **Expert = a design RFC PR**
  needing 2 TOC approvals + a live defence on the community call; the merged PR is the artifact.
- **Ranks:** SheetCadet(1) / SheetAstronaut(3) / SheetCommander(6) / SheetAdmiral(10). **Rename the all-flagship
  capstone** (e.g. `Sheetstronaut`, distinct from SheetAstronaut-at-3 and from honorary SheetLegend). Scope
  "all programs" to a **named flagship subset** — 20 programs × 3 tiers is unachievable by design.
- **Specializations** — auto-minted `SPEC…` serials when a track's set is covered (Networking = Sheetlium+Sheetmesh;
  Observability = Sheetheus+Sheetfana+SheetAIOps; Platform = SheetyStack+SheetOS+Sheetstor).
- **Trail Map** — one ordered poster from SheetCadet → capstone (biggest "feels real" lever).
- **Dynamic badge** — a shields.io endpoint pointed at the registry JSON (live valid/expired/revoked + count-rank).
- **Org/team cert** — an `ORG…` badge showing % of members covered, verified against the registry.
- **Recert** — Pro/Expert validity bound to a Sheets-API major version; auto-expire on the next platform bump.
- **Ecosystem** — an "Authorized Sheet-Native Training Partner" + "Certified Service Provider" directory ($0).

---

## D. Missing projects (the gaps)

Deduped from the draft: dropped SheetStats (it's foundation DevStats / issue #41); merged
Crossplane+Terraform and Knative+KEDA; demoted SheetLB; renamed several for better puns.

**Core 15 (ranked by how iconic the analog is):**
1. **Shelm** — Helm/Kustomize packaging (install "charts" of manifests). *Most-expected missing tool.*
2. **SheetVault** — HashiCorp Vault, an external secret store (distinct from the native Secret).
3. **SheetLoki** — Loki/Fluentd log aggregation → a Logs tab.
4. **Sheeger** — Jaeger/OpenTelemetry tracing (completes the observability trio).
5. **SheetGate** — nginx-ingress / Gateway API (L7; coordinate with Sheetlium).
6. **Sheetcert** — cert-manager (issue/rotate the "TLS" the mesh pretends to use).
7. **Gatesheeper** — OPA/Kyverno policy + admission control (rules as formulas).
8. **SheetVelero** — Velero backup/restore (owns DR; export/import).
9. **Sheetka** — Kafka/NATS streaming (an append-only tab is a perfect broker).
10. **Sheetstage** — Backstage developer portal (a service catalog over the org).
11. **SheetDNS** — CoreDNS / external-dns (service discovery).
12. **SheetFalco** — Falco runtime security (watch the Events tab for suspicious rows).
13. **SheetKnative** — Knative + KEDA serverless / scale-to-zero (pairs with SheetAssembly; the name-vs-"Sheet-Native" gag is a bonus).
14. **SheetCrossplane** — Crossplane declarative cloud provisioning (Terraform/OpenTofu as external-IaC alt).
15. **SheetSign** — cosign/sigstore signing via a signature/HMAC column (ties to supply-chain policy).

**Breadth (below the cut):** SheetChaos (delete a random Pod row — most buildable joke), SheetLB (MetalLB).

**8 iconic categories the draft missed entirely (ranked):**
1. **Sheetkton** — Argo Workflows / Tekton pipelines (DAG steps as rows). *Biggest omission — arguably top-5 overall.*
2. **Sheetcd** — etcd, the control-plane store (meta-joke: the Sheet *is* the store).
3. **Sheetgres** — CloudNativePG / Vitess DB operator (a database that literally is a spreadsheet).
4. **Sheetd** — containerd/CRI-O runtime (the layer that actually executes a Pod row).
5. **SheetFlag** — OpenFeature/flagd feature flags (a boolean cell *is* a flag).
6. **Sheedis** — Redis/Valkey caching (a named-range in-memory cache).
7. **SheetRollout** — Argo Rollouts/Flagger progressive delivery (canary by % of rows).
8. **vSheet** — vcluster/Capsule multi-tenancy (a tab-in-a-tab virtual cluster).

**Honorable mentions:** SPIFFE/SPIRE identity, a schema registry (pairs with Sheetka), Karpenter node
autoscaling, Kepler sustainability ($0 energy gag), Gitpod-style dev environments.

**Minimum-viable-platform gap-fill order:** the observability trio (SheetLoki + Sheeger on top of the
existing Sheetheus/Sheetfana), then packaging (Shelm) + secrets (SheetVault) + ingress (SheetGate);
the most glaring *iconic* omissions are pipelines (Sheetkton), etcd (Sheetcd), and a DB operator (Sheetgres).

---

## Cross-cutting: the contracts everything depends on

These aren't projects; they're the specs the whole foundation reads and writes. Own and version them.
- **Sheet-Native Resource Model** — the manifest schema + versioning (the API of record).
- **Metrics exposition tab schema** — the `/metrics` contract (Sheetheus).
- **API/verb spec** — the skctl contract all clients target + a conformance suite.
- **Known-limits & scalability doc** — behaviour at the 10M-cell / 50k-char / quota walls.
- **Version-skew / release-train policy** — a compatibility matrix across ~20 independently-shipping projects.

---

*Reviewed by five independent teammates (per-project, foundation, certification, missing-projects,
holistic). Their corrections — exam integrity, the metrics-schema keystone, the HPA/Secrets/WASM/DRBD
reality checks, the ownership de-duplication, and the eight missing iconic projects — are folded in above.*
