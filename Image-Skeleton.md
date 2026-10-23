# Distance Transform and Skeleton Compression

This project is the Java implementation of a distance-transform skeleton pipeline used in a computer vision assignment. The main logic lives in the `distanceSkeleton` class, and the program entry point is the `KingA_Project5_main` class.

The workflow is:

1. Read a grayscale image from a plain-text file.
2. Zero-pad the image so neighbor checks are safe.
3. Apply a two-pass distance transform (`distancePass1` and `distancePass2`).
4. Compute local maxima in the transformed image (`computeLocalMaxima` and `isLocalMaxima`).
5. Extract the skeleton points to a file (`extractSkeleton`).
6. Re-read the skeleton file and reconstruct the original shape (`loadSkeleton` + `deCompression`).
7. Threshold the expanded result and write the final binary image.

## Repository file

The source file in this repo is named:

- `Image-Skeleton`

This is not a standard `.java` filename, so to compile it with the Java toolchain you should copy or rename it before compilation:

```bash
cp Image-Skeleton KingA_Project5_main.java
javac KingA_Project5_main.java
```

The public class in the file is `KingA_Project5_main`, which matches the filename requirement for Java.

## Class layout and method responsibilities

### `distanceSkeleton`

This is the core class that owns the image arrays and all algorithmic steps.

Fields:

- `numRows`, `numCols`, `minVal`, `maxVal`
- `newMinVal`, `newMaxVal`
- `ZFAry` — padded image array, sized `numRows + 2` by `numCols + 2`
- `skeletonAry` — padded array for local-maxima candidates
- `distanceChoice` — either `4` or `8`

Constructor:

```java
public distanceSkeleton(int nRows, int nCols, int iMin, int iMax, int choice)
```

This initializes dimensions, image bounds, and allocates both padded arrays.

Key methods:

- `setZero(int[][] Ary)`
  - Clears the entire padded array to zero.

- `loadImage(Scanner inFile, int[][] ZFAry)`
  - Reads the image pixels into the interior of the padded array.

- `loadSkeleton(Scanner skeletonFile, int[][] ZFAry, PrintWriter logFile)`
  - Reads the skeleton header and then triplets of `i j value` to restore the skeleton into `ZFAry`.

- `distanceTransform(int[][] ZFAry, int distanceChoice, PrintWriter prettyPrintFile, PrintWriter logFile)`
  - Runs the two-pass transform and prints intermediate output.

- `distancePass1(...)`
  - Processes the image from left-to-right and top-to-bottom.
  - Uses neighbors `a, b, c, d` and updates the value with the minimum distance-based cost.

- `distancePass2(...)`
  - Processes the image from right-to-left and bottom-to-top.
  - Uses neighbors `e, f, g, h` and compares against the current pixel value.

- `compression(...)`
  - Calls `computeLocalMaxima(...)` and then extracts the skeleton.

- `isLocalMaxima(int[][] ZFAry, int i, int j, int distanceChoice)`
  - Returns true when a pixel is a local maximum relative to its neighbors.
  - For `distanceChoice == 4`, it checks the 4-neighbor set.
  - For `distanceChoice == 8`, it checks all 8 neighbors.

- `computeLocalMaxima(...)`
  - Writes a local-maxima mask into `skeletonAry`.

- `extractSkeleton(int[][] skeletonAry, PrintWriter skeletonFile, PrintWriter logFile)`
  - Writes the image header and then every nonzero skeleton point as `row col value`.

- `deCompression(...)`
  - Rebuilds a binary-ish image from the extracted skeleton using an expansion pass.

- `expensionPass1(...)` and `expensionPass2(...)`
  - Expand the skeleton outward in two directions to restore the object.

- `expandedValue(...)`
  - Computes the propagated value using the neighbors and distance weights.

- `binThreshold(int[][] ZFAry, PrintWriter deCompressedFile)`
  - Writes a binary representation of the reconstructed image using `1` for positive values and `0` otherwise.

- `prettyPrint(int[][] Ary, PrintWriter prettyPrintFile)`
  - Writes the entire framed array with a header line `rows cols minVal maxVal`.

## Program entry point: `KingA_Project5_main`

The main method validates the command-line arguments and opens all required files:

```java
public static void main(String[] args)
```

Required arguments:

```bash
java KingA_Project5_main <inFile> <distanceChoice> <prettyPrintFile> <skeletonFile> <deCompressedFile> <logFile>
```

### Argument meanings

- `inFile`: source image file
- `distanceChoice`: `4` or `8`
- `prettyPrintFile`: debug pretty-print output from the transform stages
- `skeletonFile`: extracted skeleton file
- `deCompressedFile`: final reconstructed binary image
- `logFile`: algorithm trace and function-by-function debug log

Example:

```bash
java KingA_Project5_main project5-distance-transform-sample-input.txt 8 project5-pretty.txt project5-skeleton.txt project5-decompressed.txt project5-log.txt
```

## Input file format

The input image is a plain-text grayscale image with a header:

```text
numRows numCols minVal maxVal
```

Followed by `numRows * numCols` integer pixel values in row-major order.

Example:

```text
5 5 0 1
0 0 0 0 0
0 1 1 1 0
0 1 1 1 0
0 1 1 1 0
0 0 0 0 0
```

This is a 5x5 binary object that will be processed into a skeleton.

## Distance-transform behavior

The program uses the two-pass method described by the project notes.

### Pass 1

`distancePass1` scans the image left-to-right, top-to-bottom. It uses the current pixel and the previously processed neighbors:

- `a = ZFAry[i - 1][j - 1]`
- `b = ZFAry[i - 1][j]`
- `c = ZFAry[i - 1][j + 1]`
- `d = ZFAry[i][j - 1]`

Then it computes the new value using a distance cost that depends on `distanceChoice`:

```java
if (distanceChoice == 4) {
    ZFAry[i][j] = Math.min(Math.min(a + 2, b + 1), Math.min(c + 2, d + 1));
} else {
    ZFAry[i][j] = Math.min(Math.min(a + 1, b + 1), Math.min(c + 1, d + 1));
}
```

### Pass 2

`distancePass2` scans the image right-to-left, bottom-to-top using the trailing neighbors:

- `e = ZFAry[i][j + 1]`
- `f = ZFAry[i + 1][j - 1]`
- `g = ZFAry[i + 1][j]`
- `h = ZFAry[i + 1][j + 1]`

The method then takes the minimum between those candidate distances and the current value.

## Skeleton extraction details

The skeleton is the set of local maxima in the distance image.

`isLocalMaxima` checks whether a pixel is greater than or equal to all relevant neighbors:

```java
if (distanceChoice == 4) {
    return (p >= b && p >= d && p >= e && p >= g);
}
return (p >= a && p >= b && p >= c && p >= d && p >= e && p >= f && p >= g && p >= h);
```

If the pixel is a local maximum, it is copied into `skeletonAry`; otherwise it is set to zero.

`extractSkeleton` writes the result as:

```text
numRows numCols newMinVal newMaxVal
row col value
row col value
...
```

This output is later read back by `loadSkeleton`.

## Reconstruction and decompression

After the skeleton is saved, the program reopens the file and loads it with `loadSkeleton`.

Then it calls:

```java
skel.deCompression(skel.ZFAry, distanceChoice, prettyPrintFile, logFile);
```

The expansion is performed by:

- `expensionPass1`
- `expensionPass2`
- `expandedValue`

These methods propagate values outward from the skeleton while preserving distance-based decay.

Finally, `binThreshold` converts the resulting numeric image into a binary matrix:

```java
if (ZFAry[i][j] >= 1) {
    line.append("1 ");
} else {
    line.append("0 ");
}
```

The final output file is printed as:

```text
numRows numCols minVal maxVal
0 0 0 0 0
0 0 1 0 0
0 1 1 1 0
0 0 1 0 0
0 0 0 0 0
```

## Pretty-print output

The `prettyPrint` method prints the entire framed array including the padded border. It writes a header like:

```text
rows cols minVal maxVal
```

Then it prints every padded value as a row of numbers separated by spaces.

This is especially useful for debugging the transform and previewing the skeleton before final thresholding.

## Sample run

The repository includes a small sample input/output pair:

- `project5-distance-transform-sample-input.txt`
- `project5-distance-transform-sample-output.txt`

Example command:

```bash
cp Image-Skeleton KingA_Project5_main.java
javac KingA_Project5_main.java
java KingA_Project5_main project5-distance-transform-sample-input.txt 8 project5-pretty.txt project5-skeleton.txt project5-decompressed.txt project5-log.txt
```

## Notes

- The input and output files are plain ASCII numeric files.
- `distanceChoice` must be either `4` or `8`.
- The log file records entry/exit of each method and can be used to debug neighbor calculations.
- The program relies on a padded frame around the image (`+2` rows and `+2` columns) to remove all boundary checks.

## Summary

This project demonstrates a classic distance-transform skeleton pipeline:

- transform the image into distance values,
- locate local maxima,
- keep only the skeleton,
- reconstruct the object from the skeleton,
- and output the binary result.

The implementation is organized around the `distanceSkeleton` class and the `KingA_Project5_main` entry point, which is exactly what the Java source in this repository does.

