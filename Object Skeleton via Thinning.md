# Object Skeleton via Thinning

This program implements a thinning-based skeleton extraction algorithm for a binary image. It repeatedly removes border pixels while preserving the important structure of the object, producing a one-pixel-wide skeleton representation.

The implementation is written in C++ and uses a manual 2D array approach with directional thinning passes (north, south, west, east), connectivity checks, and a progress log for debugging.

## Overview

The code reads a simple text-based image file, processes it to thin foreground pixels, and writes:

- the final skeleton image
- a detailed log of thinning steps

This is useful for shape analysis, feature extraction, and skeletonization tasks in computer vision.

## Input Image Format

The program expects the input file to begin with:

- number of rows
- number of columns
- minimum pixel value
- maximum pixel value

Then it reads the pixel matrix in row-major order.

Example:

```text
10 10 0 1
0 0 0 0 0 0 0 0 0 0
0 1 1 1 0 0 0 0 0 0
0 1 1 1 0 0 0 0 0 0
0 1 1 1 0 0 0 0 0 0
0 0 0 0 0 0 0 0 0 0
...
```

In this program:
- values greater than 0 are treated as foreground/object pixels
- 0 is treated as background

## Program Behavior

The program performs the following:

1. Reads the image header and pixel data
2. Creates a framed array so neighbor checks are easier
3. Repeatedly applies directional thinning:
   - north
   - south
   - west
   - east
4. Uses neighborhood counting and connector detection to avoid removing important pixels
5. Stops when no changes are made during a full thinning cycle
6. Writes the final output and a verbose log

## Files

The main source code is a single C++ file:

- `Object skeleton via thinning`

It uses:
- `argv[1]` = input image file
- `argv[2]` = output file
- `argv[3]` = log file

## Compiling

Use any C++ compiler, for example:

```bash
g++ -std=c++17 "Object skeleton via thinning" -o skeleton
```

## Running

```bash
./skeleton input.txt output.txt log.txt
```

Example:

```bash
./skeleton image.dat skeleton_output.txt thinning_log.txt
```

## Output

### Output file
The output file contains:
- a header echo
- the processed image result
- the final skeleton

### Log file
The log file includes:
- each thinning pass
- intermediate arrays
- per-pixel decisions
- change counts and cycle counts

This makes the algorithm easy to trace and debug.

## Algorithm Notes

The implementation is based on iterative boundary thinning:

- each pixel is considered for removal only if it is foreground
- it must have a neighbor on the current thinning side set to zero
- it must have at least four non-zero neighbors
- it must not be classified as a connector

This helps preserve connectivity while gradually reducing object thickness.

## Important Notes

- This is designed for binary or thresholded images.
- Foreground is treated as any positive value.
- The code uses dynamically allocated arrays and manual memory cleanup.
- It is educational and easy to inspect rather than optimized for large production image processing workloads.

## Example Use Case

This code can be used to reduce a binary object to its medial skeleton for tasks such as:

- shape recognition
- contour simplification
- feature extraction
- object analysis

## License

This repository does not appear to include a formal license file. Please check the repository itself for specific licensing terms before using it in a public or commercial project.

## Author

This code appears to be a custom C++ implementation for a computer vision thinning exercise or assignment.
