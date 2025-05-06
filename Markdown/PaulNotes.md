# Digital Image Processing Notes Extraction (Part 2)

## I. Morphological Image Processing (Lecture-11, 12 & related examples)

- **Structuring Element (SE):** A small image or template (a small binary array/shape mask) used to probe the input image. It can be of any shape and size, and each has an origin.

  - **Fit:** The SE perfectly matches the underlying image pixels (all SE pixels match).
  - **Hit:** Any (at least one) pixel of the SE matches the underlying image pixels.

- **1. Erosion ($A \ominus B$):**

  - **Process:** All pixels of the structuring element (B) must match the pixels of the original image (A) for the output pixel (at the SE's origin position) to be set. In other words, the SE has to "fit".
  - **Equation:** $ (A \ominus B) = \{z \mid (B)\_z \subseteq A \} $ (The translation of B by z is contained in A).
  - **Effects:**
    - Shrinks the foreground object.
    - Removes spiky edges, smoothens object boundaries.
    - Can split apart joined objects.
    - Can strip away extrusions.
    - Removes irrelevant details from a binary image.
    - Holes inside an object are _not_ removed.
  - **Example (Page 6):** Shows an image F eroded by SE B. If `fit=1`, output is 1, else 0.

- **2. Dilation ($A \oplus B$):**

  - **Process:** Any (at least one) pixel of the structuring element (B) must match a pixel of the original image (A) for the output pixel (at the SE's origin position) to be set. In other words, the SE has to "hit".
  - **Equation:** $ (A \oplus B) = \{z \mid (\hat{B})_z \cap A \neq \emptyset \} $ (The reflection of B, translated by z, intersects with A). \_Note: The notes use \( (B)\_z \cap A \neq \emptyset \) which is common for dilation if B is symmetric or if the definition of B already considers its reflection for the "hit" condition._
  - **Effects:**
    - Enlarges the foreground object.
    - Fills in holes and small gaps.
    - Repairs breaks and intrusions.
    - Smoothens object boundaries by adding an outer layer.
    - Holes inside the object can vanish.
  - **Example (Page 5, 7):** Shows an image F dilated by SE B. If `hit=1`, output is 1, else 0.

- **Duality of Erosion and Dilation (Page 2):**

  - \( (A \ominus B)^c = A^c \oplus \hat{B} \) (Erosion of A by B is the complement of the dilation of A's complement by the reflection of B).
  - \( (A \oplus B)^c = A^c \ominus \hat{B} \) (Dilation of A by B is the complement of the erosion of A's complement by the reflection of B).

- **3. Opening ($A \circ B$):**

  - **Process:** Erosion followed by dilation, using the same structuring element.
  - **Equation:** $ A \circ B = (A \ominus B) \oplus B $
  - **Effects:**
    - Smoothes the outline by rounding off sharp points (breaks narrow isthmuses).
    - Removes small protrusions, thin bridges, and any parts smaller than the structuring element.
    - Doesn't remove holes inside the image (generally).
    - Doesn't significantly change the basic core size of the image (doesn't make it larger or smaller overall, unlike plain erosion or dilation).
  - **Example (Page 6):** Shows opening of F by B.

- **4. Closing ($A \bullet B$):**

  - **Process:** Dilation followed by erosion, using the same structuring element.
  - **Equation:** $ A \bullet B = (A \oplus B) \ominus B $
  - **Effects:**
    - Smoothes the outline by filling small holes and gaps.
    - Connects nearby objects and bridges small breaks.
    - Doesn't significantly change the basic core size of the image.
  - **Opening is the dual of Closing (Page 3):** Opening the foreground pixels with a particular SE is equivalent to closing the background pixels with the same SE.
  - **Example (Page 6):** Shows closing of F by B.

- **5. Boundary Extraction (Page 2):**

  - **Equation:** \( \beta(A) = A - (A \ominus B) \) (Subtract the eroded image from the original image).

- **6. Edge Detection (Morphological) (Page 2):**

  - **Equation:** \( \beta(A) = (A \oplus B) - A \) (Subtract the original image from the dilated image).
    _This is one way; another common one is \( (A \oplus B) - (A \ominus B) \), known as the morphological gradient._

- **7. Hit-or-Miss Transform (HMT) (Page 3):**

  - **Concept:** A high-level morphological method specifically designed to find and locate specific patterns or shapes (configurations of foreground and background pixels) around an origin.
  - **Process:** Uses a composite structuring element \(B = (B_1, B_2)\), where \(B_1\) is a pattern to match in the foreground and \(B_2\) is a pattern to match in the background (disjoint from \(B_1\)). A "hit" occurs if \(B_1\) fits the foreground and \(B_2\) fits the background simultaneously.
  - **Equation:** \( A \circledast B = (A \ominus B_1) \cap (A^c \ominus B_2) \)
  - The notes illustrate with a simple SE where 1=foreground, 0=background, NaN=Don't Care. "If pattern perfectly matches then it's a hit."

- **8. Corner Detection (Page 4):**

  - Shows various structuring elements (masks) designed to detect different types of corners (L-shapes in different orientations).

- **9. End Point Detection (Page 4):**

  - Shows structuring elements for finding end points of lines.

- **Example Application (Page 5):**
  - i. Reflection of SE B.
  - ii. F dilated by B (assuming B is the SE for hits, not its reflection as per standard dilation def).
  - iii. \(F^c\) eroded by B.
  - iv. (F dilated by B) - F (This is an edge detection method).

### II. Color Image Processing (Lecture-15)

- **Human Eye Cones Sensitivity (Page 10):**

  - Red light sensitive: ~65% of cones.
  - Green light sensitive: ~33% of cones.
  - Blue light sensitive: ~2% of cones. (Notes state "out of these 3, blue are most sensitive" - this seems to contradict the percentages and general understanding that green is where peak sensitivity lies for photopic vision. The percentages likely refer to cone population, not peak sensitivity of individual cone types to their respective wavelengths or overall luminous efficiency).

- **Primary Colors of Light (Additive - RGB) (Page 10):**

  - Red, Green, Blue.
  - Combining them (e.g., R+G+B) produces white light.
  - Used in digital displays (monitors, TV screens, projectors).
  - Principle: Adding light to create colors. Starts at zero intensity (black), increase intensity of channels to add light. Max intensity of all channels = white.
  - R + G = Yellow
  - G + B = Cyan
  - B + R = Magenta

- **Primary Colors of Pigment (Subtractive - CMY(K)) (Page 11):**

  - Cyan, Magenta, Yellow.
  - Combining them (e.g., C+M+Y) produces black (theoretically).
  - Used in color printing.
  - Principle: Subtracting light. Starts with white light (e.g., paper), pigments absorb (subtract) certain wavelengths. Max intensity of all channels (i.e., no ink) = white. As ink is added, intensity decreases.
  - CMYK: K stands for Key (Black), added for better blacks and contrast.
  - Cyan = White - Red (absorbs Red)
  - Magenta = White - Green (absorbs Green)
  - Yellow = White - Blue (absorbs Blue)

- **Additive vs. Subtractive Systems (Page 11):**

  - **Additive:**
    1. Get colors by adding/mixing different colors of light.
    2. Emits different spectrums of light.
    3. Uses colored light passing through image elements to reconstruct full colors on screen.
  - **Subtractive:**
    1. Get colors by subtracting different colors/wavelengths.
    2. Absorbs different spectrums of light.
    3. Uses multi-layered colored dyes on film/paper; white light projects through/reflects off, and dyes reconstruct the spectrum.

- **RGB Color Model (Page 12):**

  - Represented as a cube.
  - Black at (0,0,0), White at (1,1,1) (or (255,255,255) for 8-bit per channel).
  - Grayscale values lie along the diagonal from black to white.
  - For a 24-bit color cube (8 bits per plane/channel): Total colors = \( (2^8)^3 = 2^{24} = 16,777,216 \).

- **Light Intensity for Human Eye (Luminance Approximation) (Page 12):**
  - Intensity \( \approx (0.299 \times \text{Red}) + (0.587 \times \text{Green}) + (0.114 \times \text{Blue}) \)
  - Notes state: "Green appears the brightest" (This aligns with the luminance formula where Green has the highest coefficient).

### III. Image Segmentation (Lecture-8 & Decipher 3(e))

- **Definition (Page 13):** The process of partitioning a digital image into multiple segments (sets of pixels, also known as image objects or regions).

  - Divide the image into different regions.
  - Separate objects from background and give them individual IDs.

- **Segmentation Algorithms - Two Basis Properties of Intensity Values (Page 13):**

  - **1. Similarity:** Partitioning an image into regions that are similar according to a set of predefined criteria.
    - **Thresholding:** Based on pixel intensities.
    - **Region Based:** Grouping similar pixels (e.g., region growing, region splitting & merging).
    - **Match Based:** Comparison to a given template.
  - **2. Discontinuity:** Partitioning an image based on sharp changes in intensity.
    - **Edge Based:** Detection of edges that separate regions.
    - **Watershed:** Find regions corresponding to local minima in intensity.

- **Canny Edge Detector (Page 13-17):**

  - **Three Main Criteria for Edge Detection:**
    1. **Good Detection:** Ability to locate and mark all real edges.
    2. **Good Localization:** Minimal distance between the detected edge and the real edge.
    3. **Clear Response:** Only one response per edge (no multiple detections for a single edge).
  - **Algorithm Steps (5 steps):**
    1. **Step 1: Smoothing (Noise Reduction)**
       - Blur the image to remove noise.
       - Typically uses a Gaussian filter: \( G(x,y) = e^{-\frac{x^2+y^2}{2\sigma^2}} \).
       - Smoothed image: \( f_s(x,y) = G(x,y) \* f(x,y) \).
    2. **Step 2: Finding Gradients**
       - Mark edges where the image gradients have large magnitudes.
       - Use an edge operator (e.g., Prewitt, Sobel) to find \(g_x\) (vertical gradient) and \(g_y\) (horizontal gradient).
       - Gradient Magnitude: \( M(x,y) = \sqrt{g_x^2 + g_y^2} \).
       - Gradient Angle (Direction): \( \alpha(x,y) = \arctan\left(\frac{g_y}{g_x}\right) \).
    3. **Step 3: Non-Maximum Suppression**
       - Only local maxima should be marked as edges.
       - For each pixel, check if its gradient magnitude \(M(i,j)\) is greater than the magnitudes of its two neighbors along the gradient direction. If not, suppress it (set to 0).
       - The gradient direction is rounded to one of 4 orientations (horizontal, vertical, +45°, -45°).
       - \(g_N(x,y)\) is the output after non-maximum suppression.
    4. **Step 4: Double Thresholding**
       - Potential edges are determined by thresholding.
       - Uses two thresholds: \(T_H\) (high) and \(T_L\) (low).
       - Pixels with magnitude \( > T*H \) are "strong" edge pixels (\(g*{NH}\)).
       - Pixels with magnitude between \(T*L\) and \(T_H\) are "weak" edge pixels (\(g*{NL}\)).
       - Pixels with magnitude \( < T_L \) are suppressed.
       - Ratio of High to Low threshold is typically 2:1 or 3:1.
    5. **Step 5: Edge Tracking by Hysteresis**
       - Final edges are determined by suppressing all edges that are not connected to a very strong edge.
       - Start with strong edge pixels (these are part of the final edge).
       - Include weak edge pixels if they are connected to strong edge pixels (directly or via other weak pixels that are ultimately connected to a strong one).
       - Discard weak edge pixels not connected to strong edges.
  - **False Positive Edge:** Threshold set too low, some non-edges are detected.
  - **False Negative Edge:** Threshold set too high, actual valid edges are eliminated.
  - **Decipher 3(e) (Page 17):** Shows a flowchart summarizing the Canny Edge Detection process: \(f(x,y) \rightarrow \text{Smoothing (Gaussian)} \rightarrow f*s(x,y) \rightarrow \text{Gradient (Gx, Gy)} \rightarrow M(x,y), \alpha(x,y) \rightarrow \text{Non-Max Suppression} \rightarrow g_N(x,y) \rightarrow \text{Double Thresholding} \rightarrow g*{NH}, g\_{NL} \rightarrow \text{Edge Tracking by Hysteresis} \rightarrow \text{Final Edges}\).

- **Line Detection (Page 17):**
  - Shows 4 masks (R1, R2, R3, R4) for detecting lines in different orientations (Horizontal, +45°, Vertical, -45°). These are typically convolved with the image, and high responses indicate lines.

### IV. Examples & Problem Solving Snippets

- **Origin-42, 1.(b) (Page 19-21):** LZW encoding example (already covered in previous extraction, but this is a more detailed step-by-step from the notes).

  - Image: 3x3, 8-bit, pixels `3,4,7` repeated.
  - Output sequence: `3-4-7-256-258-257`.
  - Compression ratio calculation: Original 72 bits, compressed 54 bits. Ratio 72/54 = 1.33:1.

- **Origin-42, 5.(a) (Page 22):**

  - Image compression needs: Data storing at low storage, data transmission at minimum cost.
  - Three types of data redundancy: Coding, Spatial/Temporal, Irrelevant information.
  - Entropy and max compression: 5.3 bits/pixel entropy means min 5.3 bits required. Max compression for 8-bit original = 8 / 5.3 = 1.509:1.

- **Origin-42, 5.(b) (Page 23):** Huffman coding example.

  - Image with pixels 18,16,16,11,11,14,18,18,11.
  - Probabilities: P(11)=3/9, P(14)=1/9, P(16)=2/9, P(18)=3/9.
  - Entropy \(H = 1.88\) bits/pixel (notes show 1.88, previous calculation was 1.758, likely due to rounding or slight diff in log base/calc).
  - \(L*{avg} = 1.98\) bits/pixel. \(H < L*{avg}\), so lossless.
  - Compression ratio = 8 / 1.98 = 4.04:1.

- **Enigma-41, 2.(a) (Page 24):** Information theory basics.

  - \(I(E) = \log(1/P(E)) = -\log P(E)\).
  - Entropy \(H = -\sum P(a_j) \log P(a_j)\).

- **Enigma-41, (Page 25):** Another Huffman coding example.

  - Pixel values and probabilities given.
  - \(H = 2.41\) bits/pixel.
  - \(L\_{avg} = 2.72\) bits/pixel.
  - Compression ratio = 8 / 2.72 = 2.94:1.
  - Relative redundancy \(R_D = 1 - (1/CR) = 0.65\).

- **Enigma-41, 2.(b) (Page 26):** RMSE and Golomb code example.

  - RMSE calculation for an image where processed pixels are original+2. RMSE = 2.
  - Golomb code \(G_4(9)\): \(q=2\) (unary `110`), \(k=2, c=0, r=1\). Remainder code `01`. Final `11001`.

- **Recursive-40, 5.(a) (Page 27-28):** Arithmetic coding.

  - Lossless technique, represents a code string as a fractional value [0,1).
  - Gives higher compression than Huffman.
  - Example sequence `a1 a4 a3 a4 a2 a5` with given probabilities, resulting code ~0.165350.

- **Integer-43, 3.(a) (Page 29-30):** Huffman and RLE example.

  - 6-bit image given.
  - Huffman: \(L\_{avg} = 2\) bits/pixel. Compression ratio (Huff) = 6/2 = 3.0.
  - RLE: Compressed file shown. Original size 4x5x6 = 120 bits. Compressed RLE size 17x2x6 = 204 bits (This indicates RLE performed poorly, resulting in expansion).
  - RLE compression ratio = 120/204 = 0.58.

- **Decipher-44, 3.(a) (Page 31):** Huffman coding example.

  - 6-bit image. \(L\_{avg} = 2\) bits/pixel. Compression ratio = 6/2 = 3.
  - Relative redundancy \(R_D = 1 - (1/3) = 0.667\).
  - Entropy \(H = 1.92\) bits/pixel. Since \(H < L\_{avg}\), no data loss.

- **Decipher-44, 3.(f) (Page 32-33):** Image filtering example.

  - Image given.
  - Average filter (3x3) applied with zero padding. Filtered output shown.
  - Median filter (3x3) applied. Output shown for inner pixels.

- **Decipher-44, (Page 18):** Sobel operator example.
  - Point (3,3) in a 5x5 image.
  - \(g_y = 1\), \(g_x = -7\).
  - Gradient Magnitude = \( \sqrt{(-7)^2 + 1^2} = \sqrt{50} = 5\sqrt{2} \).
  - Gradient Direction = \( \tan^{-1}(1/-7) = -8.13^\circ \).
  - Edge Direction = Gradient Direction - 90° = -98.13°.
