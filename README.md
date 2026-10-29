# Skill Example Gen

Give any skill a test surface: worked examples plus a runnable smoke-test
module for its scripts.

## Why

Most skills ship with a workflow and no examples, and their scripts have
no test at all. The next person maintaining the skill relearns both from
scratch and cannot tell if a fix broke a script. Skill Example Gen reads a
SKILL.md, writes 2 to 3 grounded worked examples into it (appended, never
replacing), and generates a `tests/test_scripts.py` that checks each
referenced script for `--help` exit 0, byte-compile, a happy path in a
temp dir, and a failure path with a nonzero exit.

## How it works

1. READ: parse the target SKILL.md frontmatter, steps, and every
   `scripts/*.py` reference; list findings before writing.
2. EXAMPLES: generate 2 to 3 worked examples (input, expected output,
   per-step behavior) strictly from what the skill says; append under
   `## Worked example` in the SKILL.md.
3. SMOKE TESTS: emit `<plugin>/tests/test_scripts.py` with per-script
   help/compile/happy/fail checks, plain python asserts, no pytest, no
   pip, no network.
4. INSTALL: write the files, byte-compile the generated module.
5. REPORT: count of examples, count of tests, runner PASS/FAIL, and any
   script that could not be smoke-tested plus why.

The generated tests are safe by construction: every fixture lives in a
fresh `tempfile.TemporaryDirectory`, and the runner exits 1 on any
failure.

## Install

Option A: self-installable marketplace

1. Download or clone this repo into a folder.
2. In ZCode, open Plugin Marketplace, then Add, then Add Plugin Marketplace.
3. Paste the folder path, wait for it to load, then Install `skill-example-gen`.

Option B: manual copy

Copy the `skill-example-gen` folder into your workspace `plugins/`
directory, then restart ZCode.

## Try it

```
/skill-example-gen /path/to/phone-bridge
```

Expected: a list of the scripts found under
`skills/phone-bridge/scripts/`, two to three appended worked examples in
that skill's SKILL.md, and a new `tests/test_scripts.py` whose
`python3 tests/test_scripts.py` run prints a `PASS` line per check.

## Requirements

Python 3 (stdlib only). A target plugin directory with at least one
`skills/<skill>/SKILL.md`. No network.

---

<p align="center">
  <a href="https://z.ai/subscribe?ic=R0K78RJKNW"><img alt="z.ai coding plans - 10% OFF coupon" src="https://img.shields.io/badge/z.ai%20coding%20plans-10%25%20OFF%20coupon-blueviolet" /></a>
  <a href="https://zcode.z.ai/invite/ALWM9JUA"><img alt="Try ZCode free" src="https://img.shields.io/badge/Try%20ZCode-free%20invite-0969da" /></a>
</p>

## License

MIT
