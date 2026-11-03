# Thinning

This program performs iterative skeletonization (thinning) on a binary image using a Zhang-Suen-style neighbor-based boundary removal approach. It removes boundary pixels in directional passes (north, south, west, east) until no more pixels can be removed.

## Purpose

The program reads an image from an input file, repeatedly thins the foreground pixels, and writes the result to an output file. It also writes a detailed log showing each directional thinning pass and the changing pixel states.

## Input format

The input file begins with a header:

```text
numRows numCols minVal maxVal
```

Then the image data follows row by row, with `numRows * numCols` integer values.

Example:

```text
5 5 0 1
0 0 0 0 0
0 1 1 1 0
0 1 1 1 0
0 1 1 1 0
0 0 0 0 0
```

In this implementation, non-zero values are treated as foreground pixels and the algorithm thins them to a skeleton while preserving connectivity.

## Command-line usage

```bash
./thinning <inFile> <outFile1> <logFile>
```

Example:

```bash
./thinning input.pgm output.txt log.txt
```

## Output files

- `outFile1`: summary output with image headers and the skeleton after each thinning iteration.
- `logFile`: detailed debug log showing each pass and the conditions used for deletion.

## Algorithm overview

The program uses the following idea for each thinning iteration:

1. Copy the current image into a working array.
2. Process each pixel in four direction passes:
   - north
   - south
   - west
   - east
3. A pixel can be removed if:
   - it is a foreground pixel,
   - the neighbor in the current direction is zero,
   - it has at least 4 non-zero neighbors,
   - it is not a connector pattern.
4. Repeat until no more pixels change.

This is an iterative deletion process that preserves the main skeleton while removing redundant edge pixels.

## Key classes and methods

### `Thinning`

The main class manages:

- image dimensions (`numRows`, `numCols`)
- min/max pixel values
- thinning counters (`changeCount`, `cycleCount`)
- two working arrays: `aryOne` and `aryTwo`
- neighborhood buffer `neighborAry`

### Core methods

- `loadImage(...)` loads pixel data into the internal image array.
- `countNonZeroNeighbors(...)` counts non-zero neighbors in a 3x3 neighborhood.
- `checkConnector(...)` identifies connector-like local patterns that should be protected.
- `northThinning(...)`, `southThinning(...)`, `westThinning(...)`, `eastThinning(...)` perform directional thinning passes.
- `prettyPrint(...)` writes the internal matrix with formatting for debugging.

## Important notes

- The implementation uses a padded array with an extra 2 rows and 2 columns.
- The border is treated as zero-valued padding.
- `changeCount` is used to determine whether any pixels were removed in the last pass.
- The loop continues while `changeCount > 0`.

## Example workflow

1. Read input image and header.
2. Initialize the thinning class.
3. Load the image into `aryOne`.
4. While pixels are removed:
   - run north/south/west/east passes,
   - copy the updated result back,
   - increment cycle count.
5. Output the final skeleton.

## File structure

This code is contained in a single C++ source file:

- `Thinning` (the file in this repository)

## Compile

Use a C++ compiler such as:

```bash
g++ -std=c++17 Thinning -o thinning
```

## Notes on the implementation

This is a practical educational thinning routine based on neighborhood tests and iterative pixel removal. It is not a general-purpose image-processing library; it is intended for experimentation and learning about skeleton extraction in binary images.

## License

This project does not currently declare a formal license in the provided source file. If you plan to reuse or distribute it, confirm the repository's licensing terms before use.
