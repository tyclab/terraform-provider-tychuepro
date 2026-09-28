# CLAUDE.md — tychue

An OpenTofu provider for Philips Hue, Bridge Pro first, several bridges at once; a fork of
akr4/terraform-provider-hue that we own.

- Read [HANDOVER.md](HANDOVER.md) (state, verified facts, rules) and [PLAN.md](PLAN.md)
  (decisions, milestones) before changing anything.
- tofu, never terraform, in everything we write; `terraform-provider-tychue` is the registry's
  naming exception.
- No secrets, bridge ids, addresses or personal names anywhere in the repository; fixtures use
  synthetic values. Never copy Signify's login-walled API reference.
- Test against `internal/fakebridge`; real-bridge steps are commands for the operator to run.
