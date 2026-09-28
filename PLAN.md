# tychuepro — plan

Light as code for Philips Hue: an OpenTofu provider that declares a Hue Bridge's configuration in
git and applies it, Bridge Pro first, several bridges at once. Forked from
[akr4/terraform-provider-hue](https://github.com/akr4/terraform-provider-hue) (MIT) at `f8e17db`
(2026-09-26) and owned here. Generic fixes can flow back upstream; upstream's roadmap never limits
this one.

## Why

- A home with a Hue Bridge Pro, ~42 lights, 5 rooms, 5 zones, 106 scenes, 20 app automations,
  7 entertainment areas and a MotionAware area keeps all of that only on the bridge. The Hue app
  and third-party apps edit the same objects, and nothing records who changed what.
- Home Assistant drives lights at runtime but does not manage the bridge's configuration.
- Signify offers no configuration-as-code tool and no machine-readable API spec; the CLIP v2
  reference sits behind a developer login. Its announced Backup & Restore is manual, cloud-only
  and has no API.
- Open-source support for the Bridge Pro is rare: only aiohue has models checked against a live
  Pro (PR #657, July 2026); openhue-api's MotionAware schema does not match the Pro; the provider
  this fork starts from was tested against a fake bridge.

## Decisions (2026-09-28)

1. **OpenTofu only.** Docs, examples, CI and tests use `tofu`. The repository is named
   `terraform-provider-tychuepro` only because the OpenTofu registry expects
   `NAMESPACE/terraform-provider-NAME`
   ([PROCEDURES.md](https://github.com/opentofu/registry/blob/main/PROCEDURES.md)). Provider source
   `tyclab/tychuepro`, resources `tychuepro_*`.
2. **Fork and own.** Module path, provider address, binaries and docs move to tychuepro.
3. **Bridge Pro first.** A plain Bridge v2 is supported where the API is the same; a spare
   Bridge v2 is the risk-free test target.
4. **Multi-bridge.** One provider instance (alias) per bridge, identified by its bridge id, which
   configure checks, not by its address.
5. **Schema truth** is the bridges' own responses plus aiohue's models. Never copy text or schemas
   from Signify's login-walled reference: its terms forbid derivative works.
6. **Secrets only at runtime**, from environment variables; never in files, examples or state.
7. **Safe applies.** Refuse to apply while any entertainment area streams (a bridge streams one
   area at a time, `max_streams` 1). No hidden activation: creating a smart scene activates it, so
   the provider says so in the plan.

## Milestones

Each lands as reviewed pull requests into `main`.

| #   | Milestone                  | Done when                                                                                                                                                                                                                                 |
| --- | -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| M0  | Fork hygiene               | Module, provider type, binaries and docs renamed to tychuepro; tofu everywhere (acceptance tests run the `tofu` binary); GitHub Actions build, vet, lint, unit and fake-bridge acceptance tests green; README rewritten with akr4 credited. |
| M1  | Behaviour instances, drift | Existing behaviour instances import, `enabled` and `name` are enforced, and a plan-only drift mode (`tofu plan -detailed-exitcode`) exists. An import of a real bridge gives an empty plan.                                               |
| M2  | Bridge Pro resources       | `motion_area_configuration` (name, group, participants, enabled), `convenience_area_motion` / `security_area_motion` (enabled, sensitivity) and `switch_input_configuration`, with fake-bridge tests for create, update, delete, import. |
| M3  | Entertainment              | `entertainment_configuration` with members and channels whose ids and positions round-trip unchanged; the streaming guard refuses an apply during a stream.                                                                               |
| M4  | Multi-bridge party         | Two bridges in one configuration, one party area per bridge, and a documented way for a streaming client (e.g. LedFx) to drive both at once.                                                                                             |
| M5  | Release                    | goreleaser with a signing key, GitHub releases, OpenTofu registry listing, examples with synthetic data only.                                                                                                                             |

## Requirements from the first home

- A LedFx party mode streams to one 16-channel music area. Channel ids and positions must stay
  stable: renumbering silently remaps the effect. Area names are load-bearing: Home Assistant
  selects Sync Box areas by name. The "state after streaming" behaviours on those lamps must match
  the party's own restore. Rotating the application key means re-registering LedFx.
- Home Assistant reads MotionAware areas as binary sensors; the one area is disabled today.
- Two bridges would allow two entertainment streams at once (Sync Box on one, LedFx on the other).

## Not in scope

Pairing lights and devices (stays in the Hue app), runtime light control, legacy v1 rules (revisit
only if needed), the cloud remote API.

## Risks

- Bridge Pro firmware ships about monthly (16 releases since September 2025) and renames fields
  (`effects` → `effects_v2`, `device_mode` → `switch_input_configuration`). The drift gate is the
  alarm.
- The Hue app edits the same objects: each resource needs a clear owner, git or the app.
- The bridge answers 429 above ~3 concurrent requests; there is no dry run for creation.
- The base is one author's three-week-old project.
- "Philips Hue" is Signify's trademark: descriptive use only, no logos.

## Open questions

- Which objects git owns and which the app keeps (the 106 scenes especially).
- The Bridge Pro's light limit per entertainment area.
- Status of v1 rules on the Bridge Pro.
- Which "state after streaming" behaviours cover the music-area lamps.
- The CLI's name (akr4 ships `hue-tf`).
