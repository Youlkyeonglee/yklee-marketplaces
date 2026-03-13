---
description: "Copy example files to the current project"
---

Run the following bash commands to copy example files into the current working directory:
```bash
PLUGIN_DIR=$(find ~/.claude/plugins/cache -type d -name "uou-islab-report*" | head -1)
cp -r "$PLUGIN_DIR/examples/." .
```

After running the command, list all copied files:
```bash
ls -la
```

Then confirm to the user which files were successfully added to the project.

