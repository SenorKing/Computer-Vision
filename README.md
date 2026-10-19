# Computer Vision Projects
A computer vision toolkit built from scratch in C++ and Java, from thresholding and morphology to skeletons, connected components, and line detection.

Image-processing algorithms built from scratch in C++ and Java for CS381 (Computer Vision) at Queens College. No image libraries, just pixels and loops.

## Projects

| # | Project | Language | Topic |
|---|---------|----------|-------|
| 1 | [Histogram and Thresholding](histogram.md) | C++ | Histogram, binary thresholding |
| 2 | [Bi-Gaussian Threshold](project2-bimeans) | C++ | Automatic threshold selection |
| 3 | [Image Enhancement](project3-enhancement) | C++, Java | Average, median, Gaussian filters |
| 4 | [Morphology](project4-morphology) | C++ | Dilation, erosion, opening, closing |
| 5 | [Distance Transform Compression](project5-distance-transform) | Java | 4/8-distance, skeleton, decompression |

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

