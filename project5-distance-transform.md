# Project 5: Distance Transform and Skeleton Compression

This project implements a 4-neighbor or 8-neighbor distance transform, extracts a skeleton by finding local maxima, and then reconstructs the binary image by expanding the skeleton back out. The implementation is written in Java and is the fifth project in the Computer Vision sequence.

## Files in this project

- `Image-Skeleton` — Java source for the distance-transform and skeleton logic
- `project5-distance-transform-sample-input.txt` — sample grayscale input image
- `project5-distance-transform-sample-output.txt` — sample output after skeleton reconstruction

## Requirements

- Java JDK 8 or newer
- A plain-text grayscale image file in the form:

```text
numRows numCols minVal maxVal
pixel1 pixel2 pixel3 ...
```

## Build

The source file in this repository is stored as `Image-Skeleton` without a `.java` extension. If your environment requires a standard Java filename, rename it to `KingA_Project5_main.java` before compiling:

```bash
javac KingA_Project5_main.java
```

## Usage

```bash
java KingA_Project5_main <inFile> <distanceChoice> <prettyPrintFile> <skeletonFile> <deCompressedFile> <logFile>
```

### Arguments

- `inFile`: input image file
- `distanceChoice`: `4` or `8`
- `prettyPrintFile`: debug output showing intermediate distance-transform stages
- `skeletonFile`: extracted skeleton as `(row col value)` triplets
- `deCompressedFile`: reconstructed binary image after expansion
- `logFile`: run log and function-trace output

### Example

```bash
java KingA_Project5_main project5-distance-transform-sample-input.txt 8 project5-pretty.txt project5-skeleton.txt project5-decompressed.txt project5-log.txt
```

## Input format

The program expects a plain ASCII image file with a header and then a row-major list of pixel values.

Example:

```text
5 5 0 1
0 0 0 0 0
0 1 1 1 0
0 1 1 1 0
0 1 1 1 0
0 0 0 0 0
```

This represents a small filled object in a 5x5 image.

## Sample output

A typical decompressed output for a compact object looks like this, written in the same `numRows numCols minVal maxVal` style as the input:

```text
5 5 0 1
0 0 0 0 0
0 0 1 0 0
0 1 1 1 0
0 0 1 0 0
0 0 0 0 0
```

This is a binary approximation of the reconstructed skeleton after expansion.

## How it works

1. Zero-pad the image so neighbors can be read without bounds checks.
2. Run a two-pass distance transform.
3. Detect local maxima as candidate skeleton points.
4. Write the skeleton to the output file.
5. Expand the skeleton back outward to reconstruct the shape.
6. Threshold the expanded result to binary form.

## Notes

- `distanceChoice` must be either `4` or `8`.
- The code writes a detailed log of each stage to the log file.
- The sample files included in this repository are intentionally small and easy to inspect by hand.

