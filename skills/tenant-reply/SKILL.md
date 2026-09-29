---
name: tenant-reply
description: Use when a property manager or landlord wants a short text or email to send a tenant or resident about a maintenance problem that fixRAgent has triaged. Turns a fixRAgent Triage Profile (from assess_property_photo, get_triage_profile or a fixragent.com/c/ link) into a plain, calm message using only what the profile says.
---

# Write a tenant reply from a fixRAgent Triage Profile

## Get the profile

- If a fixRAgent result is already in this conversation, use it.
- If the person gives a `fixragent.com/c/<id>` link or a diagnosis id, call `get_triage_profile` with the id. That read needs a fixRAgent key; if it returns an error saying no key was sent, ask the person to paste what the link shows instead.
- If there is only a photo, run the `triage-maintenance-photo` skill first.
- If the result has `sample: true`, stop: it is a sample, not an assessment of this tenant's problem. Say so and do not write a reply from it.

## Write the message

Build it from these fields only:

- `core.resident_explanation`: the core of the message. Keep its meaning; you may smooth the wording.
- `core.tier` (EMERGENCY, TODAY, THIS WEEK or WHENEVER): sets how the message opens. Do not promise a visit time; the manager decides that. Leave a clear placeholder such as `[visit time]` for them to fill in.
- `core.trade_required`: if the manager is sending someone, you may say which trade (for example "a plumber"). If it is null, do not name one.
- `core.safety_hazard` / `core.hazard_detail`: if true, put the safety instruction first and in plain words.
- `share_url`: include it only if the manager wants the tenant to see the Triage Profile.

Rules:

- For EMERGENCY, or anything involving fire, smoke, a gas smell, someone hurt or water on electrics, the first line tells the tenant to get clear and call 911 (or the local emergency number) if there is immediate danger.
- Keep a text under about 320 characters unless the manager asks for more. An email can be longer but stays short.
- No blame, no legal language, no costs, no repair instructions the profile did not give, and nothing about who pays.
- Sign with the manager's name or company if given; otherwise leave `[your name]`.

Offer the draft once, then ask whether they want it shorter, warmer or more formal.
