---
description: "Copy example files to the current project"
---

Run the following bash command to copy example files into the current working directory:
```bash
cp -r "$CLAUDE_PLUGIN_ROOT/examples/." .
```

After running the command, list all files that were copied by running:
```bash
find . -maxdepth 2 -newer "$CLAUDE_PLUGIN_ROOT/examples" -type f
```

Then confirm to the user which files were successfully added to the project.