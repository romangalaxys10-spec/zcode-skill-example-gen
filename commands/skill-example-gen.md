---
description: Generate worked examples and smoke tests for a skill's scripts
allowed-tools: Read, Write, Edit, Bash, Glob
argument-hint: <plugin-dir> [skill-name]
---
Invoke the `skill-example-gen` skill with this input: **$ARGUMENTS**

If no plugin dir is given, use the current plugin (the dir containing the
invoked skill's repo root). If no skill name is given, use the skill whose
name matches the plugin slug. End the run with the step-5 report and the
generated test module's PASS/FAIL output.
