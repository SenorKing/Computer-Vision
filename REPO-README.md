# Computer Vision Projects
A computer vision toolkit built from scratch in C++ and Java, from thresholding and morphology to skeletons, connected components, and line detection.

Image-processing algorithms built from scratch in C++ and Java for CS381 (Computer Vision) at Queens College. No image libraries, just pixels and loops.

## Projects

| # | Project | Language | Topic |
|---|---------|----------|-------|
| 1 | [Histogram and Thresholding](histogram.md) | C++ | Histogram, binary thresholding |
| 2 | [Bi-Gaussian Threshold](bimeans_gaussian.md) | C++ | Automatic threshold selection |
| 3 | [Image Enhancement](Image-Enhancement.md) | C++, Java | Average, median, Gaussian filters |
| 4 | [Morphology](morphology.md) | C++ | Dilation, erosion, opening, closing |
| 5 | [Image Skeleton and Image Compression](Image-Skeleton.md) | Java | 4/8-distance, skeleton, decompression |

### Image Morphology
Image Morphology is a C++ project for binary morphological processing on grayscale or binary images using a user-defined structuring element. It supports dilation, erosion, opening, closing, and batch execution of all four operations.

Key features:
- Reads plain-text image and structuring-element files
- Zero-frames the image so border pixels are processed correctly
- Computes dilation, erosion, opening, and closing
- Writes processed output images plus debug and runtime log files
- Uses nonzero pixels as foreground and supports custom structuring elements

Build:
```bash
g++ -std=c++11 -O2 -o morphology morphology
```

Run:
```bash
./morphology <inFile> <structFile> <choice> <prettyPrintFile> <logFile>
```

Operation choices:
- `1` = dilation
- `2` = erosion
- `3` = opening
- `4` = closing
- `5` = run all four

For the complete specification, examples, and file formats, see [morphology.md](morphology.md).

## Running a project

C++:
```
g++ -std=c++11 -o program main.cpp
./program <input files> <output files>
```

Java:
```
javac Main.java
java Main <input files> <output files>
```
Each project's README has its exact command.

