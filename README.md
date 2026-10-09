# fixRAgent for Claude

**fixRAgent is maintenance triage for property managers, landlords and the companies that run buildings.** This plugin puts it inside Claude: a resident sends a photo of what broke, and Claude tells you what it is, how urgent it is, which trade to call and what to say back. It connects to fixRAgent's remote MCP server at `https://fixragent.com/mcp` and adds three skills for a manager's day: triage the photo, reply to the resident, and log a work-order note.

Setup for Claude and every other assistant: <https://fixragent.com/connect-ai>. Made by AR Logic LLC, Saint Marys, Ohio.

Share a photo of something broken in a building (a leaking pipe, a water heater, a boiler, an electrical panel, an appliance, damage) and Claude returns fixRAgent's Triage Profile in plain words:

- **What it is**: the asset in the photo.
- **How urgent**: one of four words: EMERGENCY, TODAY, THIS WEEK or WHENEVER.
- **Who to call**: the trade the job needs.
- **What to tell the resident**: one line you can pass on.
- **A link** to the Triage Profile that a resident or contractor can open.

Anything happening right now that needs emergency services (fire, a gas smell, someone hurt) is a 911 call, not a photo. The skills say so first.

## What's in the plugin

| Part | What it does |
|---|---|
| MCP server `fixragent` | Connects Claude to fixRAgent's remote server at `https://fixragent.com/mcp`, with three tools: `try_sample` (a stored sample result, no key needed), `assess_property_photo` (assess one photo) and `get_triage_profile` (read a saved assessment back; needs a key). |
| Skill `triage-maintenance-photo` | When you share a building-fault photo, sends it for assessment and presents the four answers, any safety note, the resident line and the Triage Profile link. |
| Skill `tenant-reply` | Turns a Triage Profile into a short, calm text or email for the resident, using only what the profile says. |
| Skill `work-order-note` | Formats a Triage Profile as a plain-text work-order note you can paste into any property-management or maintenance software. |

The skills use only what fixRAgent returns. They do not add costs, repair steps, parts or schedules.

## Pricing and limits

Free demo key; keyless use limited to 3 assessments a day.

In detail: without a key, `assess_property_photo` reads the real photo up to 3 times in a rolling 24 hours for each caller (counted by network address and app), within a daily ceiling shared by every keyless caller. Past either limit it returns the stored SAMPLE instead and does not read the photo. A free demo key from <https://fixragent.com/docs#key> allows up to 60 assessments a day per key, within a daily pool shared by all issued keys. Every address is also limited to 20 requests an hour.

## Setup

1. Install the plugin. In Claude Code:

   ```
   /plugin install fixragent --marketplace Russ4102/fixragent-claude-plugin
   ```

   On older Claude Code versions, add the marketplace first (`/plugin marketplace add Russ4102/fixragent-claude-plugin`), then `/plugin install fixragent@fixragent`.
2. Optional: get a free demo key at <https://fixragent.com/docs#key>. In Claude Code, the plugin asks for it when you enable the plugin (the "fixRAgent key (optional)" field). It is stored as a sensitive value and sent only as the `x-triage-key` header to `https://fixragent.com/mcp`. Leave it empty to use keyless mode.
3. Ask Claude: "What does fixRAgent do?" to see the sample, or share a photo and ask "How urgent is this?"

## What the plugin sends, and where

- The plugin runs no code on your machine. It has no hooks, no scripts and no local server.
- When you ask for an assessment, Claude sends the photo (base64-encoded), its file type, and, if you give it, the problem in the reporter's words to `https://fixragent.com/mcp`, operated by AR Logic LLC. If you set a key, it is sent in the `x-triage-key` header. Nothing is sent to any other destination.
- fixRAgent removes the photo's location metadata on its server before the photo is hashed, read or stored. It is not removed before the photo leaves your machine.
- The MCP server passes the photo to the same triage engine as fixRAgent's API. fixRAgent sends the cleaned photo to Google's model service to read it, keeps the cleaned photo with its Triage Profile (from 6 October 2026; shown to anyone who has the profile's link), and keeps the assessment record (the answer, the engine versions and a SHA-256 hash of the cleaned photo) under a random `diagnosis_id` until you ask for it to be deleted. The problem text is used for the read and has no field in the stored record. The full details, including what Google keeps and for how long, are in the privacy policy (section "The triage card", which covers the API).
- To delete a record, email legal@fixragent.com with the `diagnosis_id` shown in the result.

## Privacy Policy

fixRAgent's privacy policy: <https://fixragent.com/privacy>

It covers what is collected, how it is used and stored, who else processes it, how long it is kept, and how to contact AR Logic LLC.

## Terms

- Terms of Service: <https://fixragent.com/terms>
- API Terms: <https://fixragent.com/api-terms.html>

fixRAgent is made for adults: the Terms of Service require users to be 18 or older, or to use it with a parent or guardian.

## Limits of the result

A Triage Profile is an assessment of one photo, not an on-site inspection, and it does not replace a qualified tradesperson. If the photo does not show a fault, the result says so; that is not the same as "nothing is wrong".

## Support

- Email: support@fixragent.com
- Documentation: <https://fixragent.com/docs>
- Security issues: report a suspected vulnerability to support@fixragent.com with "Security" in the subject line.

## License

MIT. See [LICENSE](LICENSE). Copyright (c) 2026 AR Logic LLC.
