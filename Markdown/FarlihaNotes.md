# Digital Image Processing Notes Extraction (Part 3 - Comprehensive)

The first page provides a chapter/lecture guide:

- **Chapter 3 (Intensity Transformations) -> Lecture Slide 4, 5, 6, 7**
  - Includes "Histogram Specification/matching" (topic or part of a topic).
- **Chapter 6 (Color Image Processing) -> Lecture 15** (HSI model mentioned as important, "বাকিটা আছে" - rest is there).
- **Chapter 8 (Image Segmentation) -> Lecture 13, 14**
- **Chapter 9 (Morphological Processing) -> Lecture 11, 12** (Thinning & thickening mentioned, "আগে আগে পড়া" - studied before/earlier).
- **Chapter 10 (Image Segmentation, likely continued or different aspects) -> Lecture 8**

---

## Chapter 3: Intensity Transformations (Lecture 4, 5, 6, 7)

### Pages: Semester Final 4.2 - Page 2 to Page 15; Lecture 5 - Page 16 to Page 24; Lecture 6 - Page 25 to Page 33; Lecture 7 - Page 34 to Page 37

#### A. Image Enhancement (Page 2)

- **Definition:** Process/manipulate an image so that the result is more suitable than the original image for a specific application.
- **Methods:**
  - Spatial Domain (direct manipulation of pixels) - _Circled as the focus._
  - Frequency Domain
  - Combination Methods

#### B. Spatial Domain Processes (Page 3)

- **Types of Image Enhancement Operations:**
  - **1. Point/Pixel Operation:** Output pixel depends only on the corresponding input pixel. (e.g., intensity transformations).
    - \(g(x,y) = T[f(x,y)]\)
  - **2. Local Operation:** Output pixel depends on the input pixel and its neighbors (using a mask/kernel/template/window). (e.g., spatial filters).
  - **3. Global Operation:** Output pixel depends on all pixels in the input image (often Frequency Domain, but some spatial operations can be global).
- _Note: Input and output image sizes are generally the same for these operations._

#### C. Intensity Transformation Functions (Page 4-8)

- **General Form:** \(s = T(r)\), where \(r\) is the input intensity, \(s\) is the output intensity.
- **1. Basic Functions:**

  - **a. Linear Functions:**
    - **Negative Transformation (Page 5, 13):** \(s = (L-1) - r\). Reverses intensity levels.
      - Example: 8-bit image (\(L=256\)). Input pixel 100 -> Output \(255-100 = 155\).
      - Example: 3-bit image (\(L=8\)). Input pixel 2 -> Output \(7-2 = 5\).
    - **Identity Transformation:** \(s = r\). Output is same as input.
  - **b. Logarithmic Functions (Page 4, 6):** \(s = c \cdot \log(1+r)\)
    - Expands values of dark pixels (lower amplitudes) and compresses values of bright pixels (higher amplitudes).
    - Called "dynamic-range compression/expansion".
    - Inverse log performs the opposite.
  - **c. Power-Law (Gamma) Functions (Page 4, 7):** \(s = c \cdot r^\gamma\)
    - \(c\) and \(\gamma\) are constants. (\(c=1\) is often default).
    - \(\gamma < 1\): Maps narrow range of dark input values into a wider range of output values (brighter).
    - \(\gamma > 1\): Maps narrow range of bright input values into a wider range of output values (darker).
    - \(\gamma = 1\): Linear mapping.
    - Used for gamma correction, contrast enhancement in MRI.

- **2. Piecewise-Linear Transformation Functions (Page 2, 4, 8-10):**
  - **Advantage:** Some important transformations can only be formulated as piecewise functions.
  - **Disadvantage:** Their specification requires more user input.
  - **Types:**
    - **a. Contrast Stretching (Page 8):** Increases the dynamic range of gray levels.
      - Formula: \(I\_{new} = \frac{(I - Min) \cdot (NewMax - NewMin)}{(Max - Min)} + NewMin\)
      - Example: Input range [80-150] stretched to [0-255].
    - **b. Intensity-Level Slicing / Gray-Level Slicing (Page 10, 15):** Highlights a specific range of intensities.
      - **Approach 1 (Binary):** Set desired range to a high value (e.g., white), others to a low value (e.g., black).
        - Example: Range (100-200) becomes 255, others 0.
      - **Approach 2 (Preserve):** Keep intensities in the desired range, and either dim or keep others.
        - Example (Page 15): Range [a=3, b=5].
          - First approach: Pixels in become 5, others 0.
          - Second approach: Pixels in become 5, others remain unchanged.
    - **c. Bit-Plane Slicing (Page 2, 10-12, 14):** (Covered in previous extractions, consistent here).
      - Decomposes image into binary planes.
      - Goals: Convert to binary, compression, enhancement by focusing.
      - MSB (Most Significant Bit) is far left, LSB (Least Significant Bit) is far right.
      - The \(n^{th}\) plane pixels are multiplied by \(2^{n-1}\) for reconstruction.
      - Example (Page 11-12): 8-bit image, reconstruct using 5th and 4th bit planes.
      - Example (Page 14): 3-bit image, bit-planes shown.
    - **d. Thresholding (Page 6, 9, 14):** A special case of intensity-level slicing (binary).
      - Function: \(s = T(r)\). If \(r < \text{Threshold}\), \(s = \text{value1}\) (e.g., 0); if \(r \ge \text{Threshold}\), \(s = \text{value2}\) (e.g., L-1).
      - Used to convert gray-level to binary.
      - Example (Page 9): 8-bit image, Threshold \(m=95\). Pixels \(<95 \rightarrow 0\), pixels \(\ge 95 \rightarrow 255\).
      - Example (Page 14): 3-bit image, Threshold \(T=3\). Pixels \(<3 \rightarrow 0\), pixels \(\ge 3 \rightarrow 7\).

#### D. Histogram Processing (Lecture 5 - Page 16 to Page 24)

- **Histogram:** A graphical representation showing the distribution of data (number of pixels for each intensity value). (Page 16)
  - \(h(r_k) = n_k\), where \(r_k\) is the \(k^{th}\) gray level and \(n_k\) is the number of pixels with that gray level.
  - Sum of histogram values \(\sum h(r_k)\) = area of the image (total number of pixels).
- **Normalized Histogram (PDF - Probability Density Function):** (Page 17, 20)
  - \(P(r_k) = \frac{n_k}{N}\), where \(N\) is the total number of pixels.
  - Sum of normalized histogram values is 1.
- **Can we reconstruct an image from its histogram?** No, histogram does not contain location information of pixels. (Page 18)
- **Color Image Histogram:** (Page 18)
  1. Convert color image to grayscale, then display histogram.
  2. Display 3 separate histograms (for R, G, B components).
- **Basic Types of Images and Their Histograms:** (Page 18-19)
  - **Dark Image:** Concentrated on the low (dark) side of the gray scale.
  - **Bright Image:** Concentrated on the high (bright) side of the gray scale.
  - **Low Contrast Image:** Narrow and centered toward the middle of the gray scale.
  - **High Contrast Image:** Covers a broad range of the gray scale, distribution is not too far from uniform, with very few vertical lines (meaning many gray levels are present).
- **Why is Image Histogram Important?** (Page 19)
  1. Provides key info about image shape (overall quality).
  2. Indicates possibility for enhancement.
  3. Visual inspection is a fast way to analyze overall image quality.
  4. By processing the histogram, an image can be quickly and automatically improved.
- **Uses of Histogram:** (Page 20)
  1. Whether image was scanned properly.
  2. Idea about tonal distribution.
  3. To improve appearance (e.g., histogram equalization).
  4. Tells about objects in the image.
  5. To select threshold values for object detection.
  6. Can be used for managed segmentation.
- **Histogram Processing Techniques:** (Page 21-24)
  - **1. Histogram Sliding:** Simply shifts the histogram towards right (brighter) or left (darker). (Page 21)
  - **2. Histogram Stretching (Contrast Stretching):** Increases the dynamic range. (Page 22)
    - Formula: \(g = \frac{255 \cdot (f - min)}{(max - min)}\) (rescales to range).
    - Normalizes histogram to range.
  - **3. Histogram Equalization:** Aims to produce a flat histogram (uniform distribution of intensities) for contrast enhancement. (Page 23-24)
    - Target: Contrast enhance.
    - Process: \(s*k = T(r_k) = (L-1) \sum*{j=0}^{k} P_r(r_j) = (L-1) \times CDF(r_k)\).
    - Example shown with calculations and original/equalized histograms.
    - Transformation function \(s_k\) vs \(r_k\) is a monotonically increasing curve.
  - **Histogram Specification/Matching:** (Mentioned on the first index page, but detailed notes not explicitly found in this PDF set, though it's a standard histogram processing technique that transforms an image to have a specific, desired histogram shape).

### E. Spatial Filtering (Lecture 6 - Page 25 to Page 33; Lecture 7 - Page 34 to Page 37)

- **Kernel/Mask/Filter/Window/Template:** A 2-dimensional matrix, usually small (e.g., 1x1, 3x3, 5x5, 7x7). Should be odd-numbered to have a center. (Page 25)
- **Convolution Process:** Element-wise multiplication of kernel with underlying image pixels, then sum of products to get the output pixel at the kernel's center. (Page 25)
- **Spatial Filtering Consists of:** (Page 26)
  - A neighborhood.
  - A predefined operation.
- **Types of Filtering in Spatial Domain:** (Page 26)
  - **1. Smoothing Spatial Filters (Low-Pass):** For blurring and noise reduction.
  - **2. Sharpening Spatial Filters (High-Pass):** For highlighting fine details and edges.
- **Why Filters are Used?** (Page 26)
  - Blurring and noise reduction.
  - Edge detection and sharpness.
  - Enhance image.
  - Extract info from image.
  - Detecting patterns.

#### E1. Smoothing Spatial Filters (Page 27-33)

- **Use:** For blurring, noise reduction, removing fine details.
- **Types:**
  - **1. Standard Average/Mean Filter (Linear):** (Page 27, 30)
    - Kernel elements are usually \(1/N^2\) for an NxN kernel (e.g., all 1/9 for 3x3).
    - Replaces each pixel with an average of its neighborhood.
    - Achieves smoothing effect (removes sharp features). Adds a 'softer' look.
  - **2. Weighted Average Filter (Linear):** (Page 27, 31)
    - Kernel elements are not all the same; typically give more weight to the center pixel or closer neighbors.
    - Example 3x3 kernel: \( \frac{1}{16} \begin{pmatrix} 1 & 2 & 1 \\ 2 & 4 & 2 \\ 1 & 2 & 1 \end{pmatrix} \) (Gaussian-like).
    - Weighted filters are mainly Gaussian filters.
  - **3. Median Filter (Order-Statistic / Non-Linear):** (Page 27, 32)
    - Replaces pixel value with the median of the intensities in the neighborhood.
    - Excellent for removing salt-and-pepper (impulse) noise.
    - Better at preserving edges than mean filters.
    - Example shown: Sort neighborhood pixels, pick median. Min/Max filters also mentioned.
- **Padding (for border pixels):** (Page 28-29)
  - **1. Zero Padding (Mostly used):** Pad with zeros around the image border.
  - **2. Border Padding / Pixel Replication:** Replicate border pixel values.
  - **3. Discard Solution:** Only calculate output for pixels where the kernel fully fits within the image (output image is smaller).
- **Cross-Correlation and Template Matching:** (Page 33 - "Slide এটার মধ্যে সুন্দর মতো দেয়া আছে" - Details are nicely given in the slide).

#### E2. Sharpening Spatial Filters (Lecture 7 - Page 34 to Page 37)

- **Objective:** Highlight fine details, remove blurring, highlight edges, emphasize transitions in image intensity. (Page 34)
- **Based on Spatial Differentiation:**
  - 1st Derivative (Gradient operators like Sobel, Prewitt).
  - 2nd Derivative (Laplacian).
- **Types:**
  - **1. Laplacian Filter (Uses 2nd derivative):** (Page 35)
    - Isotropic (rotation invariant).
    - Kernels (4-neighbor and 8-neighbor, forward/backward or positive/negative center):
      - \( \begin{pmatrix} 0 & 1 & 0 \\ 1 & -4 & 1 \\ 0 & 1 & 0 \end{pmatrix} \), \( \begin{pmatrix} 0 & -1 & 0 \\ -1 & 4 & -1 \\ 0 & -1 & 0 \end{pmatrix} \)
      - \( \begin{pmatrix} 1 & 1 & 1 \\ 1 & -8 & 1 \\ 1 & 1 & 1 \end{pmatrix} \), \( \begin{pmatrix} -1 & -1 & -1 \\ -1 & 8 & -1 \\ -1 & -1 & -1 \end{pmatrix} \)
    - Enhanced Image = Original Image + (Laplacian applied to image). (Page 36)
      - If Laplacian has negative center, subtract it. If positive center, add it.
      - This can be combined into a single filter mask, e.g., \( \begin{pmatrix} 0 & -1 & 0 \\ -1 & 5 & -1 \\ 0 & -1 & 0 \end{pmatrix} \).
  - **2. Sobel Operators (Gradient, uses 1st derivative):** (Page 35, 36)
    - Kernels for \(G_x\) and \(G_y\).
    - Magnitude \(M = |G_x| + |G_y|\) or \(M = \sqrt{G_x^2 + G_y^2}\).
  - **3. Prewitt Operators (Gradient, uses 1st derivative):** (Page 36)
    - Kernels for \(G_x\) and \(G_y\). Similar to Sobel but with constant weights.

### Chapter 6: Color Image Processing (Lecture 15 - Page 38 to Page 45)

(Largely consistent with previous extractions, providing more diagrams)

- **Human Eye Range:** 400 nm (violet) to 700 nm (red). (Page 38)
- **Color Model:** Human eye combines 3 primary colors using 3 different types of cones. Colors are different light frequencies. (Page 38)
  - Red: ~700 nm
  - Green: ~546.1 nm
  - Blue: ~435.8 nm
  - Higher frequencies are cooler colors.
- **Human Color Vision:** (Page 39)
  - Retina: Cones (color sensitive), Rods (more numerous, insensitive to color).
  - 6-7 million cones. Cone sensitivity percentages: 65% Red, 33% Green, 2% Blue. (Again, "Blue cones are the most sensitive" is noted, which is context-dependent and usually refers to their peak wavelength sensitivity, not overall contribution to luminance or population).
- **Primary Colors:** (Page 40)
  - **Light (Additive):** R, G, B. R+G+B = White. Used in displays.
  - **Pigment (Subtractive):** C, M, Y. C+M+Y = Black. Used in printers.
- **Diagrams of Additive and Subtractive Mixing:** (Page 40)
- **Pigment Interaction with Light:** (Page 41-42) Diagrams showing how different colored pigments absorb and reflect R, G, B light components to produce the perceived color.
  - Red pigment reflects R, absorbs G, B.
  - Black pigment absorbs R, G, B (no light reflected).
  - Cyan pigment reflects G, B, absorbs R (appears Cyan).
- **Primary and Secondary Colors Summary:** (Page 43)
  - Additive: R+G=Y, R+B=M, G+B=C.
  - Subtractive: C=W-R, M=W-G, Y=W-B.
- **RGB Color Model Cube:** (Page 43) (Consistent with previous notes).
- **Light Intensity (Luminance):** (Page 44) Intensity = \(0.299 \cdot R + 0.587 \cdot G + 0.114 \cdot B\). (Consistent).
- **HSI Model:** (Mentioned on the first index page as important for Chapter 6/Lecture 15, but detailed notes on HSI itself are not in this specific PDF batch, beyond the general color concepts).

### Chapter 10 & 8: Image Segmentation (Lecture 8, 13, 14 - Page 46 to Page 60)

#### A. Introduction to Image Segmentation (Lecture 8 - Page 46-54)

- **What is Image Segmentation?** (Page 46)
  - An aspect of image processing.
  - A computer vision process.
  - The first step in image analysis.
  - Process: Input Image -> Image Segmentation -> Segmented Objects/Images (Annotation of objects) -> Object Quantification (Feature Extraction) -> Feature Vector -> Results (Classification of cluster).
- **What does Image Segmentation do?** Divides an image into different regions for analysis. (Page 47)
- **Principal Approaches:** (Page 48)
  - **Similarity:** Partitioning into regions similar by predefined criteria.
    - Algorithms: Thresholding, Region-based (grouping similar pixels), Match-based (comparison to template).
  - **Discontinuity:** Partitioning based on sharp changes in intensity.
    - Algorithms: Edge-based (detection of edges), Watershed (find regions from local minima).
- **Point Detection:** \(R = \sum w_k z_k\) (weighted sum in a kernel). (Page 49)
- **Line Detection:** Masks for Horizontal, +45°, Vertical, -45° lines. (Page 49)
- **Edge:** The boundary between two homogeneous regions. (Page 49)
  - Types: Step edge, Ramp edge, Ridge, Roof.
- **Edge Detection:** Identifying sudden changes in image intensity. (Page 50)
  - Usage: Extract important features (corners, lines, curves).
  - **Three Main Steps in Edge Detection:**
    1. Filtering (Smoothing): To remove noise.
    2. Differentiation (Edge Sharpening using derivatives): Detects discontinuities.
    3. Detection (Thresholding): Decide if changes are significant.
  - Extra: Localization (determine exact location of edge).
- **Gradient-Based Edge Detection (e.g., Sobel):** (Page 51)
  - Image convolved with \(G_x, G_y\) masks, sum (or magnitude), then threshold.
  - Simple to implement.
  - Capable of detecting edges and their direction.
  - Sensitive to noise.
  - Not accurate in locating edges.
  - Problem: Too much detail. One way to overcome is to smooth images prior to edge detection -> Leads to LoG.
- **LoG (Laplacian of Gaussian):** (Page 52)
  - First, smoothing with Gaussian filter.
  - Then, enhance edges with Laplacian operator.
  - About: Computationally cheaper to implement (than some others). But doesn't provide info about edge direction. Probability of false and missing edges remain. Localization is better than Gradient operators.
- **LoG for Marr-Hildreth Algorithm:** (Page 53)
  - **Steps:**
    1. Smoothing: Gaussian filter.
    2. Enhance edges: Laplacian operator.
    3. Zero-crossings denote the edge location.
    4. Use linear interpolation to determine sub-pixel location of the edge.
  - \(g(x,y) = \nabla^2 [G(x,y) * f(x,y)]\)
- **The Canny Edge Detector:** (Page 54-60 - Consistent with previous detailed notes, providing step-by-step breakdown).
  - 3 Main Criteria: Good detection, Good localization, Clear response.
  - 5 Steps:
    1. Smoothing: \(f_s(x,y) = G(x,y) \* f(x,y)\).
    2. Finding Gradients: \(M(x,y) = \sqrt{G_x^2 + G_y^2}\).
    3. Non-Maximum Suppression: (Diagram showing pixel comparison along gradient direction).
    4. Double Thresholding (Hysteresis Thresholding): \(g*{NH}(x,y) = g_N(x,y) \ge T_H\), \(g*{NL}(x,y) = g_N(x,y) \ge T_L\).
    5. Edge Tracking by Connectivity Analysis (Hysteresis): (Diagrams showing \(g*{NH}, g*{NL}\) and final \(g(x,y)\)).
       - High threshold -> sure edge. Low threshold -> non-edges. Between High & Low, if connected to High then sure edge, otherwise discard.
       - False Positive / False Negative definitions.

### Chapter 9: Morphological Image Processing (Lecture 11, 12 - Page 61 to Page 69)

(Largely consistent with previous extractions, some rephrasing and diagrams)

- Pixel shape-based analysis. (Page 61)
- **Basic Operations:**
  - Dilation: Grow image regions.
  - Erosion: Shrink image regions.
  - Opening: Structured removal of boundary pixels.
  - Closing: Structured filling in of boundary pixels.
- **Binary Image:** Representation 0,1. Foreground=1 (white), Background=0 (black). (Page 61)
- **Structuring Element (SE):** Can be any shape, has an origin. (Page 61)
- **Fit, Hit, Miss definitions for SE:** (Page 62)
- **Erosion (\(A \ominus B\)):** (Page 62)
  - Fit -> 1, Hit -> 0, Miss -> 0.
  - Equation: \( \{z \mid (B)\_z \subseteq A \} \).
  - Effects: Shrinks foreground, enlarges background, smoothes boundaries, removes small objects.
- **Dilation (\(A \oplus B\)):** (Page 63)
  - Fit -> 1, Hit -> 1, Miss -> 0.
  - Equation: \( \{z \mid (\hat{B})\_z \cap A \neq \emptyset \} \).
  - Effects: Fills holes, smoothes boundaries, adds outer ring, expands foreground.
- **Boundary Extraction:** (Page 63)
  - For Erosion: \(A - (A \ominus B)\) (Object's inner line).
  - For Dilation: \((A \oplus B) - A\) (Object's outer line / background pixel).
- **Duality:** (Page 64) \( (A \ominus B)^c = A^c \oplus \hat{B} \), \( (A \oplus B)^c = A^c \ominus \hat{B} \).
- **Opening (\(A \circ B = (A \ominus B) \oplus B\)):** (Page 64)
  - Firstly erosion, then dilation on the result.
  - Diagram showing effect.
- **Closing (\(A \bullet B = (A \oplus B) \ominus B\)):** (Page 65)
  - Firstly dilation, then erosion on the result.
  - Diagram showing effect.
- **Table Comparing Opening and Closing Effects:** (Page 65)

  | Opening                                        | Closing                                          |
  | :--------------------------------------------- | :----------------------------------------------- |
  | Removes small objects/noise from foreground    | Fills small holes and gaps in foreground         |
  | Erosion followed by dilation                   | Dilation followed by erosion                     |
  | Makes objects smaller (initially)              | Makes objects larger (initially)                 |
  | Useful for separating objects                  | Useful for connecting objects                    |
  | Removes any parts smaller than SE              | Fills any parts touched by SE                    |
  | Denoted \(A \circ B = (A \ominus B) \oplus B\) | Denoted \(A \bullet B = (A \oplus B) \ominus B\) |

- **Hit-or-Miss Transform (Lecture 12):** (Page 66-67)
  - SE contains 0, 1, and X (don't care).
  - Diagram showing 0=background, 1=foreground, X=any pixel.
  - "WATCH OUT: We actually look for 'fits' but we will be calling them 'hits' when talking about Hit and Miss Transformation." (This clarifies a potential terminology confusion).
  - Equation: \(A \circledast B = (A \ominus B_1) \cap (A^c \ominus B_2)\) (Note: previous notes had a minus, intersection is standard).
  - SEs representing 4 corners.
  - SEs representing end points.
- **Thinning & Thickening:** (Mentioned on the first index page for Lecture 11, 12. While the basic morphological operations are covered, explicit detailed sections on "Thinning" and "Thickening" algorithms themselves are not present in this specific PDF batch, though they are derived from sequences of HMT or other morphological operations).
