# Data preparation (not part of the website)

How the files in `data/` were made. This file is not rendered, because `_quarto.yml` only
renders `*.qmd`. Numbers in the tutorial text depend on these files: if a source file changes
upstream, decide whether to update the copy, and if so, check the numbers in the text again.

## Sources

| File(s) in `data/` | Source | Source version | Copied |
|---|---|---|---|
| `Growth_Data.txt` | R_cookbook repo, `data/Growth_Data.txt` | commit `4da730d` (2026-10-19) | 2026-10-28 |
| 11 `*.fasta` files (16S sequences) | MicEco2025 repo, `data/*.fasta.gz` (https://github.com/ndombrowski/MicEco2025/tree/main/data) | last commit on `data/`: `e66b6eb` (2026-03-24) | 2026-10-28 |
| `accession_to_genus.tsv` | MicEco2025 repo, `data/accession_to_genus.tsv` | as above | 2026-10-28 |
| `accession_to_genus_windows.tsv` | made from `accession_to_genus.tsv` (Windows line endings) | | 2026-10-28 |
| `Growth_Data_dutch.csv` | made from `Growth_Data.txt` (semicolons, decimal commas) | | 2026-10-28 |

Changes to the source files:

- FASTA files: unpacked (`.fasta.gz` to `.fasta`), so readers do not need `zcat`.
- `accession_to_genus.tsv`: added the missing newline at the end of the last line.
  The rows `GCF_000714595_1` and `GCF_000714595_2` are kept on purpose: they match no
  FASTA file and serve as a realistic example when checking a join.
- `accession_to_genus_windows.tsv`: the same table with Windows line endings (CRLF),
  to teach the `\r` trap.
- `Growth_Data_dutch.csv`: the growth table with `;` between the columns and decimal commas,
  for the sed page (another column separator, and changing it back with one sed command).

## Commands

Run in Git Bash, from the `data/` folder of this repo (2026-10-28).

```bash
# Growth data: copy from the R_cookbook repo (a sibling folder of this repo)
cp -n '../../R_cookbook/data/Growth_Data.txt' .

# 16S sequences and genus table: download from MicEco2025
for name in GCF_000009425 GCF_000253315 GCF_000714595 GCF_014058685 LjRoot303 LjRoot44 LjRoot59 LjRoot60 Root401 Root68 Root935; do
  curl -sS -L -o "${name}.fasta.gz" "https://raw.githubusercontent.com/ndombrowski/MicEco2025/main/data/${name}.fasta.gz"
done
curl -sS -L -o accession_to_genus.tsv 'https://raw.githubusercontent.com/ndombrowski/MicEco2025/main/data/accession_to_genus.tsv'
ls -l

# Unpack the FASTA files
gzip -d *.fasta.gz
ls -l

# Add the missing newline at the end of the genus table
printf '\n' >> accession_to_genus.tsv
wc -l accession_to_genus.tsv          # 13
tail -c 30 accession_to_genus.tsv | cat -A

# Make the copy with Windows line endings
sed 's/$/\r/' accession_to_genus.tsv > accession_to_genus_windows.tsv
file accession_to_genus*.tsv          # the _windows file: "with CRLF line terminators"
```

Checks after these steps (2026-10-28):

- all files except `accession_to_genus_windows.tsv` are "ASCII text" (LF line endings)
- `Growth_Data.txt`: 595 lines, 15,811 bytes
- sequences per FASTA file (`grep -c '>'`): 6, 6, 9 and 7 in the `GCF_*` files (in the order of
  the loop above), 1 in each `Root*`/`LjRoot*` file, 35 in total
- FASTA sequences are wrapped at 80 letters per line
- `accession_to_genus.tsv`: 321 bytes; `accession_to_genus_windows.tsv`: 334 bytes (13 lines, one `\r` each)

### Added later: the semicolon copy of the growth table

Run in Git Bash, from the `data/` folder of this repo (2026-10-28), after the folder layout below
was made. Then build the `.tar.gz` again (see below).

```bash
# Growth_Data.txt has no , or ; yet, so the change can be undone exactly
grep -c ',' awk_data/Growth_Data.txt   # 0
grep -c ';' awk_data/Growth_Data.txt   # 0

# Decimal point to decimal comma, tab to semicolon
sed 's/\./,/g; s/\t/;/g' awk_data/Growth_Data.txt > awk_data/Growth_Data_dutch.csv
wc -l awk_data/Growth_Data_dutch.csv                      # 595
file awk_data/Growth_Data_dutch.csv                       # ASCII text
awk -F';' '{ print NF }' awk_data/Growth_Data_dutch.csv | sort | uniq -c   # 595 lines with 5 fields

# Changing it back gives the original file
sed 's/,/./g; s/;/\t/g' awk_data/Growth_Data_dutch.csv | cmp - awk_data/Growth_Data.txt && echo 'round trip identical'
```

Checks (2026-10-28): 595 lines, 15,811 bytes (same as `Growth_Data.txt`), LF line endings,
empty fields kept (line 123: `high_P;mock;ex61;;18,2`), round trip identical.

## Folder layout and the reader download

Readers download one file, `data/awk_data.tar.gz`, and unpack it into a folder `awk_data/`.
The unpacked files are kept in the repo in `data/awk_data/`. After any change to a file in
`data/awk_data/`, build the `.tar.gz` again.

Run in Git Bash, from the `data/` folder of this repo (2026-10-28):

```bash
# Move the 14 data files into their own folder, so readers get a folder when they unpack
mkdir awk_data
mv -n *.fasta *.tsv Growth_Data.txt awk_data/
ls awk_data | wc -l                   # 14

# Pack the folder into one file for readers to download
tar -czf awk_data.tar.gz awk_data
ls -l awk_data.tar.gz                 # 9,573 bytes on 2026-10-28
tar -tzf awk_data.tar.gz              # awk_data/ and the 14 files
```

Check (2026-10-28): unpacked in `_tests/`, all 14 files have the same md5 checksum as the
files in `data/awk_data/`, and only `accession_to_genus_windows.tsv` has CRLF line endings.

Rebuilt (2026-10-28, Git Bash, same `tar -czf` command) after adding `Growth_Data_dutch.csv`:
15 files, 13,289 bytes. Same checks passed (md5 of all 15 files, CRLF only in the `_windows` file).
Build the `.tar.gz` in Git Bash, not in WSL on the Windows folder (`/mnt/c/...`): a WSL build
stored all files as `rwxrwxrwx` (executable), while Git Bash stores them as `rw-r--r--`.
Check with `tar -tzvf awk_data.tar.gz | head -3`.

## Line endings and git

`.gitattributes` (repo root) holds `data/** -text`, so git never changes line endings in `data/`.
This matters because this computer has `core.autocrlf=true`. `.gitattributes` must be committed
together with, or before, `data/`. After cloning the repo on another computer, check with
`file data/awk_data/*` that only the `_windows` file has CRLF line endings.
