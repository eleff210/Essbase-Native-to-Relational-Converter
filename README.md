# Essbase-Native-to-Relational-Converter
Convert an Essbase native-format file to a relational load ready columnar format output using dimension extracts from the Essbase cube as the basis on which to covert from


# ASO Level0 Converter — Web

A single static HTML page that runs the ASO Level0 → relational conversion
entirely in the browser. No server, no upload, no backend — the same
architecture as the POS app: one file, deploy to GitHub Pages, done.

## Deploying

No build step, no dependencies to install, no secrets to configure. The only
external calls the page makes are to Google Fonts (for the two display/mono
typefaces); if you'd rather it work fully offline, delete the two `<link>`
tags in `<head>` and it'll fall back to system fonts.

## How it works

- **Data file dropzone**: your ASO Level0 backup export (the raw quoted,
  whitespace-delimited Essbase text file).
- **Dimension files dropzone**: every per-dimension metadata file (multi-file
  select or drag a whole selection in at once — `Account.txt`, `Entity.txt`,
  `Measure.txt`, etc.). The dimension name is taken from each filename.
- **Options**: delimiter, an optional comma-separated filter
  (`-DataToExtract`), an optional substring filter
  (`-MemberToFilterOnConvertASOLvl0Data`), and a "drop `#Missing` rows"
  checkbox — all the same options as the Python CLI and `.bat` scripts.
- **Convert**: reads both files via the browser's File API, hands them to a
  Web Worker (so a large file doesn't freeze the tab), and the worker runs
  the exact same algorithm as `aso_level0_converter.py` — verified
  byte-for-byte identical against the same real sample data used to validate
  the Python version, including the multi-value filter behavior.
- **Results**: row count, dimensions found, the detected column axis (e.g.
  `Period`), a log excerpt, and two downloads — the converted output file and
  the run log — generated as in-browser Blobs.

Nothing is written anywhere except back to your own downloads folder. The
files you drop in never leave the tab.

## Relationship to the Python CLI

This is a second implementation of the same algorithm as
`aso_level0_converter.py`, ported to JavaScript so it can run client-side on
a static host. Any future change to the parsing rules (column-axis
detection, the PCMCS Rule/Balance fallback, filter semantics, etc.) needs to
be made in both places to keep them in sync — they were verified identical
on the real sample dataset at the time this was built, but there's no shared
code enforcing that going forward.

## Browser file size notes

Files are read and processed entirely in the visitor's browser memory —
nothing is uploaded to GitHub or any server, so GitHub's repo/file size
limits don't apply to the data you convert. The practical ceiling is the
visitor's own machine: normal laptops should handle files in the hundreds of
MB range without issue; very large (multi-GB) files may slow down or strain
a browser tab, since the whole file is read into memory as text before
processing.
