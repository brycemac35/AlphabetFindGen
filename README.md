[README.md](https://github.com/user-attachments/files/33309454/README.md)
# Letter & Number Sheet Generator

A single-file web app that generates randomized, print-ready practice sheets (letters and numbers) and exports them as PDF from Safari on macOS. No build step, no dependencies, no server.

## Quick start

1. Open `sheet-generator.html` in Safari (double-click it, or `open sheet-generator.html`).
2. Choose sheet types, count, theme, and font in the left panel. The preview updates live.
3. Click **Save as PDF…**, then in the print dialog use the **PDF** menu (bottom-left) and choose **Save as PDF**.
   - Paper: **US Letter**, scale **100%**
   - Turn **off** "Print headers and footers"
   - Turn **on** "Print backgrounds" if colors are missing

## Features

| Feature | Details |
|---|---|
| Sheet types | Capital letters (5 cols), lowercase letters (5 cols), capital + lowercase mixed (7 cols), numbers 1–20 (4 cols) |
| Randomization | Seeded PRNG (mulberry32). Same seed gives the same sheets; **Shuffle** picks a new seed. Seed is printed in each sheet footer |
| Multiple sheets | 1–20 sheets per type, one per US Letter page, each a different shuffle |
| Themes | Rainbow, Ocean, Sunset, Forest, Black & white (ink saver) |
| Fonts | SF Rounded, Avenir Next, Futura, Helvetica Neue, Trebuchet, Georgia, Chalkboard |
| Options | Name/date lines on or off; optionally leave out `U` and `l` (look-alike characters) |

## Project structure

```
sheet-generator.html   # everything: HTML, CSS, and JS in one file
README.md              # this file
```

Inside `sheet-generator.html`:

- **`<style>`**: control panel styles (screen only) and sheet styles. `@page { size: letter; margin: 0 }` plus an `@media print` block hides the panel and removes the preview scaling.
- **`TYPES`**: defines each sheet type (title, column count, and a function returning its characters).
- **`THEMES`**: accent gradient (`a1`, `a2`) and a list of `[background, border]` cell color pairs, cycled by column.
- **`render()`**: reads the controls, builds each sheet's HTML, and writes it into `#pages`. Called on every control change.
- **`GRID_W` / `GRID_H` / `GAP`** (inches): used to compute character font size so cells scale with the grid.

## Design constraints (please preserve)

- **Single file, zero dependencies.** No npm, bundlers, or external fonts/CDNs. It must work offline by double-clicking.
- **Page size:** each `.sheet` is `8.5in x 10.95in` (slightly under 11in on purpose, so Safari doesn't spill a blank page). Don't set it to exactly `11in`.
- **Print fidelity:** keep `-webkit-print-color-adjust: exact` so cell colors print.
- **Preview scaling** uses CSS `zoom` on screen only (Safari supports it). It must be removed in `@media print`.
- **Fonts** must be macOS system fonts with fallbacks.
- **Determinism:** the same seed + options must always produce identical sheets. Don't use `Math.random()` inside `render()` (it's only used to pick a new seed on load and on **Shuffle**).

## Known limitations

- Safari ignores some `@page` rules, so the user must pick US Letter and turn off headers/footers in the print dialog.
- In most sans-serif fonts, capital `I` and lowercase `l` look nearly identical. Georgia is the clearest option, or use "Leave out U and l".
- The last row of the grid can be partial (for example, 26 letters in 5 columns leaves one cell in the final row).

## Ideas for improvement

Good first tasks for Claude Code, roughly easiest to hardest:

1. **Paper size option**: add A4 alongside US Letter (adjust `.sheet` size and `GRID_H`).
2. **Answer key page**: optional page after each sheet listing characters in reading order.
3. **Custom number range**: let the user choose the range (for example 1–50, 0–9) and the column count.
4. **Custom character sets**: let the user type their own characters (for example only `b d p q`, or sight words).
5. **Column control**: per-sheet-type column count so the last row can be filled evenly.
6. **Timer box**: add a "Time: ____  Errors: ____" box in the footer for fluency practice.
7. **Easy-to-confuse mode**: avoid placing look-alike characters (`b/d`, `p/q`, `I/l`) next to each other.
8. **Dark/high-contrast preview** and keyboard shortcuts (`Cmd+P`, `R` to shuffle).
9. **Save settings** to `localStorage` so options persist between sessions.
10. **Direct PDF download** without the print dialog (for example by bundling a small PDF library inline), if it can stay offline and single-file.

## Testing checklist

After any change, check in **Safari on macOS**:

- [ ] Preview renders for every sheet type and every theme
- [ ] Same seed gives identical output after a reload
- [ ] Print preview shows exactly 1 sheet per page, with no blank pages and no clipped edges
- [ ] Colors appear in the saved PDF (with "Print backgrounds" on)
- [ ] The control panel is absent from the PDF
- [ ] Sheet count in the footer ("Sheet N of M") is correct
- [ ] Characters stay large and centered in cells for the 4-, 5-, and 7-column layouts

## Code style

- Plain modern JavaScript (ES2015+), no frameworks.
- Keep functions small; keep data (`TYPES`, `THEMES`) separate from rendering logic.
- Use inches for anything that affects print layout; use `px` only for on-screen panel UI.
- Comment *why*, not *what*, especially around print and Safari quirks.

## Instructions for Claude Code

- Read `sheet-generator.html` fully before editing; it is the whole project.
- Make small, focused changes and keep the file self-contained.
- Don't add dependencies, a build system, or extra files unless asked.
- After editing, open the file in a browser and verify with the testing checklist above.
- Explain any print-related change (page size, margins, zoom), since those are the easiest things to break.

## License

Add a license of your choice (for example MIT) before publishing.
