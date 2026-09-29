---
name: work-order-note
description: Use when a property manager or maintenance coordinator wants to log a fixRAgent triage as a work order, ticket or maintenance request note. Formats a fixRAgent Triage Profile as a plain-text note that can be pasted into any property-management or maintenance software, using only what the profile says.
---

# Format a fixRAgent result as a work-order note

## Get the profile

- Use a fixRAgent result already in the conversation, or call `get_triage_profile` with a diagnosis id or the id at the end of a `fixragent.com/c/<id>` link. That read needs a fixRAgent key; if it returns an error saying no key was sent, ask the person to paste what the link shows instead.
- If there is only a photo, run the `triage-maintenance-photo` skill first.
- If the result has `sample: true`, do not log it as a work order: it is a sample, not this property's problem. Say so.

## The note

Plain text, no Markdown tables, so it pastes cleanly into any system. Use this layout and leave out any line whose field is empty or null:

```
PRIORITY: <core.tier>
ASSET: <core.asset_type> (<core.asset_category>)
TRADE: <core.trade_required>
FAULT SEEN: <yes/no from core.fault_detected> - <core.fault_summary>
SAFETY: <core.hazard_detail, only if core.safety_hazard is true>
REPORTED AS: <problem text in the reporter's words, if given>
TELL RESIDENT: <core.resident_explanation>
TRIAGE PROFILE: <share_url>
FIXRAGENT ID: <diagnosis_id>
READS AGREED: <agreement.k> of <agreement.n>, if agreement is present
```

Rules:

- Copy `core.tier` exactly as returned (EMERGENCY, TODAY, THIS WEEK or WHENEVER). If the person's system uses its own priority names, ask how they map; do not guess.
- Add a unit, property or tenant name only if the person gives it. Never invent one.
- Do not add costs, parts, schedules or a vendor the profile did not name.
- If `core.fault_visible_in_photo` is false, keep "FAULT SEEN: no" and do not soften or strengthen it.
- End with the line: `Triage from one photo by fixRAgent. Not an on-site inspection.`

If the person names their software, you may adjust the field labels to match what they tell you it uses, but keep every value as returned.
