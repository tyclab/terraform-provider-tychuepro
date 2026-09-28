# Handover — for the session that drives tychuepro

Written 2026-09-29 by the planning session. Read [PLAN.md](PLAN.md) first; this file is the
state, the facts behind the plan, and how to work here.

## State

- `tyclab/terraform-provider-tychuepro` is a GitHub fork of `akr4/terraform-provider-hue` at
  `f8e17db` (2026-09-26). Only `PLAN.md`, `HANDOVER.md` and `CLAUDE.md` are ours; every other
  file is still akr4's, including the names (`terraform-provider-hue`, `hue-tf`,
  `registry.terraform.io/akr4/hue`).
- Nothing has been built, renamed or tested here yet. Start with M0.

## Your environment

- A cloud session has no access to the operator's network: no bridge, no Home Assistant, no
  secrets. Build and test against `internal/fakebridge`.
- Whatever needs a real bridge becomes an exact command for the operator, with what it should
  print; ask for its output. Real-bridge runs go to the spare Bridge v2 first, the Bridge Pro
  (production) only after that.

## Verified facts

Read on 2026-09-28 from the operator's Bridge Pro through Home Assistant's Hue diagnostics.

- Model BSB003, software 1.78.2071476020.
- The 32 resource types and their counts: behavior_instance 20, behavior_script 14, bridge 1,
  bridge_home 1, button 34, clip 1, convenience_area_motion 1, device 59, device_power 16,
  device_software_update 56, entertainment 41, entertainment_configuration 7,
  geofence_client 3, geolocation 1, grouped_light 11, grouped_light_level 4, grouped_motion 4,
  light 42, light_level 3, matter 1, matter_fabric 1, motion 3, motion_area_candidate 38,
  motion_area_configuration 1, room 5, scene 106, security_area_motion 1,
  switch_input_configuration 1, temperature 3, zigbee_connectivity 59,
  zigbee_device_discovery 1, zone 5.
- Behaviour instances: 13 switch and button bindings, 6 "state after streaming", 1 disabled
  "Go to sleep". None reacts to motion.
- `motion_area_configuration`: one area, 4 participants, `enabled` false, health `not_running`.
  `convenience_area_motion` enabled, sensitivity 2 of 4; `security_area_motion` disabled.
- `entertainment_configuration`: 7 areas of 3 to 16 channels, types screen, monitor and music;
  the bridge's entertainment proxy reports `max_streams` 1.

From the research (sources below):

- Signify's CLIP v2 reference, migration guide and SDK pages need a developer login; there is no
  official OpenAPI spec. The terms forbid republishing or deriving works from the API content.
- MotionAware has been documented since 2025-09-04 and exists only in API v2. Plain HTTP is gone
  from firmware released after 2025-08-01; bridges present certificates under either the older
  root or "Hue Root CA 01" (valid 2025–2050).
- akr4's own coverage reading of the reference: full create/update/delete for room, zone, scene,
  smart_scene, behavior_instance, motion_area_configuration, entertainment_configuration and
  service_group; settable fields only for convenience/security area motion,
  switch_input_configuration, geolocation and light power-up; read-only behavior_script.
- aiohue (home-assistant-libs) checked its models against a live Bridge Pro in PR #657
  (July 2026); its motion-area update covers name and enabled.
- The OpenTofu registry expects the repository `NAMESPACE/terraform-provider-NAME`.

## Start here

1. M0 from PLAN.md, one pull request per step: rename (module path
   `github.com/tyclab/terraform-provider-tychuepro`, provider type `tychuepro`, binaries, docs), make the
   acceptance tests run `tofu`, add GitHub Actions, rewrite the README in English with akr4
   credited and the MIT licence kept.
2. Start a `CHANGELOG.md` with the rename.
3. Then M1 and M2. For the Bridge Pro resources, write fake-bridge fixtures with synthetic ids and
   values that follow the shapes above, and ask the operator for a values-free shape dump of any
   resource you need.

## Rules

- tofu, never terraform, in everything we write; the repository name is the registry's exception.
- No secrets, bridge ids, addresses, room or device names, or personal names in commits, fixtures,
  examples or issues. Fixtures use synthetic values.
- Never paste text or schemas from Signify's reference.
- Conventional commit messages (`feat:`, `fix:`, `docs:`, `chore:`). Comments say why; about one
  comment line per five lines of code.
- Don't push to upstream. Offer generic fixes to akr4 as pull requests from branches of this fork.

## Ask the operator about

The open questions in PLAN.md, branch protection and who reviews pull requests, and any
real-bridge run.

## Sources

- https://developers.meethue.com/ (portal; reference pages need a login) and
  https://developers.meethue.com/terms-of-use-and-conditions/
- https://www.philips-hue.com/en-us/support/release-notes/bridge-pro
- https://github.com/akr4/terraform-provider-hue (docs/api-coverage.md)
- https://github.com/home-assistant-libs/aiohue (PR #657)
- https://github.com/openhue/openhue-api
- https://github.com/opentofu/registry/blob/main/PROCEDURES.md
- https://hueblog.com/2026/09/03/backup-restore-all-the-details-on-the-long-awaited-feature/
