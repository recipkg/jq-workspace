# Working on jq reconstruction

The full target is jq 1.8.2, including its documented CLI, language, formats,
libraries and platforms. The recipe and source are separate pinned Git
submodules. Improve the recipe before relying on undocumented behavior; build
the implementation from the complete recursive recipe and record all failed,
skipped and unverified acceptance cases. Do not call a system jq at runtime or
claim full compatibility from partial samples.

Edit and publish the appropriate submodule repository first, then update the
gitlink. Keep `generated/` tracked and never flatten source into this container.
Run package checks on `recipes/jq`; implementations and build artifacts do not
belong in Markdown recipe archives. Preserve unrelated user work.
