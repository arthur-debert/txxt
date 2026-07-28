- Packaging: `lex-wasm` declares `conda` as a selectable publish endpoint alongside
  `gh-release` and npm. The derived endpoint repackages the platform-independent
  wasm-pack archive into one `noarch: generic` `.conda` in the `noarch/` subdir of
  this repo's public Artifact channel, so a consumer can pin the wasm bundle as an
  ordinary conda package. npm publishing is unchanged (lex#850)
