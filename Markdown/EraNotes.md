# Digital Image Processing Notes Extraction

## Part 1: Image Compression

### 1. Lempel-Ziv-Welch (LZW) Coding (Lossless)

- **Concept:** A lossless compression technique that assigns fixed-length codes to variable-length sequences of source symbols. It reduces spatial redundancy.
- **Process:**

  1. Initialize a dictionary with single-character strings (e.g., for an 8-bit image, entries 0-255 represent the pixel values).
  2. Read the input sequence. Find the longest string \(S\) in the dictionary that matches the current input.
  3. Output the code for \(S\).
  4. Take the next character \(C\) from the input. Add the string \(S+C\) to the dictionary with a new code.
  5. Set \(S = C\) and repeat from step 2.

- **Example 1 (Page 1-3):**

  - Image: 3x3, 8-bit, pixels \( (3,4,7), (3,4,7), (3,4,7) \), i.e., sequence: `3, 4, 7, 3, 4, 7, 3, 4, 7`.
  - Codebook uses 9 bits (new entries start from 256).
  - **Encoding Steps:**

    | Current Recognized Sequence (S) | Pixel Being Processed (C) | Encoded Output | Dictionary Location (New Code) | Dictionary Entry (S+C) |
    | :------------------------------ | :------------------------ | :------------- | :----------------------------- | :--------------------- |
    |                                 | 3                         |                |                                |                        |
    | 3                               | 4                         | 3              | 256                            | 3-4                    |
    | 4                               | 7                         | 4              | 257                            | 4-7                    |
    | 7                               | 3                         | 7              | 258                            | 7-3                    |
    | 3-4 (is 256)                    | 7                         | 256            | 259                            | 3-4-7                  |
    | 7-3 (is 258)                    | 4                         | 258            | 260                            | 7-3-4                  |
    | 4-7 (is 257)                    | (end of sequence)         | 257            |                                |                        |

  - **Output Sequence:** `3, 4, 7, 256, 258, 257`
  - **Compression Analysis:**
    - Original size: 9 pixels \* 8 bits/pixel = 72 bits.
    - Compressed size: 6 codes \* 9 bits/code = 54 bits.
    - Compression Ratio = \( \frac{72}{54} \approx 1.33 \). Compression is achieved.

- **Example 2 (Page 12-13):**
  - Image: 5x5, 8-bit. Each row is `38, 40, 60, 80, 100`. (Sequence: `38,40,60,80,100,38,40,60,80,100,...`)
  - Using 9-bit codes.
  - **Output Sequence (from notes):** `38, 40, 60, 80, 100, 256, 258, 260, 257, 259, 261, 265, 264, (another code, likely the last single pixel if the sequence ended there, or another dictionary code)`. The notes state 14 output codes.
    - (The table on page 12 details the step-by-step encoding for a longer sequence based on this pattern).
  - **Compression Analysis:**
    - Original size (for 25 pixels): 25 pixels \* 8 bits/pixel = 200 bits.
    - Compressed size (assuming 14 output codes from the partial trace): 14 codes \* 9 bits/code = 126 bits.
    - Compression Ratio = \( \frac{200}{126} \approx 1.59 \). Compression is achieved.

#### 2. Huffman Coding (Lossless)

- **Concept:** A variable-length coding technique that assigns shorter codes to more frequent symbols and longer codes to less frequent symbols. Reduces coding redundancy.
- **Process:**

  1. Calculate the probability (or frequency) of each symbol.
  2. Create a leaf node for each symbol and its probability.
  3. Repeatedly combine the two nodes with the smallest probabilities into a new internal node whose probability is the sum of its children's probabilities. Assign 0 to one branch and 1 to the other.
  4. Repeat until only one node (the root) remains.
  5. The code for each symbol is read by traversing the tree from the root to the leaf.

- **Entropy (\(H\)):** The theoretical minimum average number of bits per symbol.
  $$ H = -\sum\_{k} P_r(r_k) \log_2 P_r(r_k) $$
  where \( P_r(r_k) \) is the probability of symbol \( r_k \).
- **Average Length (\(L\_{avg}\)):** The average number of bits per symbol achieved by the Huffman code.
  $$ L*{avg} = \sum*{k} L*k P_r(r_k) $$
  where \( L_k \) is the length of the Huffman code for symbol \( r_k \).
  For lossless compression, \( H \le L*{avg} \).

- **Example 1 (Page 4-5):**

  - Image: 3x3, 8-bit. Pixels: `18, 16, 16, 11, 11, 14, 18, 18, 11`.
  - Symbols and counts: 11 (3), 14 (1), 16 (2), 18 (3). Total 9 pixels.
  - Probabilities: \(P(11)=\frac{3}{9}\), \(P(14)=\frac{1}{9}\), \(P(16)=\frac{2}{9}\), \(P(18)=\frac{3}{9}\).
  - **Huffman Codes (from notes' tree, mapping probabilities to symbols):**
    - Symbol with \(P=\frac{3}{9}\) (e.g., 18): `0` (Length 1)
    - Symbol with \(P=\frac{3}{9}\) (e.g., 11): `10` (Length 2)
    - Symbol with \(P=\frac{2}{9}\) (e.g., 16): `110` (Length 3)
    - Symbol with \(P=\frac{1}{9}\) (e.g., 14): `111` (Length 3)
  - **Entropy:** \(H \approx 1.758\) bits/pixel (calculation shown in notes).
  - **Average Length (based on notes' specific code assignment in sum):** \(L*{avg} = (0.33 \times 1) + (0.22 \times 3) + (0.33 \times 2) + (0.11 \times 3) = 1.98\) bits/pixel.
    \*(Note: A standard Huffman construction with the given probabilities yields \(L*{avg}=2\) bits/pixel. The notes' \(L\_{avg}=1.98\) is based on their specific summation order which implies a slightly different probability-to-code-length mapping than the tree might suggest at first glance, or a rounding in probabilities used for the sum).\*
  - **Compression Ratio:** \( \frac{\text{Original bits/pixel}}{\text{Avg bits/pixel}} = \frac{8}{1.98} \approx 4.04:1 \).
  - Since \(H (1.758) < L\_{avg} (1.98)\), it's lossless.

- **Example 2 (Page 6-7):**

  - Image: 4x8 pixels (32 total).
  - Symbols and counts: 21 (12), 95 (4), 169 (4), 243 (12).
  - Probabilities: \(P(21)=0.375\), \(P(95)=0.125\), \(P(169)=0.125\), \(P(243)=0.375\).
  - **Huffman Codes (from notes' tree):**
    - 21 (0.375): `0` (Length 1)
    - 243 (0.375): `10` (Length 2)
    - 95 (0.125): `110` (Length 3)
    - 169 (0.125): `111` (Length 3)
  - **Entropy:** \(H \approx 1.811\) bits/pixel.
  - **Average Length:** \(L\_{avg} = (0.375 \times 1) + (0.375 \times 2) + (0.125 \times 3) + (0.125 \times 3) = 1.875\) bits/pixel.
  - **Compression Ratio:** \( \frac{8}{1.875} \approx 4.267:1 \).
  - **Relative Data Redundancy (\(R_D\)):** \( R*D = 1 - \frac{1}{\text{Compression Ratio}} = 1 - \frac{L*{avg}}{L\_{original}} = 1 - \frac{1.875}{8} \approx 0.7656 \) or 76.56%.

- **Maximum Compression with Huffman (Page 4):**
  - For an image with entropy \(H_e\), the maximum compression ratio achievable is \( \frac{\text{Original bits/pixel}}{H_e} \).
  - Example: 512x512, 8-bit image, entropy = 5.3 bits/pixel.
  - Max Compression Ratio = \( \frac{8}{5.3} \approx 1.509 \).

#### 3. Types of Redundancy (Page 3)

- **Coding Redundancy:** More bits are used than necessary to represent intensity values (addressed by Huffman).
- **Spatial and Temporal Redundancy:** Pixels in 2D images (spatial) or frames in video (temporal) are often correlated (addressed by LZW, Run-Length Encoding, etc.).
- **Irrelevant Information:** Information ignored by the human visual system (addressed by lossy compression).

#### 4. Why Image Compression is Needed (Page 3)

- Store data at low storage.
- Data transmission at minimum cost.

#### 5. Golomb Coding (Page 9-10)

- **Concept:** A lossless data compression method suited for data with geometrically distributed values.
- **Parameters:** Integer \(n\) to be encoded, positive integer divisor \(m\).
- **Process for \(G_m(n)\):**
  1. **Quotient (\(q\)):** \( q = \text{floor}(\frac{n}{m}) \) (as per notes, though \( (n-1)/m \) is also common).
  2. **Unary code for \(q\):** \(q\) ones followed by a zero (e.g., if \(q=2\), unary is `110`).
  3. **Remainder (\(r\)):** \( r = n \pmod m \).
  4. **Parameters for remainder encoding:**
     - \( k = \lceil \log_2 m \rceil \)
     - \( c = 2^k - m \)
  5. **Remainder encoding:**
     - If \( r < c \): encode \(r\) in \(k-1\) bits.
     - Else: encode \(r+c\) in \(k\) bits.
       (Note: If \(m\) is a power of 2, then \(c=0\), so the remainder is always \(r\) encoded in \(k = \log_2 m\) bits).
- **Concatenated Code:** Unary code of \(q\) + Binary code of remainder.

- **Example: \(G_4(9)\) (n=9, m=4)**

  - \(q = \text{floor}(\frac{9}{4}) = 2\). Unary(2) = `110`.
  - \(k = \lceil \log_2 4 \rceil = 2\).
  - \(c = 2^2 - 4 = 0\).
  - \(r = 9 \pmod 4 = 1\).
  - Since \(r (1) \not< c (0)\), use second case. Remainder part is \(r+c = 1+0 = 1\). Encode `1` in \(k=2\) bits: `01`.
  - **Golomb Code for \(G_4(9)\):** `110` `01`.

- **Example: \(G_4(7)\) (n=7, m=4)**
  - \(q = \text{floor}(\frac{7}{4}) = 1\). Unary(1) = `10`.
  - \(k=2, c=0\).
  - \(r = 7 \pmod 4 = 3\).
  - Remainder part is \(r+c = 3+0 = 3\). Encode `3` in \(k=2\) bits: `11`.
  - **Golomb Code for \(G_4(7)\):** `10` `11`.

#### 6. Arithmetic Coding (Page 11)

- **Concept:** A form of entropy encoding where a sequence of symbols is represented by a single fractional number between 0 and 1. Longer, more complex messages lead to more precision (more bits) in the fraction.
- **Process:**
  1. Initialize an interval [0, 1).
  2. For each symbol in the sequence:
     - Subdivide the current interval based on the probabilities of the symbols.
     - Select the sub-interval corresponding to the current symbol. This becomes the new current interval.
  3. The final code can be any number within the final interval.
- **Example (Page 11):**
  - Symbols & Probabilities & Initial Intervals:
    - a1: 0.2 [0.0, 0.2)
    - a2: 0.2 [0.2, 0.4)
    - a3: 0.3 [0.4, 0.7)
    - a4: 0.2 [0.7, 0.9)
    - a5: 0.1 [0.9, 1.0)
  - Sequence: `a1 a4 a3 a4 a2 a5`
  - The notes show a diagram illustrating the narrowing of the interval for this sequence, resulting in a final arithmetic code of approximately `0.16536`.

### Part 2: Image Enhancement

#### 1. Histogram Equalization (Page 14-17)

- **Concept:** A method to adjust image intensities to enhance contrast by spreading out the most frequent intensity values. It aims to produce an output image with a flatter histogram.
- **Process:**
  1. Calculate the histogram \(h(r_k)\) of the input image, where \(r_k\) is the \(k\)-th intensity level.
  2. Calculate the Probability Density Function (PDF): \( P(r_k) = \frac{h(r_k)}{MN} \), where MN is the total number of pixels.
  3. Calculate the Cumulative Distribution Function (CDF): \( CDF(r*k) = \sum*{j=0}^{k} P(r_j) \).
  4. Transform the input intensity \(r_k\) to an output intensity \(s_k\):
     $$ s_k = T(r_k) = (L-1) \times CDF(r_k) $$
     where \(L\) is the number of possible intensity levels (e.g., 256 for 8-bit). \(s_k\) is typically rounded to the nearest integer.
- **Example 1 (Page 14):**
  - 4x4 image, 5-bit gray values (\(L=32\)). 16 distinct pixel values, each occurring once.
  - \(P(r_k) = \frac{1}{16} = 0.0625\) for each present \(r_k\).
  - The table shows the calculation of \(CDF\) and the transformed values \(s_k = (32-1) \times CDF(r_k)\).
- **Example 2 (Page 15):**
  - Gray levels \(L=8\) (0-7). Image size 64x64 = 4096 pixels.
  - Given histogram \(h(r_k)\).
  - The table shows \(PDF\), \(CDF\), and \(s_k = (8-1) \times CDF(r_k)\).
  - Page 16-17 show graphical representations: normalized histogram, equalized histogram, and the transformation function (transition graph).

#### 2. Spatial Filtering

- **Averaging Filter (Low-pass / Smoothing) (Page 18-19):**

  - Replaces each pixel value with the average of its neighbors (including itself) within a defined kernel (e.g., 3x3).
  - Reduces noise and blurs edges.
  - **Example:** 3x3 image `[2,5,8; 5,1,3; 4,7,2]` smoothed with a 3x3 averaging filter and zero-padding.
    - Calculations for each output pixel \(T(i,j)\) are shown by summing the 9 pixels in the 3x3 neighborhood (in the zero-padded input) and dividing by 9.
    - Output image (from notes): \( \begin{pmatrix} 1.44 & 2.67 & 1.89 \\ 2.67 & 4.11 & 2.89 \\ 1.89 & 2.44 & 1.44 \end{pmatrix} \) (values are rounded from fractions).

- **Median Filter (Non-linear / Smoothing) (Page 19):**

  - Replaces each pixel value with the median of its neighbors within a kernel.
  - Effective for removing salt-and-pepper noise while preserving edges better than averaging filters.
  - **Example:** Apply 3x3 median filter to a 4x4 image `[0,2,1,0; 5,2,3,8; 0,1,3,4; 3,0,7,2]`.
    - Borders of the output are set to zero (as per instruction "ignore border pixels, put zero on border of output").
    - For an inner pixel, e.g., \(T\_{22}\) (corresponding to input 2), the 3x3 neighborhood is `[0,2,1; 5,2,3; 0,1,3]`. Sorted: ``. Median is 2.
    - The notes show calculations for the 4 inner pixels of the output.

- **Laplacian Filter (High-pass / Sharpening) (Page 20, 21):**

  - Uses a 2nd derivative approximation to highlight regions of rapid intensity change (edges, fine details).
  - **Common Kernels:**
    - Negative center: \( \begin{pmatrix} 0 & 1 & 0 \\ 1 & -4 & 1 \\ 0 & 1 & 0 \end{pmatrix} \) or \( \begin{pmatrix} 1 & 1 & 1 \\ 1 & -8 & 1 \\ 1 & 1 & 1 \end{pmatrix} \)
    - Positive center: \( \begin{pmatrix} 0 & -1 & 0 \\ -1 & 4 & -1 \\ 0 & -1 & 0 \end{pmatrix} \) or \( \begin{pmatrix} -1 & -1 & -1 \\ -1 & 8 & -1 \\ -1 & -1 & -1 \end{pmatrix} \)
  - **Single Operation Enhancement:** To get a sharpened image directly, the Laplacian is added to (or subtracted from) the original image.
    - If using a negative center Laplacian (e.g., center -4), enhanced \(g(x,y) = f(x,y) - \nabla^2 f(x,y)\). This results in a filter mask like \( \begin{pmatrix} 0 & -1 & 0 \\ -1 & 5 & -1 \\ 0 & -1 & 0 \end{pmatrix} \).
    - The notes derive this type of mask for single-step enhancement.

- **Sobel Filter (High-pass / Edge Detection) (Page 21, 22):**

  - Uses 1st derivative approximations to detect edges and their orientation.
  - **Kernels:**
    - \(G_x = \begin{pmatrix} -1 & 0 & 1 \\ -2 & 0 & 2 \\ -1 & 0 & 1 \end{pmatrix}\) (detects vertical edges)
    - \(G_y = \begin{pmatrix} -1 & -2 & -1 \\ 0 & 0 & 0 \\ 1 & 2 & 1 \end{pmatrix}\) (detects horizontal edges)
  - **Gradient Magnitude:** \( G = \sqrt{G_x^2 + G_y^2} \) or \( G = |G_x| + |G_y| \)
  - **Gradient Angle:** \( \theta = \tan^{-1}\left(\frac{G_y}{G_x}\right) \)
  - **Objective of Sharpening (Page 22):** Remove blurring, highlight edges, emphasize intensity transitions.
  - **Example (Page 22):** 5x5 image, calculate gradient at a highlighted center pixel using a 3x3 Sobel mask.
    - The notes show the 3x3 sub-image, application of \(G_x\) and \(G_y\) masks, calculation of \(G_x=5\), \(G_y=-3\).
    - \(G = |5| + |-3| = 8\).
    - \(\theta = \tan^{-1}\left(\frac{-3}{5}\right) \approx -30.96^\circ\).

- **Prewitt Filter (High-pass / Edge Detection) (Page 21):**
  - Similar to Sobel, uses 1st derivative approximations.
  - **Kernels:**
    - \(G_x = \begin{pmatrix} -1 & 0 & 1 \\ -1 & 0 & 1 \\ -1 & 0 & 1 \end{pmatrix}\)
    - \(G_y = \begin{pmatrix} -1 & -1 & -1 \\ 0 & 0 & 0 \\ 1 & 1 & 1 \end{pmatrix}\)

### Part 3: Other Concepts

#### 1. Bit-Plane Slicing (Page 23-24)

- **Concept:** A method of representing an image by decomposing it into a series of binary images (bit-planes). Each bit-plane corresponds to a single bit position in the binary representation of the pixel intensities.
- **Main Goals:**
  1. Converting a gray-level image to a binary image (by selecting a significant bit-plane).
  2. Representing the image with fewer bits (image compression, especially if higher-order bit-planes are similar).
  3. Enhancing the image by focusing on main characteristics (e.g., contribution of higher vs. lower bits).
- **Example:** Image \(I = \begin{pmatrix} 150 & 60 \\ 60 & 210 \end{pmatrix}\), 8-bit.
  - 150 = `10010110`
  - 60 = `00111100`
  - 210 = `11010010`
  - The notes show the individual bit-planes (from MSB - Plane 8 down to LSB - Plane 1).
- **Reconstruction from Bit-Planes:** An image can be reconstructed by summing the contributions of its bit-planes, weighted by their corresponding powers of 2.
  - Reconstruction using bit-planes 8 (MSB) and 7:
    Image_reconstructed = \(2^7 \times \text{Plane}\_8 + 2^6 \times \text{Plane}\_7\)
  - The notes show the calculation for the example image.

#### 2. Number of Bits for Image Storage (Page 18)

- Formula: Bits = Width x Height x Number of bits per pixel.
- Number of bits per pixel is determined by the number of gray levels (\(G\)): bits = \( \log_2 G \).
- **Example:** 256x256 image, 32 gray levels.
  - Bits per pixel = \( \log_2 32 = 5 \).
  - Total bits = \( 256 \times 256 \times 5 = 327,680 \) bits.

#### 3. Root Mean Square Error (RMSE) (Page 9)

- A fidelity criterion to measure the difference between an original image \(f(x,y)\) and a processed/reconstructed image \(f'(x,y)\).
  $$ RMSE = \sqrt{\frac{1}{MN} \sum*{x=0}^{M-1}\sum*{y=0}^{N-1} [f'(x,y) - f(x,y)]^2} $$
- **Example:** If all pixel values in a decompressed image are increased by 2 compared to the original (i.e., \(f'(x,y) - f(x,y) = 2\) for all pixels).
  - For a 3x3 image: \( RMSE = \sqrt{\frac{1}{3 \times 3} \sum (2)^2} = \sqrt{\frac{1}{9} \times 9 \times 4} = \sqrt{4} = 2 \).
