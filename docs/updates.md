# Updating WSTerminal

!!! info "Under construction — gated on the pre-launch dry run"
    The exact update flow will be documented after the operator's mini-PC
    test build verifies Phase C survival end-to-end.

## What this page will cover

- The in-app update notifier — when it appears, what it says
- Defer vs. forced lockout (when an update is mandatory)
- The manual update process — download, replace, restart
- What persists across an update (license, presets, trade log — stored in
  `%APPDATA%\WSTerminal\` on Windows, outside the install dir, so it
  survives wholesale folder replacement)
- What does NOT persist (the bot's local config_product.py is replaced —
  but your saved presets in `%APPDATA%\` survive and can be reapplied)
- Rolling back to the previous version if a new release breaks something
- Future automatic-update plans (post-launch)
