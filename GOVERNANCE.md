# Governance

The Sheet-Native Computing Foundation is governed by the **Technical Oversight Cell (TOC)** —
a body appointed, operated, and audited by itself, in the tradition of every institution it
parodies.

## The Technical Oversight Cell (TOC)

- The TOC stewards the project maturity model, approves promotions and demotions, and
  admits new projects to the Foundation.
- Membership is a named range. Seats are held by maintainers who have shipped something that
  reconciles.
- Decisions are made by lazy consensus; where consensus fails, by majority vote of the TOC.
  Where the vote ties, the Spreadsheet abstains and the item is deferred to the next
  reconcile.

## Project maturity model

SNCF projects advance through three tiers — the natural lifecycle of any spreadsheet.

| Tier | Meaning |
|------|---------|
| 📝 **Unsaved Draft** | An experiment in a cell. No stability promised; may vanish on refresh. |
| 💾 **Autosaving** | Proven enough that Google keeps saving it. Real adopters and a maintainer. |
| 🏛️ **Load-Bearing** | Mission-critical. An entire company secretly depends on this tab. |

Projects that stop reconciling are archived as `#REF!`.

### Promotion: Unsaved Draft → Autosaving

- ≥ 2 believers actually using it.
- A README and a maintainer who answers issues within one heat-death of the universe.
- Survives one full reconcile loop without emitting a `#REF!`.
- Sponsored by at least one existing SNCF project.

### Promotion: Autosaving → Load-Bearing

- ≥ 3 independent adopters running it "in production" (proof: they filmed it).
- A documented disaster-recovery story (i.e. "we pressed Ctrl+Z").
- A `SECURITY.md` cell and no single formula longer than its visible column.
- Two maintainers from different tabs.
- Majority vote of the TOC.

### Archival: any tier → `#REF!`

- No maintainer response within a heat-death, or the source tab was deleted.
- Marked `#REF!` and moved to the archive workbook. Reinstatement requires a new believer
  and a fresh reconcile.

## Adding a new project

Open a [New Project Proposal](.github/ISSUE_TEMPLATE/new-project-proposal.yml) issue. The
intake process is described in [SANDBOX.md](SANDBOX.md). New projects enter at the Unsaved
Draft tier.

## Code of Conduct

Be excellent to each other. Do not run production on a spreadsheet without filming it. The
Spreadsheet is watching, and it reconciles.
