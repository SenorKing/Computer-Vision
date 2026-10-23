# Image Enhancement

A Java image-processing project that applies three neighborhood filters to a grayscale image: 3x3 averaging, median filtering, and Gaussian convolution. The program mirrors edge pixels, computes filtered results, applies binary thresholding, and writes separate output files for each stage.

## Features
- Read a plain-text grayscale image file with a header (`numRows numCols minVal maxVal`).
- Mirror-frame the image so edge pixels have a full 3x3 neighborhood.
- Compute a 3x3 average filter and record the new min/max values.
- Compute a 3x3 median filter.
- Compute a Gaussian filter using a user-supplied 3x3 mask.
- Threshold each processed image at a user-defined value.
- Produce debug output in a log file for each step.

## Requirements
- Java JDK 8 or newer
- A plain ASCII image file and a 3x3 mask file in numeric format

## Build
```bash
javac KingA_Project3_main.java
```

## Usage
```bash
java KingA_Project3_main <inFile> <maskFile> <thrVal> <AvgFile> <MedianFile> <GaussFile> <logFile>
```

### Arguments
- `inFile`: input grayscale image file
- `maskFile`: 3x3 Gaussian mask file
- `thrVal`: threshold value between 0 and 255
- `AvgFile`: output file for average-filter results
- `MedianFile`: output file for median-filter results
- `GaussFile`: output file for Gaussian-filter results
- `logFile`: detailed runtime log

Example:
```bash
java KingA_Project3_main input.pgm gaussMask.txt 120 avgOut.txt medianOut.txt gaussOut.txt project3.log
```

## Input file format
The input image file is plain text. The first four integers are:

```text
numRows numCols minVal maxVal
```

Then the pixel values follow in row-major order, one integer per pixel.

Example:
```text
5 5 0 255
10 20 30 40 50
60 70 80 90 100
110 120 130 140 150
160 170 180 190 200
```

## Mask file format
The Gaussian mask file is also plain text and contains a 3x3 matrix.

Example:
```text
3 3 0 255
1 2 1
2 4 2
1 2 1
```

## Program behavior
The program does the following in order:
1. Reads the image header and mask header.
2. Allocates all temporary arrays.
3. Loads the Gaussian mask and computes its total weight.
4. Loads the image into a mirrored frame.
5. Applies the 3x3 average, median, and Gaussian filters.
6. Thresholds each processed image to binary values (`0` and `1`).
7. Writes the final outputs to the requested files.

## Output format
Each output file is pretty-printed with a header line:

```text
rows cols minVal maxVal
```

and then one row per image row with values separated by spaces.

The binary-threshold outputs are printed using a minimum/maximum range of `0 1`:

```text
47 47 0 1
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
...
```

This is the binary threshold result after the median-filtered and Gaussian-filtered images are processed.

## Notes
- Thresholds are validated to be in the range `0..255`.
- The code uses mirrored borders to avoid out-of-bounds access during neighborhood operations.
- The debug log file shows the entry/exit of each major method and the intermediate 3x3 neighbor arrays.

## Author
Written by Andre King in Java for the Computer Vision project sequence.
