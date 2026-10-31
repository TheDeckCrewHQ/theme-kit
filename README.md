# Deck Crew theme kit

Make your own theme for **Deck Crew**, the Stream Deck+ plugin for agent CLIs: your own key plates, chat tiles,
deck switch keys and touch strips, still or moving, plus your own strip backgrounds on the free **Main** profile.

A theme is a folder of images and a small `theme.json`. It lives in your user folder, so plugin updates never touch
it, and the deck picks it up the moment you save it.

- `SKILL.md`: a skill for your agent CLI (Claude Code, Codex, agy, Grok, OpenCode) that walks you through a theme:
  it asks about the look, uses your own images or your agent's image tool (never images from the web), writes the
  folder, checks it, shows a preview and keeps dated backups.
- `FORMAT.md`: the theme format, if you'd rather make one by hand.
- `examples/minimal/`: a complete small theme ("Slate") to start from.

Needs Windows, a Stream Deck+ and Deck Crew from the Elgato Marketplace, set up once (its Setup key installs what the
checker runs on).

## Install the skill

Copy `SKILL.md` and `FORMAT.md` into a folder named `deck-theme-kit` in your agent's skills folder:

| Agent | Folder | Then type |
|---|---|---|
| Claude Code | `%USERPROFILE%\.claude\skills\deck-theme-kit\` | `/deck-theme-kit` |
| Codex | `%USERPROFILE%\.codex\skills\deck-theme-kit\` | `$deck-theme-kit` |
| agy | `%USERPROFILE%\.gemini\config\skills\deck-theme-kit\` | `/deck-theme-kit` |
| Grok | `%USERPROFILE%\.grok\skills\deck-theme-kit\` | `/deck-theme-kit` |
| OpenCode | `%USERPROFILE%\.config\opencode\commands\deck-theme-kit.md` (SKILL.md, renamed) | `/deck-theme-kit` |

Or just ask your agent to "make me a deck theme with the kit at <this folder>".

## Try the example

Copy `examples\minimal` to `%USERPROFILE%\.claude\claude-deck\themes\minimal`, then in the Stream Deck app select
the Setup key and pick **Deck theme → Your themes → Slate**.

## Check a theme

```powershell
& "$env:USERPROFILE\.claude\claude-deck\node\node.exe" "$env:USERPROFILE\.claude\claude-deck\runtime\scripts\theme-check.mjs" "<theme folder>" --preview
```

## More

Deck Crew, its decks and more themes: https://thedeckcrewhq.com

MIT licensed (see `LICENSE`). Deck Crew itself is a separate, paid product; this kit only describes its public theme
format.
