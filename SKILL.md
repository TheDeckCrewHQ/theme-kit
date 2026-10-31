---
name: deck-theme-kit
description: Make or change your own Deck Crew theme for the Stream Deck+ on Windows (the agent decks' keys and touch strip), and give the Main profile your own strip backgrounds or another of its themes. A short interview (mood, your own images, surfaces, still or moving, colours, font), then one of three ways to make the art - painted by an image generator (Codex CLI, an image skill or tool you already have, or prompts you paste into any AI image tool), drawn in code (JavaScript rendered with Edge or Chrome), or motion-designed with the huashu-motion / huashu-art-motion skills. Writes the theme folder, checks it, previews it, installs every file where it belongs and keeps dated backups. Use when the user says "make a deck theme", "theme kit", "my own stream deck theme", "change my deck theme", "custom strip", or wants the deck to match a look, a faceplate or their own art.
---

# Deck Crew theme kit

You are helping the user make their own theme for Deck Crew, the Stream Deck+ plugin for agent CLIs. A theme is a
folder of images plus `theme.json`; the format is in `FORMAT.md` next to this file (if it isn't there, the public copy
is at https://github.com/thedeckcrewhq/theme-kit/blob/main/FORMAT.md). Read FORMAT.md before you write anything. Go one
step at a time: say what you're about to do, do it, report in a line or two, ask, move on.

However the art is made (painted, drawn in code, motion-designed), the end result is always the same: `theme.json`
plus images in the folders FORMAT.md names. The paths below only change how the images get made.

## Hard rules

- **Art comes from these places only:** files the user gives you (that they own or may use), images an image
  generator on this computer makes from a prompt you write, images the user makes from your prompts in an AI image
  tool of their choice, and images you draw in code. Never download, search for or fetch images from the web, and
  never copy logos, characters or artwork the user doesn't own. Ask for no text, letters or logos in any image: the
  deck draws every label and logo itself.
- **Keys and passwords:** never ask the user to paste an API key into the chat, never write one into any file you
  create, never print one. Use only a tool that already holds its own key.
- **Touch only these:** `%USERPROFILE%\.claude\claude-deck\themes\` (themes, `.work\`, `.backups\`),
  `%USERPROFILE%\.home-deck\strips\` (Main's strips) and, only when the user asks for a Main theme,
  `%USERPROFILE%\.home-deck\show-main.json`. Never read, explain, modify or copy the plugins' own files (anything in
  their plugin folders, or `runtime\`, `node\`, `bin\`, `hooks\`, `scripts\`), never write into the Stream Deck app's
  folders or edit its profiles. Running the theme checker as written below is the one exception: run it, don't open it.
- **Ask before each change on disk** outside the work folder (one question per change; "no" means skip it). Before
  replacing anything, make a dated backup (step 9).
- Run only the commands written here, filling in only the `<...>` parts. Windows PowerShell; no encoded commands. The
  one addition: on the code-drawn path you may write your own drawing script into the work folder and run it with the
  deck's Node (step 6B). It uses Node's built-ins only: no package installs, no network.

## 1. Check

```powershell
$deck = "$env:USERPROFILE\.claude\claude-deck"
$node = "$deck\node\node.exe"; $check = "$deck\runtime\scripts\theme-check.mjs"
"node: $(Test-Path $node)  checker: $(Test-Path $check)"
Get-ChildItem "$deck\themes" -Directory -ErrorAction SilentlyContinue | Where-Object Name -notlike ".*" | ForEach-Object Name
```

- Both `True`: go on. List the user's existing themes (the names printed).
- Either `False`: Deck Crew isn't set up yet (it puts both there). Ask the user to start Stream Deck with Deck Crew
  installed and press its Setup key once (or run the `deck-setup` skill), then check again.

## 2. Interview

Ask one question at a time (two short ones together at most). Offer two or three concrete answers to pick from, and
skip a question the user already answered. Changing an existing theme: ask only what the change needs (which surface,
what's wrong, what instead) and go to step 10.

1. **New or change?** A new theme, or change one of the themes listed in step 1. New: its **name** (shown in the
   list, up to 32 characters). You make the folder id from it (step 4).
2. **Mood.** What should it feel like, in a few words? Offer directions to react to: a place (night city, forest,
   desert), a material (brushed metal, paper, glass, wood), an era or style (retro terminal, ink wash, art deco), a
   mood (calm, intense, playful).
3. **Your images.** "Do you have images to use? A photo of your faceplate, art you made, screenshots of a look you
   like." Ask for file paths, and ask for each: **inspiration only** (you study it for colour, light and texture) or
   **the art itself** (it goes on the keys, cropped). Look at every image yourself. Only images they own or may use.
4. **Where it shows.** The agent decks (Deck Crew's own keys and strips), Main's strips, or both. For the agent decks:
   everything, or some of these: chat tiles, button / command / pager / blank plates, the deck switch keys, strips.
5. **Still or moving.** Moving surfaces: chat tiles, menu command keys, switch keys, strip knobs. Pace: calm (slow
   drift, a light sweep, 2 to 4 s loops) or lively. Calm loops read best and cost the deck's USB link the least.
6. **Colours.** Propose a palette from their answers and images: `bg` (dark strip colour), `ink` (light text on the
   plates), up to three chat tile rims (`accents`: idle, working, done; waiting and error keep the deck's red and
   amber). Show it as hex values with a word each; let them change any.
7. **Font.** Segoe UI (clean, neutral), Cambria (classic serif), Trebuchet MS (friendly), Bahnschrift (condensed,
   technical). The deck can only measure these four.
8. **How to make the art** (step 6 says what each needs):
   - **Painted**: every surface its own painted image from an image generator. Richest look; needs an image tool, or
     the user generates images from your prompts.
   - **Code-drawn**: shapes, gradients, patterns and motion written in JavaScript and rendered to images here. Crisp
     and exact; nothing to install.
   - **Motion-designed**: animated with the huashu-motion or huashu-art-motion agent skills. Best for rich motion;
     the skill must be installed in the user's agent first.
   - Mixes work: painted plates with code-drawn motion on top, the user's own image as the strip, and so on.
9. **Main too?** Only if they picked Main in question 4: their own strips on Main's pages, and / or a switch to
   another of Main's shipped themes (step 11).

## 3. Art direction (confirm before any image is made)

Write a short brief and show it to the user. Change it until they say yes:

- **Concept**: one paragraph. What the theme is, its light, texture, line style and mood.
- **Palette**: the hex values from question 6, plus the colours the art may use around them.
- **Style prefix**: one or two sentences that start every image prompt, every drawing and every motion brief, so
  plates, tiles, switch keys and strips look like one set. Same lighting direction, same texture, same palette on
  every surface. If the user gave an inspiration image, name it as the reference for every image too.
- **Calm zones** (where the deck draws text; keep them low in detail and in contrast with `ink`):

| Surface | Size | Keep calm |
|---|---|---|
| chat tile (`slot`) | 144×144 | the centre (the mascot) and the bottom 40 px (name, state); keep it dark |
| `button` | 144×144 | the centre (glyph) and the bottom 45 px (live values, label) |
| `command`, `pager` | 144×144 | the middle and bottom (names, page number) |
| `blank` | 144×144 | nothing on top; still quieter than its neighbours |
| switch key | 144×144 | the centre (the tool's logo) and the bottom 35 px (the deck's name) |
| strip | 800×100 | in each 200 px quarter: the top 35 px (title) and the middle (value) |

Save the brief as `brief.txt` in the work folder (step 4) and use it unchanged for every image.

## 4. Work folder

Build in a work folder the deck ignores, so a half-made theme never shows. The folder id is the name in lowercase,
spaces as `-` (letters, digits, `-`, up to 40 characters), starting with `1-` so the user's themes sort first among
**Your themes** (the deck lists them by folder id): "Night Garden" is `1-night-garden`. A second theme they want first
can take `0-`.

```powershell
$id = "<id>"
$work = "$env:USERPROFILE\.claude\claude-deck\themes\.work\$id"
New-Item -ItemType Directory -Force "$work\refs", "$work\incoming", "$work\art", "$work\code", "$work\plates", "$work\switch", "$work\strips", "$work\motion" | Out-Null
$work
```

- `refs\`: copies of the user's images (copy them in now; the originals stay where they are).
- `incoming\`: images the user generates from your prompts (painted path, hand-off).
- `art\`: raw images and renders, before fitting. `code\`: your drawing scripts.
- `plates\`, `switch\`, `strips\`, `motion\`: the finished theme, as FORMAT.md lays it out.

This folder is the theme's source: keep it after install (step 9 copies only the finished parts), so a later change
starts from the same art and brief. Changing an existing theme: copy it in first,
`Copy-Item "$env:USERPROFILE\.claude\claude-deck\themes\$id\*" $work -Recurse -Force` (skip if `$work` already holds
its source).

## 5. Proof first

Before making everything, make **one chat tile or button plate and the `agents` strip** by the chosen path (step 6),
fit them (step 7), write `theme.json` (step 7), and preview (step 8). Show the user. Only when they like it, make the
rest in the same style. A proof costs minutes; a full set made in the wrong style costs an hour.

## 6. Make the art

Sizes are in step 3's table. Motion frames: 144×144 for keys, 200×100 per strip knob (strip quarter 0 = x 0 to 199,
1 = 200 to 399, and so on), 2 to 288 frames a loop at the theme's `fps` (12 is smooth; 24 frames = 2 s). A loop's last
frame must lead back into its first: move things by whole cycles (a full turn, a drift by exactly the pattern's
width), never stop and jump.

### 6A. Painted

**What you need first: an image generator.** Look, in this order, and tell the user what you found:

1. **Your own image tool.** If you (the agent running this skill) can generate images yourself, use that.
2. **The Codex CLI.** `Get-Command codex -ErrorAction SilentlyContinue; codex login status`. It must be installed and
   signed in; if it isn't signed in, ask the user to run `codex login` themselves (it opens a browser). One image:

   ```powershell
   $name = "<surface name>"; $before = Get-Date
   codex exec --skip-git-repo-check -s read-only -C "$work\art" -i "<reference image, or drop this>" -- "Use your built-in image generation tool exactly once to generate one image. Do not save, copy, move or edit any file and run no commands. Image: <style prefix> <surface prompt>"
   $img = Get-ChildItem "$env:USERPROFILE\.codex\generated_images" -Recurse -Filter *.png | Where-Object LastWriteTime -gt $before | Sort-Object LastWriteTime -Descending | Select-Object -First 1
   if ($img) { Copy-Item $img.FullName "$work\art\$name.png"; "saved: $name" } else { "no image: rephrase the prompt and try again" }
   ```

   Each call takes a minute or more. If a flag is refused, `codex exec --help` shows this version's spelling.
3. **An image skill or tool the user's agent already has** (an image-generation skill, an MCP image tool, a local
   image app with a command line). Ask the user to name it; use it as its own instructions say, saving into
   `$work\art\`. It must already hold its own key (hard rules).
4. **Prompt hand-off** (no tool, or the user prefers their own). Write `$work\prompts.md`: the style prefix once,
   then one entry per surface with the file name to save as (`incoming\<surface>.png`), the size to ask for (square
   for keys; the widest the tool offers for strips, subject in a centred horizontal band, as the kit crops it to
   800×100), the calm zones from step 3, and "no text, no letters, no logos". Open it for the user
   (`Start-Process "$work\prompts.md"`), let them generate the images anywhere they like and save them into
   `$work\incoming\`, then carry on with step 7 from there.

Prompts: one per surface, each different in subject but the same in style (the prefix). A plate's prompt says what
the key is for only through mood, never with text. Look at every result yourself and redo what breaks the brief
(text in the image, a busy calm zone, a style that drifts from the set).

**Moving painted surfaces:** a generator makes stills, not loops. Paint the still, then add the motion in code over
it (6B: a light sweep, drifting motes, a slow pan of a wider painting, a glow that breathes), or hand it to a motion
skill (6C).

### 6B. Code-drawn

**What you need first:** nothing new. The deck's Node runs your script, and Microsoft Edge (on every Windows 11 PC)
or Google Chrome renders it.

Write `$work\code\draw.mjs`: plain JavaScript, Node built-ins only (`node:fs`, `node:path`), that writes SVG files
into `$work\art\` (one per surface, or one per frame: `slot-00.svg`, `slot-01.svg`, …). Each SVG gives its own
`width` and `height` (144 / 144, 800 / 100 or 200 / 100), uses only the theme's font, and draws a frame from its time
`t` (frame index / frame count) so the loop closes. A painting or the user's image can sit under the drawing with
`<image href="<file name>">` next to the SVG. Run it:

```powershell
& "$env:USERPROFILE\.claude\claude-deck\node\node.exe" "$work\code\draw.mjs"
```

Render every SVG of one size to PNG (headless Edge, else Chrome; a private profile so an open browser isn't touched):

```powershell
function Render-Svgs($dir, $pattern, $w, $h) {
  $browser = @("${env:ProgramFiles(x86)}\Microsoft\Edge\Application\msedge.exe", "$env:ProgramFiles\Microsoft\Edge\Application\msedge.exe", "$env:ProgramFiles\Google\Chrome\Application\chrome.exe", "$env:LOCALAPPDATA\Google\Chrome\Application\chrome.exe") | Where-Object { Test-Path $_ } | Select-Object -First 1
  if (-not $browser) { "no Edge or Chrome found"; return }
  Get-ChildItem $dir -Filter $pattern | ForEach-Object {
    $png = [IO.Path]::ChangeExtension($_.FullName, ".png")
    $url = "file:///" + ($_.FullName -replace '\\', '/')
    Start-Process -FilePath $browser -Wait -WindowStyle Hidden -ArgumentList @("--headless=new", "--disable-gpu", "--hide-scrollbars", "--default-background-color=00000000", "--window-size=$w,$h", "--user-data-dir=`"$work\.browser`"", "--screenshot=`"$png`"", "`"$url`"")
  }
  "rendered: $dir\$pattern"
}
Render-Svgs "$work\art" "slot-*.svg" 144 144
```

About a second a frame. Transparent stays transparent in the PNG. Then fit and convert them (step 7).

### 6C. Motion-designed (huashu-motion / huashu-art-motion)

**What you need first:** the skill installed in the user's agent. These are third-party agent skills (on GitHub:
`alchaincyf/huashu-motion`, `alchaincyf/huashu-art-motion`), not part of Deck Crew. Check your skill list for
`huashu-motion` or `huashu-art-motion`. Not there: tell the user, and let them install it the way their agent installs
skills, then start a new session (a new skill shows up only then). Each brings its own requirements (huashu-motion
renders in the user's installed Chrome; huashu-art-motion asks for more tools); install those only if the user agrees.
No skill and no wish to install one: use 6B instead.

Brief the skill with the style prefix and calm zones, and ask for exactly this per moving surface: a seamless loop
at the deck's size (144×144 for a key, 800×100 for a strip), at the theme's `fps`, of a whole number of frames,
**exported as still frames** (PNG per frame; huashu-motion's `still` command takes the frame times, `0`, `1/fps`,
`2/fps`, …), with no text, no logos and no signature on them. Save the frames into `$work\art\<surface>\`. Then fit
and convert them (step 7): a strip's frames are cut into its four 200×100 knob quarters.

## 7. Fit images, write theme.json

Every image must be exactly its size. This crops an image to the target's shape (centred, or from `$x`, `$y`, `$cw`,
`$ch` when you pass a source rectangle: that's how a strip is cut into knob quarters) and scales it; `.png` keeps
transparency, `.jpg` is saved at the quality given (lower it if the check says a file is too big):

```powershell
Add-Type -AssemblyName System.Drawing
function Fit-Image($src, $dst, $w, $h, $quality = 85, $rect = $null) {
  $img = [System.Drawing.Image]::FromFile($src)
  if ($rect) { $cx, $cy, $cw, $ch = $rect } else {
    $scale = [Math]::Max($w / $img.Width, $h / $img.Height)
    $cw = [int]($w / $scale); $ch = [int]($h / $scale)
    $cx = [int](($img.Width - $cw) / 2); $cy = [int](($img.Height - $ch) / 2)
  }
  $bmp = New-Object System.Drawing.Bitmap $w, $h
  $g = [System.Drawing.Graphics]::FromImage($bmp); $g.InterpolationMode = "HighQualityBicubic"
  $g.DrawImage($img, (New-Object System.Drawing.Rectangle 0, 0, $w, $h), (New-Object System.Drawing.Rectangle $cx, $cy, $cw, $ch), "Pixel")
  if ($dst -like "*.png") { $bmp.Save($dst, [System.Drawing.Imaging.ImageFormat]::Png) } else {
    $enc = [System.Drawing.Imaging.ImageCodecInfo]::GetImageEncoders() | Where-Object MimeType -eq "image/jpeg"
    $p = New-Object System.Drawing.Imaging.EncoderParameters 1
    $p.Param[0] = New-Object System.Drawing.Imaging.EncoderParameter ([System.Drawing.Imaging.Encoder]::Quality), ([long]$quality)
    $bmp.Save($dst, $enc, $p)
  }
  $g.Dispose(); $bmp.Dispose(); $img.Dispose()
}
Fit-Image "<source image>" "$work\plates\slot.png" 144 144
```

- Plates `button`, `command`, `pager` as `.png` (their contrast is checked). Other stills `.png` or `.jpg`.
- Motion frames are `.jpg`, named `00.jpg`, `01.jpg`, … in their folder (`motion\slot\`, `motion\command\`,
  `motion\switch-<deck>\`, `motion\strip-<page>\<knob>\`). Key frames at most 16 KB, strip frames 12 KB: start at
  quality 80, lower it if the check complains. Example, one frame of a 800×100 strip render, knob 2:
  `Fit-Image "$work\art\agents\05.png" "$work\motion\strip-agents\2\05.jpg" 200 100 80 @(400, 0, 200, 100)` (the render
  must be exactly 800×100 for that rectangle).

**No art at all** (the fastest theme): plain plates drawn here, a rounded card in two colours with a thin edge:

```powershell
Add-Type -AssemblyName System.Drawing
function New-Plate($file, $top, $bottom, $edge) {
  $bmp = New-Object System.Drawing.Bitmap 144, 144
  $g = [System.Drawing.Graphics]::FromImage($bmp); $g.SmoothingMode = "AntiAlias"
  $path = New-Object System.Drawing.Drawing2D.GraphicsPath
  $r = 40; $path.AddArc(4, 4, $r, $r, 180, 90); $path.AddArc(140 - $r, 4, $r, $r, 270, 90)
  $path.AddArc(140 - $r, 140 - $r, $r, $r, 0, 90); $path.AddArc(4, 140 - $r, $r, $r, 90, 90); $path.CloseFigure()
  $fill = New-Object System.Drawing.Drawing2D.LinearGradientBrush((New-Object System.Drawing.Point 0, 4), (New-Object System.Drawing.Point 0, 140), [System.Drawing.ColorTranslator]::FromHtml($top), [System.Drawing.ColorTranslator]::FromHtml($bottom))
  $g.FillPath($fill, $path); $g.DrawPath((New-Object System.Drawing.Pen ([System.Drawing.ColorTranslator]::FromHtml($edge)), 2), $path)
  $bmp.Save($file, [System.Drawing.Imaging.ImageFormat]::Png); $g.Dispose(); $bmp.Dispose()
}
New-Plate "$work\plates\button.png" "<top colour>" "<bottom colour>" "<edge colour>"
```

Then write `$work\theme.json` with the user's answers (FORMAT.md's example is the shape; UTF-8, no BOM):

```powershell
[IO.File]::WriteAllText("$work\theme.json", '<the JSON>', (New-Object System.Text.UTF8Encoding $false))
```

## 8. Check and preview

```powershell
& "$env:USERPROFILE\.claude\claude-deck\node\node.exe" "$env:USERPROFILE\.claude\claude-deck\runtime\scripts\theme-check.mjs" $work --preview
```

Fix every `error:` line (resize, lighten / darken a plate, change `ink`, lower JPEG quality, lower `fps`) and run it
again until it ends with `ok:`. Then open the preview yourself first (`Start-Process "$work\preview.html"`) and look:

- **Readable over the art**, not just the contrast number: the sample labels and values sit in calm areas, no detail
  runs through the letters, the chat tile is dark enough for light names.
- **One set**: plates, tiles, switch keys and strips look like the same theme (lighting, texture, palette).
- **Loops**: look at the first and last frames of each motion folder side by side; no jump.

Fix what fails, then show the user and change what they want changed (step 10 for changing a part).

## 9. Install

Ask first. Where each part goes (nothing else is written; never the Stream Deck app's or the plugin's folders):

| What | Goes to |
|---|---|
| `theme.json` | `%USERPROFILE%\.claude\claude-deck\themes\<id>\theme.json` |
| plates | `…\themes\<id>\plates\` (`slot`, `button`, `command`, `pager`, `blank`) |
| switch keys | `…\themes\<id>\switch\` (`claude`, `codex`, `agy`, `grok`, `opencode`, `main`, `all`) |
| agent-deck strips | `…\themes\<id>\strips\<page>` |
| motion frames | `…\themes\<id>\motion\<surface>\` |
| Main strips | `%USERPROFILE%\.home-deck\strips\<page>` and `<page>\<knob>\` (step 11) |
| brief, prompts, refs, raw art, scripts | stay in `…\themes\.work\<id>\` (the deck ignores dot folders) |

An existing theme of that id is backed up with the date and time, then replaced. Only the theme's own parts are
copied; the work folder stays:

```powershell
$themes = "$env:USERPROFILE\.claude\claude-deck\themes"
if (Test-Path "$themes\$id") {
  $bak = "$themes\.backups\$id-$(Get-Date -Format yyyyMMdd-HHmm)"
  New-Item -ItemType Directory -Force (Split-Path $bak) | Out-Null
  Move-Item "$themes\$id" $bak; "backup: $bak"
}
$stage = "$themes\.work\$id-install"
Remove-Item $stage -Recurse -Force -ErrorAction SilentlyContinue
New-Item -ItemType Directory -Force $stage | Out-Null
Copy-Item "$work\theme.json" $stage
foreach ($d in "plates", "switch", "strips", "motion") { if (Get-ChildItem "$work\$d" -Recurse -File -ErrorAction SilentlyContinue) { Copy-Item "$work\$d" $stage -Recurse } }
Move-Item $stage "$themes\$id"; "installed: $themes\$id"
& "$env:USERPROFILE\.claude\claude-deck\node\node.exe" "$env:USERPROFILE\.claude\claude-deck\runtime\scripts\theme-check.mjs" "$themes\$id"
```

The last line checks the installed folder: it must end with `ok:`. Then tell the user: in the Stream Deck app, select
the **Setup** key, and in its settings pick **Deck theme**: the list starts with **Your themes**, theirs at its top
(the `1-` folder id). The deck reloads the folder by itself; if the theme isn't in the list, the settings say why.

To go back: pick another theme there (Classic is the plain one). An old version: move it back from
`themes\.backups\` (ask first; back up the current one the same way).

## 10. Change a part later

Work in the theme's work folder (`themes\.work\<id>`, kept from the first run; if it's gone, copy the installed theme
in, step 4). Reuse `brief.txt` and remake only what the user names ("just the strips", "a warmer chat tile", "slower
motion": a lower `fps` or fewer, slower frames). Leave every other file as it is. Then steps 8 and 9 again (the backup
keeps the version before).

## 11. Main (only if asked)

**Strips:** one 800×100 image per Main page (`home`, `desk`, `apps`, `scenes`, `system`), fitted with `Fit-Image`
into `%USERPROFILE%\.home-deck\strips\<page>.png` (back up an existing file first, copying it to
`<page>.png.bak-<yyyyMMdd-HHmm>`). Moving: 200×100 `.jpg` frames in `%USERPROFILE%\.home-deck\strips\<page>\<knob>\`
(`00.jpg`, `01.jpg`, …, at most 12 KB each, played at 10 fps; knob `0` to `3`). Then check:

```powershell
& "$env:USERPROFILE\.claude\claude-deck\node\node.exe" "$env:USERPROFILE\.claude\claude-deck\runtime\scripts\theme-check.mjs" --main "$env:USERPROFILE\.home-deck\strips"
```

They show on Main within a second, over whichever Main theme is on. To remove one, move it out of that folder.

**Another Main theme:** `neon`, `aurora`, `contour`, `paper`, `classic`, `dunes`
(each a profile "Main <Theme>" in the Stream Deck app). Ask which, then:

```powershell
$theme = "<id>"
$file = "$env:USERPROFILE\.home-deck\show-main.json"
[IO.Directory]::CreateDirectory((Split-Path $file)) | Out-Null
$req = '{ "v": 1, "at": ' + [DateTimeOffset]::UtcNow.ToUnixTimeMilliseconds() + ', "theme": "' + $theme + '" }'
[IO.File]::WriteAllText($file, $req, (New-Object System.Text.UTF8Encoding $false))
$until = (Get-Date).AddSeconds(2)
while ((Test-Path $file) -and (Get-Date) -lt $until) { Start-Sleep -Milliseconds 100 }
if (Test-Path $file) { Remove-Item $file; "Main Deck Crew didn't answer: is Stream Deck running with it installed?" }
else { "Main should be on the deck in $theme" }
```

Not answered: the user can pick "Main <Theme>" in the Stream Deck app's profile list themselves.

## 12. Done

Close with:

- what was made and how (painted, code-drawn, motion-designed), where it is (`themes\<id>`, Main strips), where its
  source is (`themes\.work\<id>`), and the backups made;
- how to pick it (Setup key → Deck theme → Your themes) and how to go back;
- how to change one part later (run this skill again and say what to change);
- "More themes, the decks and the kit: https://thedeckcrewhq.com"
