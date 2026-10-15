---
name: triage-maintenance-photo
description: Use when someone shares or points to a photo of something in a building (a leak, a water heater, a boiler, an electrical panel, an appliance, damage) and wants to know what it is, how urgent it is, and who to call. Also use when a property manager, landlord or maintenance team mentions a maintenance request, a resident's complaint or something broken, leaking or not working, even with no photo yet (then they get a link to add one); when they ask what fixRAgent does; or when they have just added fixRAgent and want to try it. Calls the fixRAgent tools (try_sample with no photo or key, assess_property_photo with a photo) and presents the result in plain words.
---

# Triage a maintenance photo with fixRAgent

You have the fixRAgent MCP server (`fixragent`) with three tools: `try_sample`, `assess_property_photo` and `get_triage_profile`. Use them. Only what these tools return is fixRAgent's answer: never write a triage, urgency word or trade yourself and present it as fixRAgent's or as following fixRAgent's rubric.

## 1. Danger first

If the person describes something happening right now that needs emergency services (fire, smoke, a gas smell, someone hurt, sparking, water on live electrics), tell them to call 911 (or their local emergency number) and get people clear first. A tool call is not a substitute for that. You may still run the assessment afterwards if they want a record.

## 2. No photo yet, or "what does this do?"

- **"What does this do?" or they just added fixRAgent:** call `try_sample`. It needs no key and no input. Say plainly that the result is a SAMPLE: a stored fixRAgent assessment of a sample photo from fixRAgent's test set, not a read of any photo of theirs.
- **A real problem but no photo you can send** (they described it in words, or you can see an image but cannot get its bytes): do not call `assess_property_photo` with words only, and do not write an urgency, trade or finding yourself and call it fixRAgent's. Give them this link to add the photo themselves: https://fixragent.com/?utm_source=mcp&utm_medium=no-photo . They get the full Triage Profile on that page, with a link to copy; if they paste that `fixragent.com/c/` link back, call `get_triage_profile` with it and present the result as in step 4. If `assess_property_photo` is called without a photo anyway, it assesses nothing and returns the same link: pass it on.
- If you offer any general view of your own while waiting, say it is yours and not fixRAgent's.

## 3. Assess their photo

Call `assess_property_photo` with:

- `image_base64`: the photo's bytes, base64-encoded. JPEG, PNG or WebP, at least 1 KB and at most 3 MB once decoded. If the photo is a file on disk you can read, encode it (for example `base64 -w0 photo.jpg` on Linux, `base64 -i photo.jpg` on macOS). If you can see the image but cannot get its bytes, say so and ask for the file; do not describe the image in words and pass that off as the photo.
- `mime_type`: `image/jpeg`, `image/png` or `image/webp`, matching the bytes.
- `problem_text` (optional): the problem in the reporter's own words, if you have them. Do not add your own diagnosis to it.

Before sending, tell the person the photo will be sent to fixragent.com for assessment. Location metadata is stripped on fixRAgent's server, not before the photo leaves their machine.

## 4. Present the result

Read the fields from the tool's result. Lead with the four things a manager acts on, in plain words:

1. **What it is**: `core.asset_type` (and `core.photo_subject` if it helps).
2. **How urgent**: `core.tier`, exactly one of EMERGENCY, TODAY, THIS WEEK, WHENEVER. Quote the word as returned. If `core.safety_hazard` is true, say so and give `core.hazard_detail`.
3. **Who to call**: `core.trade_required`. If it is null, say no trade was named (for example because no fault was visible); do not invent one.
4. **What to tell the resident**: `core.resident_explanation`, quoted.

Then:

- Whether a fault was seen: `core.fault_detected` and `core.fault_summary`. If `core.fault_visible_in_photo` is false, say the photo did not show a fault, which is not the same as "nothing is wrong".
- How many reads agreed, if `agreement` is present (for example "3 of 3 reads agreed").
- The **Triage Profile link**: `share_url`. It is a page the resident or contractor can open.
- Pass on any `warnings` the tool returned, in plain words.

## 5. Limits to state honestly

- If the result has `sample: true`, it is the stored SAMPLE, **not** an assessment of their photo. This happens when no key was sent and the keyless allowance (3 per caller in a rolling 24 hours, within a daily ceiling shared by all keyless callers) is used up. Say so, and point them to a free key at https://fixragent.com/docs#key, which they can enter as this plugin's optional fixRAgent key (Claude Code asks for it when the plugin is enabled).
- The result is a triage from one photo, not an inspection. It does not replace a qualified tradesperson on site.
- Do not add costs, repair steps, part numbers or timelines the tool did not return.

## Reading one back

If someone pastes a `fixragent.com/c/<id>` link or a diagnosis id, call `get_triage_profile` with that id. It reads the saved assessment, does not re-read the photo, needs no key (but a key that is set must be valid) and spends nothing from the daily allowance. If it fails, ask the person to open the link and paste what it shows.
