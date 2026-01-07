# asciinema-ghost

A tiny, single-file site to scrub Asciinema casts, grab precise start/end times, and generate Ghost-friendly embed snippets. Built around https://asciinema.org.

Hosted version: https://www.generouscorp.com/asciicinema-ghost/

## What it does

- Load one or more `.cast` files (local drag/drop or URL).
- Scrub the timeline and insert the current playhead time into start/end fields.
- Preview the selection, loop it, and apply markers in the player.
- Generate Ghost snippets (HTML + header/footer injections) to paste into a Ghost post.
- Capture preview images and use them as the in-player cover plus email fallback.
- Save everything in `localStorage` and export/import a project JSON.
- Enable local drag/drop in **Settings → Local testing** if you want to preview files without URLs.

## Recommended workflow (Ghost embeds)

Ghost needs a URL it can fetch, so host your `.cast` files somewhere public (GitHub, S3, or any static file host) and use the raw URL in this tool. Local files are still useful for preview, but the snippets will only include their filenames, so swap those with hosted URLs before publishing.
Adding multiple `.cast` URLs generates a single post embed with multiple players.

## Useful shortcuts

- `Alt+S` / `Option+S` — insert current time as Start
- `Alt+E` / `Option+E` — insert current time as End
- `Alt+H` / `Option+H` — hide/show the controls panel

## Using the snippets in a Ghost post

There are multiple ways to add code to Ghost, but for a one-off post:

1) Copy the `POST HEADER INJECTION` snippet into the post's **Settings → Code Injection → Post Header** section.
2) Copy the `POST FOOTER INJECTION` snippet into **Settings → Code Injection → Post Footer**.
3) Copy the `POST CONTENT (HTML card)` snippet into an HTML card in the post body.

References:

- https://ghost.org/help/post-settings/#code-injection
- https://ghost.org/help/cards/#html
- Example embed: https://danielraffel.me/2026/01/06/learning-claude-code-plugins-by-building-two/

## Email-friendly fallbacks

Email clients don't execute JavaScript, so the asciinema player won't work in Ghost email previews or newsletters. The tool now generates email-friendly fallbacks:

### Option 1: Auto-detected previews (default)
If your `.cast` URL ends in `.cast`, the tool assumes the preview image sits next to it with a `.png` extension (for example: `https://host/demo.cast` → `https://host/demo.png`).

### Option 2: Capture from player
1. Load your cast and scrub to the frame you want as a preview
2. Click **Capture preview at current time**
3. Choose one:
   - **Download preview image**: Save as PNG, upload next to your `.cast` file, and the preview URL auto-fills from the cast name
   - **Use embedded data URL**: Embeds the image directly in the HTML (larger file size, no hosting needed)

### Option 3: Manual URL
Paste any image URL into the "Preview image URL" field.
You can disable the web cover image per cast by unchecking the **Use preview image as the player poster** toggle.

### How it works
- **In the browser** (web version): JavaScript replaces the fallback with the interactive player and uses the preview image as the cover until play.
- **In emails**: JavaScript is blocked, so email clients show the preview image (or text fallback if no image).

## Why a local server?

Modern browsers apply extra restrictions to `file://` pages. Asciinema Player fetches `.cast` files and can be blocked by those restrictions, depending on the browser. Running a tiny local server avoids those issues and keeps everything working consistently.

### Quick start

From this folder:

```bash
python3 -m http.server 8000
```

Then open:

```
http://localhost:8000/index.html
```

You can use any local server you like (Node, Ruby, etc.). The Python server is just the simplest built-in option.

### Asset helper (optional)

If you want to work offline, run the included helper server once. It will download the Asciinema Player assets into this folder if they are missing, then start a local server.

```bash
python3 server.py
```

To skip the download step:

```bash
python3 server.py --no-fetch
```

## Notes

- The tool tries local player assets first (`asciinema-player.min.js` and `asciinema-player.css`) and falls back to the CDN if they are missing.
- Local file casts are supported in the tool. Snippets include filenames only; replace them with hosted URLs before publishing.

## Appendix: hosting vs. Python

Running a local Python server is optional. Most people can just host `index.html` on any static site and use it from a URL (for example: https://www.generouscorp.com/asciicinema-ghost/). Python is only useful if you want a quick local server or offline asset downloads.
