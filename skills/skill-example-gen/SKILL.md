# Skill Example Gen

Turn any existing skill into worked examples and runnable smoke tests.
Use after shipping a skill or plugin to give it a test surface it never
had.

## Inputs

- Target plugin directory (default: the plugin dir containing the invoked
  session, or a path the user passes).
- Target skill (default: the skill whose name matches the plugin slug).

## Steps

### 1. READ

Parse the target `SKILL.md`:

- Frontmatter: `name`, `description`, `allowed-tools` (the frontmatter
  block between the first `---` pair; read it, do not assume fields).
- Body: numbered steps, exact commands, and every `scripts/*.py` path
  the skill references (grep for `scripts/`, `.py`, `python3`,
  `python`).
- For each referenced script, record: path relative to the skill dir,
  its argparse flags if the help text is available, and what the skill
  says its exit codes mean (or "undocumented" if the skill is silent).

List the findings in the session before writing anything. A skill that
references no script gets examples only; say so.

### 2. EXAMPLES

Generate 2 to 3 worked examples, each grounded only in what the skill
actually says. Never invent a capability the skill does not have.

Each example has three parts:

- **Input**: a realistic user invocation, concrete values.
- **Expected output**: what the session or file should look like after.
- **Per-step behavior**: for each numbered step of the skill, one line
  on what the agent does and what it must not do (the guardrails).

Write them under `## Worked example` in the target `SKILL.md`.

Append rule: insert before the last heading only if a `## Worked example`
section does not exist; if it does, replace only that section's content.
Never rewrite any other part of the file.

### 3. SMOKE TESTS

For each referenced script, generate tests into
`<plugin>/tests/test_scripts.py`. Plain python, no pytest, no
third-party imports, no network. Per script, four checks:

1. **help**: run `python3 <script> --help`, assert exit 0 and that
   stderr or stdout is not empty.
2. **compile**: `py_compile.compile(<script>)` without raising.
3. **happy path**: build minimal fixtures in a fresh temp dir
   (`tempfile.TemporaryDirectory`), run the script with the arguments
   the skill's own example uses, assert the expected exit code and a
   marker in the output or a file the script writes.
4. **failure path**: one input the script documents as failing (missing
   config, bad arg, unreachable source per the skill's Limits). Assert
   the documented exit code; if the skill does not document one,
   assert the script exits non-zero.

The generated file includes a `main` runner: it executes every test
function whose name starts with `test_`, prints `PASS`/`FAIL` per
function, and exits 1 if any failed.

Run the generated file once and show the output.

### 4. INSTALL

Write the files:

- `<target>/skills/<skill>/SKILL.md` - appended `## Worked example`
  section (in place, no new file).
- `<target>/tests/test_scripts.py` - the smoke test module.

Use `Write`/`Edit`, never a shell heredoc. After writing, byte-compile
the generated test module to catch syntax errors before reporting.

### 5. REPORT

End with a short list: examples added (count and one-line titles),
tests generated (count, which scripts), runner result
(N passed / M failed), and any script that could not be smoke-tested
and why (for example, it requires a live API that the happy path
cannot fake; the test asserts help and compile only, and says so in
the generated file's comment).

## Limits

- Examples are as good as the skill: a vague skill produces vague
  examples. The plugin reports this, it does not pad.
- The generated tests exercise scripts, not the agent's judgment. A
  passing test module says the scripts behave; it says nothing about
  whether the workflow steps were followed.
- No network, no pip, no pytest in the generated tests.
