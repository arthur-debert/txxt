@AGENTS.md

# Lex

Lex is a plain text format for structured documents — more expressive than Markdown, human-readable in raw form. Structure comes from indentation (4-space tabs), not markup.

## Repo Structure

This is the unified Rust workspace containing all Lex crates and specifications.

```text
crates/
  lex-core/       Parser crate (crates.io)
  lex-analysis/   Stateless semantic analysis (crates.io)
  lex-lsp/        LSP server, tower-lsp — package: lexd-lsp (crates.io)
  lex-babel/      Format conversion via IR (crates.io)
  lex-config/     Configuration loader (crates.io)
  lex-cli/        Command-line interface — package: lexd (crates.io)
  lex-wasm/       WebAssembly bindings (not published)
comms/
  specs/          Grammar specs and test fixtures
  docs/           Website content (lex.ing)
  assets/         Images and resources
```

## Key Files

| What | Where |
| ------ | ------- |
| AST nodes | `crates/lex-core/src/lex/ast.rs` |
| Parser | `crates/lex-core/src/lex/parsing.rs` |
| Lexer | `crates/lex-core/src/lex/lexing.rs` |
| Grammar specs | `comms/specs/grammar-{core,line,inline}.lex` |
| Test fixtures | `comms/specs/elements/`, `comms/specs/trifecta/`, `comms/specs/benchmark/` |
| LSP server | `crates/lex-lsp/src/server.rs` |
| Analysis modules | `crates/lex-analysis/src/{semantic_tokens,document_symbols,hover,go_to_definition,completion,diagnostics}.rs` |
| Format adapters | `crates/lex-babel/src/formats/` |
| CLI entry | `crates/lex-cli/src/main.rs` |

## Development

- All crates build together: `cargo build --workspace`
- Tests: `cargo nextest run --workspace` or `cargo test --workspace`
- Lint gate: `pixi run lint` (`pixi run lint --fix` to format what it can) — the same
  multi-language gate CI runs, wired as a commit/push hook by the shipit-managed
  `lefthook.yml`. `shipit install` activates the hooks.
- Exclude lex-wasm from most commands: `--exclude lex-wasm`

## Releasing

Releases are cut by hand, never automatically. There is no cascade: nothing here
triggers on an upstream release, and cutting here triggers nothing downstream. A
cross-repo dependency is a plain conda package whose version lives in exactly one
place — the consuming repo's own `pixi.toml` — so a consumer picks up a new `lexd`
or `lexd-lsp` by editing its own pin. See
[shipit/docs/dependencies.md](https://github.com/arthur-debert/shipit/blob/main/docs/dependencies.md).

To cut: `gh workflow run shipit-release.yml --repo lex-fmt/lex -f version=X.Y.Z`
(workflow_dispatch, not tag-push — shipit's release blocks drive the bump, commit
and tag). `stage` selects the full chain or one re-runnable stage
(`prepare` | `build` | `sign` | `publish`); `endpoints` narrows which endpoints
fire. The release surface itself — artifacts, platforms, endpoints — is declared in
`.shipit.toml` `[artifacts]`, not in the workflow.

Every feature/fix PR must add a `CHANGELOG/unreleased-*.md` fragment; `prepare`
refuses an empty release. Run `pixi run changelog render` after adding one so
`CHANGELOG.md` stays in sync.

## Related repos

- [tree-sitter-lex](https://github.com/lex-fmt/tree-sitter-lex) — Tree-sitter grammar (syntax highlighting, injection, textobjects)
- [lexed](https://github.com/lex-fmt/lexed) — Electron desktop editor
- [nvim](https://github.com/lex-fmt/nvim) — Neovim plugin
- [vscode](https://github.com/lex-fmt/vscode) — VSCode extension

Editor UIs download pre-built lexd-lsp binaries from this repo's releases,
and tree-sitter artifacts from lex-fmt/tree-sitter-lex releases.

For local development, set `LEX_LSP_PATH` to point editors at a local build:

```sh
cargo build -p lexd-lsp
LEX_LSP_PATH=./target/debug/lexd-lsp
```

## CLI Quick Reference

```sh
lexd inspect file.lex                    # AST tree visualization (default)
lexd inspect file.lex ast-tag            # XML-like AST
lexd inspect file.lex ast-json           # JSON AST
lexd inspect file.lex --ast-full         # Full AST with all properties
lexd inspect file.lex token-line-simple  # Token stream (line-classified)
lexd inspect file.lex ir-json            # Intermediate representation
lexd file.lex --to markdown              # Convert formats
lexd format file.lex                     # Auto-format
```
