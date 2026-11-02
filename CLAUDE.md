# CLAUDE.md — tychuepro

A staging fork of akr4/terraform-provider-hue for upstream pull requests: the Bridge Pro
resources and light power-up.

- Read [HANDOVER.md](HANDOVER.md) (state, verified facts, rules) and [PLAN.md](PLAN.md)
  (decisions, milestones) before changing anything.
- tofu, never terraform, in everything we write.
- No secrets, bridge ids, addresses or personal names anywhere in the repository; fixtures use
  synthetic values. Never copy Signify's login-walled API reference.
- Test against `internal/fakebridge`; real-bridge steps are commands for the operator to run.
