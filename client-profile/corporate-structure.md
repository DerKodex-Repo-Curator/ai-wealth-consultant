# Corporate structure

> **Status**: TEMPLATE — populated by `/onboard` if the user runs companies. Skip if none.

## Summary

- **Entities owned by the user** (direct or indirect): <list>
- **Stack pattern**: <e.g. flat / parallel-holding-and-OpCo / consolidated-holding / family-partnership / etc.>
- **Family partners** (if any KG / Familiengesellschaft): <names + percentages>

## Per-entity detail

Replicate this block for every entity:

### <Entity name>

- **Legal form**: <GmbH / AG / GmbH & Co. KG / Ltd / LLC / etc.>
- **Jurisdiction of incorporation**: <DE / UK / other>
- **Registry**: <HRB number / Companies House number / etc.>
- **Address**: <registered office>
- **Stammkapital / share capital**: <amount>
- **Ownership**: <who owns what %>
- **Geschäftsführer / directors**: <names>
- **Tax number(s)**: <Finanzamt + Steuernummer + USt-IdNr / Companies House + UTR>
- **Fiscal year**: <calendar / other>
- **Notes**: <Organschaft, profit-transfer agreements, holding privileges in play, etc.>

---

## Structural diagnostics (Felix's beat)

Felix flags sub-optimal stack patterns. If parallel holding + operating partnership are detected, restructuring candidates should be noted here once Felix has been consulted:

- **Current diagnostic**: <e.g. "parallel Holding GmbH and GmbH & Co. KG — KG income flows direct to personal ESt rates rather than being shielded by § 8b KStG">
- **Open restructuring candidates**: <A/B/C list — see `corporate-structuring-specialist` agent>
- **Sperrfristen in play**: <UmwStG 7-year blocking periods, etc.>

---

`verified: YYYY-MM-DD`
