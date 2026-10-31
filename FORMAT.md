# Deck Crew theme format (format 1)

A theme is a folder of images and one `theme.json`. Deck Crew reads it from your user folder, so plugin updates
never touch it. Everything here is optional except `theme.json`: whatever a theme leaves out, the deck draws in its
plain Classic look.

```
%USERPROFILE%\.claude\claude-deck\themes\<id>\
  theme.json
  plates\     slot  button  command  pager  blank          (.png / .jpg, 144×144)
  switch\     claude  codex  agy  grok  opencode  main  all  (.png / .jpg, 144×144)
  strips\     main  agents  menus  Settings  Status  Keys  Help  Session  Build  Review  Other   (.png / .jpg, 800×100)
  motion\     slot\  command\  switch-<deck>\  strip-<page>\<knob>\   (frames: 00.jpg, 01.jpg, …)
```

Check a theme before you use it (the deck runs the same checks and won't offer a theme that fails them):

```powershell
& "$env:USERPROFILE\.claude\claude-deck\node\node.exe" "$env:USERPROFILE\.claude\claude-deck\runtime\scripts\theme-check.mjs" "<folder>" --preview
```

It prints every problem at once, ends with `ok: … will load` or `not loaded: N errors`, and with `--preview` writes
`preview.html` into the folder (every plate, switch key and strip, with sample text in your ink and font).

## The folder name

`<id>`: lowercase letters, digits and `-`, up to 40 characters, starting with a letter or digit (`slate`,
`night-garden`). Folders starting with `.` are ignored, so `themes\.backups\` and `themes\.work\` are safe places for
copies and work in progress.

## theme.json

```json
{
  "format": 1,
  "name": "Slate",
  "ink": "#eef2f6",
  "font": "Segoe UI",
  "bg": "#10151c",
  "stripText": { "title": "#9fe7dc", "value": "#eef2f6", "dim": "#8c99a8" },
  "accents": { "idle": "#5eead4" },
  "fps": 12
}
```

| Field | Required | What it is |
|---|---|---|
| `format` | no | `1`. A deck that reads another format says so. |
| `name` | yes | Shown in the theme list, 1 to 32 characters. |
| `ink` | yes | `#rrggbb`. Glyphs, labels and values on the button, command and pager plates. At least 4.5:1 contrast on them (checked). |
| `font` | no | One of `Segoe UI` (default), `Cambria`, `Trebuchet MS`, `Bahnschrift`: the fonts the deck can measure, so labels fit and long ones slide. |
| `bg` | yes | `#rrggbb`. The strip's colour where you have no strip image, and the colour strip text is checked against. |
| `stripText` | no | `title`, `value`, `dim` colours of the knob text. `title` and `value` need 4.5:1 on `bg`, `dim` 3:1. Default: `ink`, `ink`, `#9ca3af`. |
| `accents` | no | The chat tile's rim colour for `idle`, `work` (working, thinking, compacting) and `done`. Waiting and error keep the deck's own colours: they mean "look at me". |
| `fps` | no | Motion frames a second: `12` (default), `6`, `4`, `3`, `2` or `1`. |

Other keys are ignored (the check warns about them).

## plates\ (144×144)

The backing under the deck's own art. The deck draws on top of every plate; don't paint text, glyphs or logos into
them.

| Plate | Where | Drawn on top | Rules |
|---|---|---|---|
| `slot` | every chat key on the agents page, and the free "+ new chat" key (faded) | the chat's mascot, its name and what it's doing, the state rim | dark: names are drawn in light colours. A PNG is checked against white (4.5:1); a JPEG isn't (warning). At most 16 KB: a busy chat's key is sent to the deck 12 times a second. |
| `button` | the main page's keys (New Session, Send, Stop, Model, Effort…) | the key's glyph and value in `ink`, its state ring | PNG (contrast checked), at most 64 KB |
| `command` | the menus' command keys, and the switch keys without a `switch\` image | the command's name in `ink` | PNG (contrast checked), at most 64 KB |
| `pager` | the bottom-right paging key of each menu folder | "next / back" and the page number in `ink` | PNG (contrast checked), at most 64 KB |
| `blank` | an empty place on a menu page | nothing | at most 64 KB |

Contrast is measured between `ink` and the plate's average colour over the label band (the bottom 40 px) and over the
whole key; both need 4.5:1. Transparent corners are fine: the deck behind them is black. A plate you leave out is the
deck's plain dark tile (`#151515`), so `ink` then needs 4.5:1 on that too.

## switch\ (144×144)

The backdrop of each deck's switch key in the Other folder, one per deck: `claude`, `codex`, `agy`, `grok`,
`opencode`, `main`, `all`. The deck draws the tool's own logo, unchanged, and the deck's name on top. At most 64 KB.
A deck without one uses the `command` plate.

## strips\ (800×100)

The touch strip's background, one per page: `main`, `agents`, `menus`, and each menu folder (`Settings`, `Status`,
`Keys`, `Help`, `Session`, `Build`, `Review`, `Other`). A page without one uses `agents`, else a plain `bg` fill. At
most 160 KB. Each knob shows its own 200 px: keep the top 35 px (titles) and the middle (the value) of each quarter
calm so the text reads.

## motion\ (frames)

Any of these surfaces can move. A motion folder holds 2 to 288 JPEG frames played in name order (`00.jpg`, `01.jpg`,
…) at `fps`, looping.

| Folder | Frames | Each frame at most | Notes |
|---|---|---|---|
| `motion\slot\` | 144×144 | 16 KB | chat keys play these instead of the `slot` plate; the free slot keeps the still plate |
| `motion\command\` | 144×144 | 16 KB | the menus' command keys, at half `fps` |
| `motion\switch-<deck>\` | 144×144 | 16 KB | that deck's switch key |
| `motion\strip-<page>\<knob>\` | 200×100 | 12 KB | `<knob>` = `0`, `1`, `2`, `3`, one folder per knob that moves; the others show the page's still strip |

Every frame goes over the deck's USB link, so the deck checks what a theme would send each second on its busiest
page and refuses a theme over budget. At 12 fps every combination above fits.

The main page's buttons don't move in a theme (their glyphs are drawn live over the still `button` plate).

## Limits

- The whole theme at most 30 MB.
- PNG (not interlaced) or baseline / progressive JPEG.
- What a theme doesn't change: the mascots, the tools' logos, every label and value, the waiting and error colours, and
  the menus page's folder tiles (those are Stream Deck's own folder keys).

## Main's strips

The Main profile's look is one of its shipped themes; a theme can't add a new one. You can give each Main page its
own strip, which shows over whichever Main theme is on:

```
%USERPROFILE%\.home-deck\strips\
  home.png  desk.png  apps.png  scenes.png  system.png        (.png / .jpg, 800×100, at most 160 KB)
  <page>\<knob>\00.jpg, 01.jpg, …                              (200×100 frames, at most 12 KB each, 2 to 288, 10 fps)
```

A page or knob without one keeps the theme's strip. A file that fails the check is left out (and logged); the rest
still show. Check the folder with:

```powershell
& "$env:USERPROFILE\.claude\claude-deck\node\node.exe" "$env:USERPROFILE\.claude\claude-deck\runtime\scripts\theme-check.mjs" --main "$env:USERPROFILE\.home-deck\strips"
```

## Using a theme

The deck reloads the themes folder by itself when anything in it changes. Pick the theme on the Stream Deck app's
Setup key: its settings → **Deck theme** → under **Your themes**. A folder that isn't offered is listed there with the
reason. Main's strips show as soon as they're saved.
