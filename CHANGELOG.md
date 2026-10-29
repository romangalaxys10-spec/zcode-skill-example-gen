# Changelog

## 0.1.1 - 2026-10-30

- Generated test skeletons hardened after live run against a real plugin copy.

## 0.1.0 - 2026-10-29

- Initial release: SKILL.md parsing (frontmatter, steps, referenced
  scripts), 2 to 3 grounded worked examples appended under `## Worked
  example`, and a generated `tests/test_scripts.py` module.
- Per-script smoke checks: `--help` exit 0, `py_compile` byte-compile,
  a happy-path skeleton with temp-dir fixtures, and a failure-path
  probe that asserts a non-zero exit.
- `gen_smoke.py`: stdlib generator, safe by construction (temp dirs
  only, no network, no pip); the generated module has a plain runner
  that prints PASS/FAIL and exits 1 on any failure.
