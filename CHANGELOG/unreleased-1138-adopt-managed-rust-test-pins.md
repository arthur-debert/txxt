- Build: adopt the managed rust/test pins and reconcile onto shipit v1.7.0. Three
  consumer-owned entries in `pixi.toml` were shadow copies that made shipit skip the
  block that would have delivered them, with only a warning — so this repo was
  silently off the fleet pins. `rust = "1.96.*"` and the hand-declared
  `rust-std-wasm32-unknown-unknown = "1.96.*"` now ride the managed
  `shipit-rust-release-toolchain` block (byte-identical pins, and the std was only
  hand-declared because the local `rust` pin made shipit skip that very block), and
  the `test` task rides `shipit-test-task` as `./bin/shipit test` (the workaround
  cited shipit#444, now closed). `pixi run test` still runs `cargo nextest run` over
  the whole workspace — dispatched via the declared `[toolchains] "." = "rust"` — so
  the runner and the test set are unchanged. The lockfile does not move
  (arthur-debert/shipit#1138)
