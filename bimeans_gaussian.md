# Bi-Gaussian Thresholding

This project implements an automatic threshold-selection method based on fitting a bi-Gaussian model to a grayscale histogram. It is written in C++ and is designed to read a histogram file, search for the best threshold value, and output both the fitted Gaussian curve and a progress visualization.

## Overview

The program:
- reads the image/histogram header
- loads histogram counts into an array
- scans candidate threshold positions
- fits two Gaussian curves to the left and right sides of the split point
- selects the threshold that minimizes the combined fitting error
- writes textual output files for the histogram, progress, Gaussian fit, and runtime logs

## Files

- `bimeans_gaussian` — C++ implementation
- `bimeans_gaussian.md` — project documentation (this file)

## Build

Compile with:

```bash
g++ -std=c++11 -O2 -o bimeans_gaussian bimeans_gaussian.cpp
```

If the source file is named exactly `bimeans_gaussian`, you can compile it directly as shown above. If your local filename differs, replace it in the command.

## Usage

```bash
./bimeans_gaussian <inFile1> <histFile> <progressFile> <GaussFile> <logFile>
```

### Arguments

- `inFile1`: input histogram file
- `histFile`: output file for the raw histogram counts
- `progressFile`: output file with iterative progress charts
- `GaussFile`: output file for the fitted Gaussian curves and final threshold
- `logFile`: runtime diagnostic log

## Input format

The input file begins with a header:

```text
numRows numCols minVal maxVal
```

Then it contains one histogram entry per gray level:

```text
idx val
```

for every index from `minVal` to `maxVal`.

Example:

```text
64 64 0 63
0 8
1 15
2 30
3 41
4 52
5 68
6 0
7 0
...
```

The program uses `idx` as the histogram bin and `val` as the frequency count for that bin.

## Output files

### Histogram output

The histogram file contains the image header and a list of frequency values:

```text
64 64 0 63
0 8
1 15
2 30
...
63 2
```

### Progress graph

The progress file includes the iterative threshold-search process. Each candidate threshold produces a graph showing the histogram values and the current Gaussian approximation.

Sample output excerpt:

```text
*** below is the progress graph: dividePt = 56 ***
64 64 0 63
++++++++++                                @
++++++++++++++                                 @
+++++++++++++++++                                   @
...
```

### Best-fit Gaussian output

The Gaussian output shows the selected threshold value and the best fitted Gaussian curve:

```text
*** The selected threshold value = 56 ***
*** Below is the best Fitted Gaussians ***
64 64 0 63
...
*** Below is the graphic display of BestFitGaussAry ***
64 64 0 63
***
*****
*********
...
```

## How the algorithm works

The method repeatedly tries a split point `dividePt` between the grayscale range and evaluates the approximation error for two Gaussian curves:

1. Fit one Gaussian to the left portion of the histogram
2. Fit a second Gaussian to the right portion
3. Sum the absolute differences between the histogram and the fitted curves
4. Keep the split point with the smallest total error
5. Record that split as the best threshold

Internally the program computes:
- the mean and variance for each side of the split
- the maximum histogram height in the current interval
- a modified Gaussian curve using an exponential model

## Example output

Below is a representative sample from the project:

```text
*** below is the progress graph: dividePt = 56 ***
64 64 0 63
++++++++++                                @
++++++++++++++                                 @
+++++++++++++++++                                   @
++++++++++++++++++++                                     @
++++++++++++++++++++++                                        @
+++++++++++++++++++++++++++++++                                     @
++++++++++++++++++++++++++++                                              @
+++++++++++++++++++++++++++++++++                                                @
+++++++++++++++++++++++++++++++++++++++++++++                                          @
...

*** Below is the graphic display of BestFitGaussAry ***
64 64 0 63
***
*****
*********
**************
***********************
**********************************
************************************************
******************************************************************
...
```

## Notes

- The threshold selected by this method is automatic and driven by the histogram shape.
- The output files are intentionally text-based and designed for debugging and visualization.
- This project is a histogram-based thresholding utility rather than a full image-processing pipeline.

## Author

This documentation was prepared for the Bi-Gaussian thresholding project in the `Computer-Vision` repository.
