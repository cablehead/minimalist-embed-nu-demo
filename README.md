# A guide to embed Nushell in your application

_Four bite‑sized demos for embedding Nushell in Rust_

This repository demonstrates **four escalating patterns** for running Nushell
inside a Rust application.

| Example                                        | New capability                                    |
| ---------------------------------------------- | ------------------------------------------------- |
| [**p1‑basic**](./p1-basic/README.md)           | Run one Nushell command synchronously             |
| [**p2‑background**](./p2-background/README.md) | Background thread + Ctrl‑C + external proc kill   |
| [**p3‑the‑works**](./p3-the-works/README.md)   | Custom commands, multi‑thread pipelines, shutdown |
| [**p4‑sandbox**](./p4-sandbox/README.md)       | Restricted environment with only filter commands  |

---

## Why embed Nushell?

- Script‑like power _without_ spawning `/bin/sh` — pipeline, tables, math.
- Typed data ﬂowing through Rust ↔ Nushell via `Value` and `PipelineData`.
- Works everywhere Rust does; no shell‑specific quirks.

---

## Prerequisites

- **Rust 1.78+** with `cargo`
- Works on macOS, Linux, Windows (no extra setup)
- (Optional) Native Nushell install for comparison.

---

## 30‑second quick‑start

```
git clone https://github.com/cablehead/how-to-embed-nu
cd how-to-embed-nu

# Build the whole workspace
cargo build --workspace

# Run the simplest demo
cargo run -p p1-basic -- '"hello" | str upcase'
# → HELLO
```

---

## Example progression

| Crate                                          | One‑liner to try                              | Expected output                                             |
| ---------------------------------------------- | --------------------------------------------- | ----------------------------------------------------------- |
| **[p1‑basic](./p1-basic/README.md)**           | `cargo r -p p1-basic '"hello" \| str upcase'` | `HELLO`                                                     |
| **[p2‑background](./p2-background/README.md)** | `cargo r -p p2-background '^sleep 5; "done"'` | `done`                                                      |
| **[p3-the-works](./p3-the-works/README.md)**   | `cargo r -p p3-the-works '{\|_\| warble}'`    | `warble, oh my`<br/><small>(for each line of input)</small> |
| **[p4-sandbox](./p4-sandbox/README.md)**       | `cargo r -p p4-sandbox '"hi" \| wrap msg'`    | `{msg: hi}`                                                 |

---

## Running every test

```
cargo test --workspace
```

All suites exercise command output plus signal/start‑stop behaviour using
[`assert_cmd`](https://docs.rs/assert_cmd)
([Docs.rs](https://docs.rs/assert_cmd)), `nix` signals, and the `sysinfo`
process table.

---

## Repo layout

```
/
├─ p1-basic/         # minimal, single command
├─ p2-background/    # adds job control
├─ p3-the-works/     # custom cmd + multi-pipeline
├─ p4-sandbox/       # restricted env (filters only)
├─ Cargo.toml        # workspace manifest
└─ README.md         # you are here
```

Shared deps such as **`nu-cmd-lang`** (boots the engine)
([Docs.rs](https://docs.rs/nu-cmd-lang)), and **`nu-protocol`** (core types)
([Docs.rs](https://docs.rs/nu-protocol)) live in `[workspace.dependencies]`.

---

## Further reading

- **How Nushell code gets run** — deep dive into the parse/compile/eval cycle.
  ([Nushell](https://www.nushell.sh/book/how_nushell_code_gets_run.html))
- **Background jobs** in Nushell's own shell.
  ([Nushell](https://www.nushell.sh/book/background_jobs.html))
- API docs:
  [`nu-cmd-lang::create_default_context`](https://docs.rs/nu-cmd-lang/latest/nu_cmd_lang/fn.create_default_context.html),
  [`nu-cli::gather_parent_env_vars`](https://docs.rs/nu-cli/latest/nu_cli/fn.gather_parent_env_vars.html),
  [`ctrlc`](https://docs.rs/ctrlc) for signal handling.

---

## Acknowledgements

An early example from [@sophiajt](https://github.com/sophiajt) herself:
https://github.com/sophiajt/nu_app that inspired this mini‑series.
