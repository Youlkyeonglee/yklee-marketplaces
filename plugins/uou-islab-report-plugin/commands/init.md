---
description: "Copy example files to the current project"
---

Run the following bash commands to copy example files into the current working directory:
```bash
PLUGIN_BASE=$(find ~/.claude/plugins/cache -type d -name "uou-islab-report*" | head -1)
PLUGIN_DIR=$(find "$PLUGIN_BASE" -maxdepth 1 -mindepth 1 -type d | sort -V | tail -1)
cp -r "$PLUGIN_DIR/examples/." .
```

After running the command, list all copied files:
```bash
ls -la
```

Then confirm to the user which files were successfully added to the project.

