# Binary-Image Morphology

A C++ image-processing project that applies binary morphological operations to a grayscale or binary image using a user-defined structuring element. The program supports dilation, erosion, opening, closing, and a batch mode that runs all four operations.

## Features
- Read a plain-text image file with header information (`numRows numCols minVal maxVal`).
- Read a structuring element file with its dimensions and origin.
- Zero-frame the image so boundary pixels can be processed correctly.
- Compute dilation and erosion using the supplied structuring element.
- Compute opening and closing as erosion-dilation and dilation-erosion pipelines.
- Write output image files and a pretty-printed debug report.
- Produce a runtime log showing each major method entry and exit.

## Requirements
- A C++ compiler (tested with `g++`, C++11 or newer)
- Standard C++ library only

## Build

```bash
g++ -std=c++11 -O2 -o morphology morphology
```

## Usage

```bash
./morphology <inFile> <structFile> <choice> <prettyPrintFile> <logFile>
```

### Arguments
- `inFile`: input image file
- `structFile`: structuring element file
- `choice`: operation to run
  - `1` = dilation
  - `2` = erosion
  - `3` = opening
  - `4` = closing
  - `5` = run all four
- `prettyPrintFile`: output file containing formatted text output for inspection
- `logFile`: runtime log with method calls and diagnostics

Example:

```bash
./morphology data.txt struct.txt 5 prettyPrint.txt morphology.log
```

## Input file format

The input image file is plain text. The first four integers form the header:

```text
numRows numCols minVal maxVal
```

Then the image pixel values follow in row-major order, one integer per pixel.

Example:

```text
42 31 0 1
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
0 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
...
```

The project sample file `data.txt` is a binary image with values `0` and `1`.

## Structuring element format

The structuring element file begins with:

```text
numStructRows numStructCols structMin structMax
rowOrigin colOrigin
```

Then the structuring element values follow in row-major order.

Example 3x3 filled square:

```text
3 3 0 1
1 1
1 1 1
1 1 1
1 1 1
```

This indicates a 3x3 structuring element whose origin is at `(1, 1)` and whose active pixels are all 1s.

## Program behavior

The program does the following:
1. Reads the image header and structuring element header.
2. Allocates all required temporary arrays.
3. Zero-frames the image to allow border processing.
4. Loads the image into the framed array.
5. Loads the structuring element.
6. Runs the selected morphological operation:
   - dilation
   - erosion
   - opening
   - closing
   - all four
7. Writes the processed image to the requested output file.
8. Produces a pretty-printed text version for visual inspection.

## Output files

The program writes:
- output files for the selected operation (`dilationOutFile.txt`, `erosionOutFile.txt`, etc.)
- a pretty-printed textual representation of the resulting image
- a log file with execution details

Each output image file follows the original image format:

```text
numRows numCols minVal maxVal
```
```text
example
44 33 0 1
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 
0 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0 
0 1 1 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0 0 
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 1 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 
0 0 0 0 0 1 1 0 0 0 0 0 0 0 1 1 1 1 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 
0 0 0 0 1 1 1 0 0 0 0 0 0 1 1 1 1 1 1 1 0 0 0 0 0 0 1 1 0 0 0 0 0 
0 0 0 1 1 1 0 0 0 0 0 0 1 1 1 1 1 1 1 1 1 0 0 0 0 0 1 0 0 0 0 0 0 
0 0 0 1 1 1 1 0 0 0 0 1 1 1 1 1 1 1 1 1 1 1 0 0 0 0 0 1 0 0 0 0 0 
0 0 1 1 1 1 0 0 0 0 1 1 1 1 1 1 1 1 1 1 1 1 1 0 0 0 0 1 1 0 0 0 0 
0 0 0 1 1 0 0 0 0 0 1 1 1 1 1 1 1 1 1 1 1 1 1 0 0 0 0 1 0 0 0 0 0 
0 0 0 0 1 1 0 1 0 1 1 1 1 1 1 1 1 1 1 1 1 1 1 0 0 0 0 0 0 0 0 0 0 
0 0 0 0 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 0 0 0 1 0 0 0 0 0 
0 0 0 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 0 0 0 0 
0 0 0 0 1 1 1 0 0 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 0 0 0 0 0 0 0 0 0 
0 0 0 0 0 1 0 0 0 0 1 1 1 1 1 1 1 1 1 1 1 1 1 0 0 0 0 0 0 0 0 0 0 
0 0 0 0 1 0 0 0 0 0 0 1 1 1 1 1 1 1 1 1 1 1 0 1 0 0 0 0 0 0 0 0 0 
0 0 0 0 0 0 0 0 0 0 1 0 0 1 1 1 1 1 1 1 1 0 0 0 1 0 0 0 0 0 0 0 0 
0 0 0 0 0 0 0 0 0 1 0 0 0 0 1 1 1 1 1 1 0 0 0 0 0 1 0 0 0 0 0 0 0 
0 0 0 0 0 0 0 0 1 0 0 0 0 0 0 1 1 1 0 0 1 0 0 0 0 0 1 0 0 0 0 0 0 
0 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 1 0 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 

```

followed by the processed pixel values in row-major order.

## Notes
- The implementation treats nonzero pixels as foreground (`1`-like values).
- Zero padding is used so pixels near the image borders are still processed with the structuring element.
- This project is intended for educational and algorithmic demonstration of morphological processing in C++.
- The code uses dynamic memory allocation and is designed for plain text image input/output rather than a GUI or standard image library.

## Author
Andre King
