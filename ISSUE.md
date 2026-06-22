# Weaknesses remediation plan

## Remaining

1. Fix sender identity in runtime request flow (critical): in `request_public_key`, always sign requests with local `participant_id`, not remote peer ID; add a regression test for `sender_id`.
2. Add protected transport: enable TLS with certificate verification as a minimum; target mTLS for mutual authentication and encrypted channel security.
3. Harden trust model: introduce explicit participant allowlist (`participant_id` + cert/fingerprint binding) and reject announcements/keys from nodes outside allowlist even with valid `SID_TOKEN`.
4. Restrict discovery defaults: disable UDP broadcast by default (or gate it behind `--enable-broadcast`) and prefer explicit bootstrap/service discovery in production.
5. Replace custom HTTP parsing with battle-tested stack: migrate to `hyper/axum`-style runtime, enforce request body limits/timeouts/strict parsing via library primitives.
6. Close operational gaps: add CI workflow (`fmt`, `lint`, `test`, `build`, `e2e`), align `docs/man.md` version with `Cargo.toml`, and add dependency security scanning (`cargo-audit`, policy checks).

## Completed

- Move daemon control artifacts out of `/tmp`: store PID/stop/log files in `$XDG_RUNTIME_DIR/ssh-key-sync/<sid>/` (fallback `~/.local/run/...`), enforce `0700` directory and `0600` file permissions, and use safer file creation flags where applicable.

Suggested rollout:

- Phase 1 (immediate): remaining item 1
- Phase 2 (security release): items 2-4 from remaining list
- Phase 3 (stabilization): items 5-6 from remaining list
