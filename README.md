# asciinema-ghost

A tiny, single-file site to scrub Asciinema casts, grab precise start/end times, and generate Ghost-friendly embed snippets. Built around https://asciinema.org.

Hosted version: https://www.generouscorp.com/asciicinema-ghost/

## What it does

- Load one or more `.cast` files (local drag/drop or URL).
- Scrub the timeline and insert the current playhead time into start/end fields.
- Preview the selection, loop it, and apply markers in the player.
- Generate Ghost snippets (HTML + header/footer injections) to paste into a Ghost post.
- Save everything in `localStorage` and export/import a project JSON.

## Recommended workflow (Ghost embeds)

Ghost needs a URL it can fetch, so host your `.cast` files somewhere public (GitHub, S3, or any static file host) and use the raw URL in this tool. Local files are great for preview, but they will not appear in the Ghost snippets.

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
- Local file casts are supported in the tool, but Ghost snippets only include URL casts (Ghost can't fetch `blob:` URLs).

## Appendix: hosting vs. Python

Running a local Python server is optional. Most people can just host `index.html` on any static site and use it from a URL (for example: https://www.generouscorp.com/asciicinema-ghost/). Python is only useful if you want a quick local server or offline asset downloads.
