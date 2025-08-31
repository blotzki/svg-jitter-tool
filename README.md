# SVG Jitter Tool

This tool applies a "jitter" effect to SVG paths, making them appear hand-drawn or sketch-like while preserving the original curve characteristics.

## Features

- Apply jitter effect to SVG paths with adjustable amplitude
- Subdivide paths for more detailed jitter effects
- Preserve curve characteristics (Bezier curves, etc.)
- Toggle between straight lines and smooth curves
- Drag and drop SVG file support
- Dark mode support
- Export functionality

## Problem Fixed

Previously, the tool had an issue where it would ignore the direction handles or curves of the original SVG. For example, a ball made with two anchor points would become a line (or two lines on top of each other) instead of maintaining its curved shape.

### Root Cause

The original implementation treated all curve commands (C, c, Q, q) as straight lines between start and end points, completely ignoring the control points that define the curve shape.

### Solution

1. **Proper Curve Subdivision**: 
   - Implemented proper subdivision functions for both cubic and quadratic Bezier curves
   - These functions use the mathematical formulas for Bezier curves to generate intermediate points

2. **Accurate Length Calculation**:
   - Updated the length calculation for curves to use the distances between control points as an approximation
   - This provides better subdivision based on the actual curve complexity

3. **Enhanced Path Processing**:
   - Modified the path processing functions to properly handle Bezier curves instead of treating them as straight lines
   - Maintained relative and absolute coordinate handling for all curve types

4. **Improved Curve Reconstruction**:
   - Enhanced the `pointsToPathData` function to create smoother curves when converting back to SVG path data
   - Implemented better control point calculation that considers the direction from previous to current point and from current to next point

## How It Works

1. **Path Parsing**: The tool parses the SVG path data and identifies all commands (M, L, C, Q, etc.)

2. **Curve Subdivision**: For each curve segment:
   - Cubic Bezier curves (C/c) are subdivided using the cubic Bezier formula
   - Quadratic Bezier curves (Q/q) are subdivided using the quadratic Bezier formula
   - The number of subdivisions is determined by the curve length and frequency setting

3. **Jitter Application**: Random displacements are applied to all points while preserving the overall shape

4. **Path Reconstruction**: The jittered points are converted back to SVG path data:
   - When "Curvy Lines" is enabled, smooth curves are generated using cubic Bezier segments
   - When disabled, straight line segments are created

## Usage

1. Paste your SVG code in the "Input SVG" panel
2. Adjust the jitter parameters using the sliders:
   - Jitter Amplitude: Controls the intensity of the jitter effect
   - Segment Frequency: Controls how many points to generate along each path segment
3. Toggle "Curvy Lines" to switch between straight and curved output
4. Click "Copy Jittered SVG" to copy the result to your clipboard
5. Use the drag & drop area to load SVG files

## Example

### Original SVG
```svg
<svg width="200" height="200" xmlns="http://www.w3.org/2000/svg">
  <!-- Circle using cubic Bezier approximation -->
  <path d="M 100 50 C 100 22.38576250846453 122.38576250846453 0 150 0 C 177.61423749153547 0 200 22.38576250846453 200 50 C 200 77.61423749153547 177.61423749153547 100 150 100 C 122.38576250846453 100 100 77.61423749153547 100 50 Z" fill="none" stroke="blue" stroke-width="2"/>
</svg>
```

### Jittered SVG (with preserved curves)
The output will maintain the circular shape while applying the jitter effect, ensuring that curves remain as curves rather than becoming straight lines.

## Technical Details

### Curve Subdivision Formulas

**Cubic Bezier Curves**:
B(t) = (1-t)³P₀ + 3(1-t)²tP₁ + 3(1-t)t²P₂ + t³P₃

**Quadratic Bezier Curves**:
B(t) = (1-t)²P₀ + 2(1-t)tP₁ + t²P₂

### Control Point Calculation

When reconstructing curves from jittered points, the control points are calculated based on:
1. The direction from the previous point to the current point
2. The direction from the current point to the next point
3. A scaling factor to control the curve tightness

This approach ensures smooth transitions between curve segments while maintaining the hand-drawn aesthetic.