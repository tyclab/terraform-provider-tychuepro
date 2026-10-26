# tychuepro — plan

A staging fork of [akr4/terraform-provider-hue](https://github.com/akr4/terraform-provider-hue)
(MIT) for pull requests to upstream: the Hue Bridge Pro resources and light power-up. The home's
two bridges are managed with upstream's provider from the home's private infrastructure
repository; nothing there depends on this fork.

## Why upstream, not a renamed fork

- Upstream v0.2.0 (2026-10-20) already covers rooms, zones, scenes, smart scenes, behaviour
  instances and devices, with import blocks (`hue-tf import-blocks`), config generation and an
  empty-plan check. Drift detection is plain `tofu plan -detailed-exitcode`.
- A renamed fork would inherit none of upstream's monthly work without a rebase of every change.
- Signify's announced Backup & Restore is manual, cloud-held and restores onto a Bridge Pro only, so
  configuration in git is the only backup a Bridge v2 gets.

## Decisions (2026-10-26)

1. **Track upstream.** No rename, no own releases or registry listing. Each change is a branch off
   upstream `main`, offered as an upstream pull request; nothing lands on this fork's `main` but
   these docs.
2. **OpenTofu** in everything we write, examples and tests included.
3. **Schema truth** is the bridges' own responses plus aiohue's models. Never copy text or schemas
   from Signify's login-walled reference: its terms forbid derivative works.
4. **Secrets only at runtime**, from environment variables; never in files, examples or state.
5. **No hidden activation**: creating a smart scene activates it, so the provider says so in the
   plan.

## Upstream pull requests

| #   | Change               | Done when                                                                                                                                                                                                   |
| --- | -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| U1  | Bridge Pro resources | `motion_area_configuration` (name, group, participants, enabled), `convenience_area_motion` / `security_area_motion` (enabled, sensitivity) and `switch_input_configuration`, with fake-bridge tests for create, update, delete, import, merged upstream. |
| U2  | Light power-up       | The light's `powerup` (preset, on, dimming, color) settable, merged upstream.                                                                                                                              |

## Not in scope

Entertainment areas (the home's streaming client creates and positions its own), pairing lights
and devices, runtime light control, legacy v1 rules, the cloud remote API.

## Risks

- Bridge Pro firmware ships about monthly and renames fields (`effects` → `effects_v2`,
  `device_mode` → `switch_input_configuration`).
- The bridge answers 429 above about three concurrent requests; there is no dry run for creation.
- "Philips Hue" is Signify's trademark: descriptive use only, no logos.

## Open questions

- Status of v1 rules on the Bridge Pro.
- Whether upstream will list on registry.opentofu.org; until then `tofu` installs it from
  registry.terraform.io.
