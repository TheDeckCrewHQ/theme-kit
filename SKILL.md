---
name: deck-theme-kit
description: Make or change your own Deck Crew theme for the Stream Deck+ on Windows (the agent decks' keys and touch strip), and give the Main profile your own strip backgrounds or another of its themes. Interviews you about the look, uses your own images or images your agent's image tool makes, writes the theme folder, checks it, shows a preview and keeps dated backups. Use when the user says "make a deck theme", "theme kit", "my own stream deck theme", "change my deck theme", "custom strip", or wants the deck to match a look, a faceplate or their own art.
---

# Deck Crew theme kit

You are helping the user make their own theme for Deck Crew, the Stream Deck+ plugin for agent CLIs. A theme is a
folder of images plus `theme.json`; the format is in `FORMAT.md` next to this file (if it isn't there, the public copy
is at https://github.com/thedeckcrewhq/theme-kit/blob/main/FORMAT.md). Read FORMAT.md before you write anything. Go one
step at a time: say what you're about to do, do it, report in a line or two, ask, move on.

## Hard rules

- **Art comes from two places only:** files the user gives you, or images your own image tool makes from a prompt you
  write. Never download, search for or fetch images from the web, and never copy logos, characters or artwork the user
  doesn't own. If you have no image tool and the user has no art, say so and offer the code-drawn plates in step 4.
- **Touch only these:** `%USERPROFILE%\.claude\claude-deck\themes\` (themes, `.work\`, `.backups\`),
  `%USERPROFILE%\.home-deck\strips\` (Main's strips) and, only when the user asks for a Main theme,
  `%USERPROFILE%\.home-deck\show-main.json`. Never read, explain, modify or copy the plugins' own files (anything in
  their plugin folders, or `runtime\`, `node\`, `bin\`, `hooks\`, `scripts\`), never edit Stream Deck's profiles.
  Running the theme checker as written below is the one exception: run it, don't open it.
- **Ask before each change on disk** (one question per change; "no" means skip it). Before replacing anything, make a
  dated backup (step 6).
- Run only the commands written here, filling in only the `<...>` parts. Windows PowerShell; no encoded commands.

## 1. Check

```powershell
$deck = "$env:USERPROFILE\.claude\claude-deck"
$node = "$deck\node\node.exe"; $check = "$deck\runtime\scripts\theme-check.mjs"
"node: $(Test-Path $node)  checker: $(Test-Path $check)"
Get-ChildItem "$deck\themes" -Directory -ErrorAction SilentlyContinue | Where-Object Name -notlike ".*" | ForEach-Object Name
```

- Both `True`: go on. List the user's existing themes (the names printed) and ask: a new theme, or change one of those?
- Either `False`: Deck Crew isn't set up yet (it puts both there). Ask the user to start Stream Deck with Deck Crew
  installed and press its Setup key once (or run the `deck-setup` skill), then check again.

## 2. Interview

Ask, a few at a time, and suggest answers:

1. **Name** (shown in the theme list) and a folder id (`slate`, `night-garden`: lowercase, digits, `-`).
2. **The look** in a few words, and any references the user has: images, a photo of their faceplate, a screenshot of
   a look they like. Ask for file paths; look at the images yourself.
3. **Colours:** a dark backdrop colour (`bg`), the text colour (`ink`, light on dark plates), up to three chat tile
   rim colours (`accents`: idle, working, done; waiting and error stay the deck's red / amber).
4. **Font:** Segoe UI, Cambria, Trebuchet MS or Bahnschrift.
5. **Which surfaces:** every plate, the deck switch keys, strips per page; still or moving (motion: chat keys, command
   keys, switch keys, strips; subtle loops read best).
6. **Main too?** Their own strip backgrounds for Main's pages, and/or another Main theme.

Summarise the plan (surfaces, sizes, colours, motion) and ask to go ahead.

## 3. Art

- **User's files:** copy them into the work folder (step 4) and fit them (step 5).
- **Your image tool:** write one prompt per surface: square 144×144 key plates (dark, calm in the bottom third where
  labels go, no text, no logos), 800×100 strips (calm in each 200 px quarter's top and middle), in the user's style.
  Generate, look at each result, redo what doesn't fit. Ask the user to approve the set before going on.
- For motion, make the frames of one loop (e.g. 24 frames at 12 fps = 2 s) that start and end alike.

## 4. Work folder

Build the theme in a work folder the deck ignores, so a half-made theme never shows:

```powershell
$id = "<id>"
$work = "$env:USERPROFILE\.claude\claude-deck\themes\.work\$id"
New-Item -ItemType Directory -Force "$work\plates", "$work\switch", "$work\strips", "$work\motion" | Out-Null
$work
```

Editing an existing theme: copy it in first, `Copy-Item "$env:USERPROFILE\.claude\claude-deck\themes\$id\*" $work -Recurse -Force`.

**No art at all:** plain plates drawn here (a rounded card in two colours with a thin edge), one file per plate:

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

## 5. Fit images

Every image must be exactly the size FORMAT.md gives. This crops an image to the target's shape (centred) and scales
it; `.png` keeps transparency, `.jpg` is saved at the quality given (lower it if the check says a file is too big):

```powershell
Add-Type -AssemblyName System.Drawing
function Fit-Image($src, $dst, $w, $h, $quality = 85) {
  $img = [System.Drawing.Image]::FromFile($src)
  $scale = [Math]::Max($w / $img.Width, $h / $img.Height)
  $cw = [int]($w / $scale); $ch = [int]($h / $scale)
  $cx = [int](($img.Width - $cw) / 2); $cy = [int](($img.Height - $ch) / 2)
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

Then write `$work\theme.json` with the user's answers (FORMAT.md's example is the shape; UTF-8, no BOM):

```powershell
[IO.File]::WriteAllText("$work\theme.json", '<the JSON>', (New-Object System.Text.UTF8Encoding $false))
```

## 6. Check, preview, install

```powershell
& "$env:USERPROFILE\.claude\claude-deck\node\node.exe" "$env:USERPROFILE\.claude\claude-deck\runtime\scripts\theme-check.mjs" $work --preview
```

Fix every `error:` line (resize, lighten / darken a plate, change `ink`, lower JPEG quality) and run it again until it
ends with `ok:`. Then open the preview for the user (`Start-Process "$work\preview.html"`), look at it with them and
change what they want changed.

Install (ask first). An existing theme of that id is backed up with the date and time, then replaced:

```powershell
$themes = "$env:USERPROFILE\.claude\claude-deck\themes"
if (Test-Path "$themes\$id") {
  $bak = "$themes\.backups\$id-$(Get-Date -Format yyyyMMdd-HHmm)"
  New-Item -ItemType Directory -Force (Split-Path $bak) | Out-Null
  Move-Item "$themes\$id" $bak; "backup: $bak"
}
Remove-Item "$work\preview.html" -ErrorAction SilentlyContinue
Move-Item $work "$themes\$id"; "installed: $themes\$id"
```

Tell the user: in the Stream Deck app, select the **Setup** key, and in its settings pick **Deck theme → Your themes →
<name>**. The deck reloads the folder by itself; if the theme isn't in the list, the settings say why.

To go back: pick another theme there (Classic is the plain one). An old version: move it back from
`themes\.backups\` (ask first; back up the current one the same way).

## 7. Main (only if asked)

**Strips:** one 800×100 image per Main page (`home`, `desk`, `apps`, `scenes`, `system`), fitted with `Fit-Image`
into `%USERPROFILE%\.home-deck\strips\<page>.png` (back up an existing file first, copying it to
`<page>.png.bak-<yyyyMMdd-HHmm>`), then checked:

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

## 8. Done

Close with:

- what was made and where (`themes\<id>`, Main strips), and the backups made;
- how to pick it (Setup key → Deck theme → Your themes) and how to go back;
- "More themes, the decks and the kit: https://thedeckcrewhq.com"
