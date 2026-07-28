- Build: pin shipit to v1.6.0 and reconcile the managed set. The previous pin (v1.4.1)
  emitted a `[feature.shipit-lexd.target.win-64]` table for a platform this workspace
  does not declare, so every `pixi` invocation warned `target selector 'win-64' does
  not match any of the platforms supported by the workspace`; shipit#1072 now
  intersects the channel's served set with the repo's own declared platforms, and the
  warning is gone. The conda packager (`rattler-build`) moves to its own
  endpoint-gated block, the managed `lint` task moves into the lint feature's env, and
  the fundamental agent skills move from `.shipit-skills/` to `.agents/skills/` with
  `.claude/skills` as a symlink (lex#851)
- Build: drop the retired cross-repo cascade. The `notify-downstreams` declaration and
  the `on-upstream-released` cascade receiver fired consumer releases that consumed no
  new version of anything — a cross-repo dependency is now a plain conda package whose
  version lives only in the consumer's own `pixi.toml`. The `copilot-review` workflow
  is also removed: the PR state engine is the sole requester of reviewers (lex#851)
