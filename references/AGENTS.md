# References Guidance

- Must treat every submodule repository under `references/` as read-only reference material.
- Do not edit files inside submodules unless the user explicitly asks to fork or patch that upstream project.
- Do not run dependency installation, build, test, package, setup, benchmark, or unknown project scripts inside submodules unless the user explicitly asks.
- Use lightweight inspection only: `find`, `rg`, `sed`, and read-only file viewing.
- Prefer English docs and English source comments. Skip localized documentation when English source material exists.
- If a path is stale or a submodule is unavailable, record it as unavailable; do not guess.
