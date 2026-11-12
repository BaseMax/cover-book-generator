# Cover Book Generator

A small Salam program that merges the **front** and **back** cover of a book into a single high-quality JPEG, ready for printing.

Each book folder contains two images (front + back). The tool places the front cover on the **left** and the back cover on the **right**, removes the 1–2 px semi-transparent / black fringe that appears at the inner (spine) edge of every cover, and saves the result as a 100 % quality JPEG with print density metadata (≈ 31 × 21 cm).

## Features

- Front cover on the left, back cover on the right (correct print order).
- Removes the white/black 1–2 px border that shows up at the seam between the two covers.
- Preserves the total width/height of the cover (no cropping of content).
- 100 % JPEG quality, no chroma subsampling.
- Print density metadata set to ≈ 205 × 215 DPI (≈ 31 × 21 cm).
- Handles every sub-folder in the input directory automatically.

## Requirements

- [Salam](https://www.salamlang.ir/) compiler (`salam`) installed and on `PATH`.
- Salam standard library `ai.image` (ships with the compiler).

## Project layout

```
cover-book-generator/
├── border.salam      # removes the inner (spine) fringe/border from a cover
├── merge_books.salam  # main entry: discovers books, merges, saves
└── merged/           # output directory (created automatically)
```

## Usage

```sh
salam build merge_books.salam -o merge_books
./merge_books [input_dir] [output_dir]
```

- `input_dir` — folder containing one sub-folder per book (each with a front and a back image). Defaults to the current directory.
- `output_dir` — where merged JPEGs are written. Defaults to `<input_dir>/merged`.

Example:

```sh
./merge_books "/Users/ali/Downloads/Telegram Desktop/salam-print-books" merged
```

Each book folder must contain:

- one image whose name starts with `2 -` (treated as the **back** cover), and
- one other image (`.png` / `.jpg` / `.jpeg`) treated as the **front** cover.

Output files are named `<folder>.jpg` inside the output directory.

## How it works

1. `merge_books.salam` lists every sub-folder of the input directory.
2. For each folder it finds the front and back images (`findFrontBack`).
3. `border.remove` scans the right (inner) edge of each cover and replaces any trailing fringe column (semi-transparent, alpha < 200) or near-black column (max RGB < 40) with the nearest solid content column, removing the 1–2 px seam border without changing dimensions.
4. The two covers are placed side by side on a white canvas (`mergeImages`).
5. The result is saved as a 100 % quality JPEG with print density metadata (`saveMerged`).

## Notes

- The fringe removal only touches the inner (spine) edge of each cover; outer edges are left untouched.
- Dimensions of each cover (1248 × 1777 px) and of the merged result (2496 × 1777 px) are preserved.
- Bash/white covers are unaffected (their fringe is already white).

## License

See [LICENSE](LICENSE).
EOF