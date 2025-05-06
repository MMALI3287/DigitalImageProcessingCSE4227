# Digital Image Processing: Topics & Subtopics

## Part 1. Intensity Transformations

### 1.1 Linear Transformations

#### 1.1.1 Identity Transformation

##### Definition

- $s = r$. The output gray level $s$ equals the input gray level $r$.
- Effect: no change.

##### Example

- Input pixel values $\{0,64,128,192,255\}$ → output identical.

##### Exercise

- Q: If you apply the identity transform to a 3×3 block
  $\begin{pmatrix}10&20&30\\40&50&60\\70&80&90\end{pmatrix}$, what is the result?
- A: Same block.

#### 1.1.2 Negative Transformation

##### Definition

- For an $L$-level image (levels $0..L-1$), $s = (L-1) - r$.
- Effect: "photographic negative"—dark ↔ bright.

##### Example

- 8-bit ($L=256$): $r=100$ → $s=155$.
- 3-bit ($L=8$): $r=2$ → $s=5$.

##### Exercise

- Q: Apply negative to $\begin{pmatrix}0&50&100\\150&200&250\end{pmatrix}$ in an 8-bit image.
- A: Subtract from 255 → $\begin{pmatrix}255&205&155\\105&55&5\end{pmatrix}$.

### 1.2 Logarithmic Transformations

##### Definition

- $s = c\cdot \log(1+r)$.
- $r$ in $[0,L-1]$.
- Expands dark levels, compresses bright levels.

**Choosing $c$**

- $c = \frac{(L-1)}{\log(1+R_{max})}$.

**Example**

- 8-bit image, $R_{max}=255$, $c=255/\log(256)\approx45.985$.
- If $r=15$, $s=45.985\cdot\log(16)\approx45.985\cdot2.773=127.5\to128$.

**Exercise**

- Q: Compute $s$ for $r=63$.
- A: $\log(64)=4.159$, so $s\approx45.985\cdot4.159\approx191$.

### 1.3 Power-Law (Gamma) Transformations

**Definition**

- $s = c\,r^\gamma$, $r\in[0,1]$ (normalize first)
- $\gamma<1$: map dark→bright (expansion of dark).
- $\gamma>1$: map bright→dark.

**Gamma Correction**

- To compensate for display devices: $s = r^{1/\gamma}$.

**Example**

- Let $r=0.25$, $\gamma=0.5$, $c=1$: $s = (0.25)^{0.5}=0.5$.

**Exercise**

- Q: For $\gamma=2$, what is output for $r=0.5$?
- A: $s=(0.5)^2=0.25$.

### 1.4 Piecewise-Linear Transformations

These are defined by breaking the input range into segments, each with its own linear mapping.

#### 1.4.1 Contrast Stretching

**Definition**

- Map an input interval $[r_1,r_2]$ to the full display range $[s_1,s_2]$.
- Formula:
  $s = \begin{cases}
  s_1, & r \leq r_1,\\
  \frac{(r-r_1)(s_2-s_1)}{r_2-r_1}+s_1, & r_1<r<r_2,\\
  s_2, & r\geq r_2.
  \end{cases}$

**Example**

- 8-bit image, want to map $[50,200]\to[0,255]$.
- For $r=125$: $s = \frac{(125-50)\cdot255}{200-50}= \frac{75\cdot255}{150}=127.5\to128$.

**Exercise**

- Q: Compute $s$ for $r=175$.
- A: $\frac{(175-50)\cdot255}{150}= \frac{125\cdot255}{150}\approx212.5\to213$.

#### 1.4.2 Intensity-Level Slicing

**Definition**

- Highlight a range $[a,b]$.
- Binary mode:
  $s=\begin{cases}
  L-1,& a\leq r\leq b,\\
  0, & \text{otherwise.}
  \end{cases}$
- Preserve mode:
  $s=\begin{cases}
  r,& a\leq r\leq b,\\
  \text{(dim value)},&\text{otherwise}.
  \end{cases}$

**Example**

- 8-bit, highlight [100–150] in white: $r=120\to255$; $r=80\to0$.

**Exercise**

- Q: For [50–100], preserve output, others → 0. What is $s$ at $r=75, r=125$?
- A: $s(75)=75; s(125)=0$.

#### 1.4.3 Thresholding

**Definition**

- Convert to binary by a single threshold $T$:
  $s=\begin{cases}
  0,& r< T,\\
  L-1,& r\geq T.
  \end{cases}$

**Example**

- 8-bit, $T=128$: Values below 128 → 0; ≥128 →255.

**Exercise**

- Q: Apply to $[120,130,140,150]$.
- A: $[0,255,255,255]$.

#### 1.4.4 Bit-Plane Slicing

**Definition**

- Decompose each pixel's 8-bit binary into 8 bit-planes.
- Plane $k$ holds the $k$th bit of each pixel (0 or 1).

**Reconstruction**

- $I(x,y)=\sum_{k=0}^7 b_k(x,y)\,2^k$.

**Example**

- Pixel 150 → binary `10010110`.
- Plane 7 (MSB) =1; Plane 6=0; …; Plane 1=1; Plane 0 (LSB)=0.

**Exercise**

- Q: For pixel 45 (`00101101`), what is bit-plane 3?
- A: Bits are numbered 0–7 LSB→MSB. Plane 3 = the 4th bit from right = 1.

---

## Part 2. Histogram Processing

### 2.1 Histogram Definition and Properties

##### Definition

- For a digital image of size $M\times N$, with gray levels $0,1,\dots,L-1$, the histogram $h(r_k)$ is the number of pixels having gray level $r_k$.
- $h(r_k) = \#\{(x,y)\mid f(x,y)=r_k\},\quad k=0,\dots,L-1.$
- $\sum_{k=0}^{L-1}h(r_k)=MN$.

##### Example

Consider the 3×3 image
$I=\begin{pmatrix}
0 & 1 & 2\\
1 & 2 & 3\\
0 & 1 & 3
\end{pmatrix}.$  
Gray-level counts: $h(0)=2,\;h(1)=3,\;h(2)=2,\;h(3)=2$.

##### Practice

- Q: Compute the histogram of $\begin{pmatrix}1&2&1&0\\0&2&3&1\end{pmatrix}$.
- A: Levels $0,1,2,3$: counts $h(0)=2,\;h(1)=3,\;h(2)=2,\;h(3)=1$.

### 2.2 Normalized Histogram (PDF)

**Definition**

- The normalized histogram or probability density function is
  $p(r_k) = \frac{h(r_k)}{MN},\quad \sum_{k=0}^{L-1}p(r_k)=1.$

**Example**

- From 2.1, $MN=9$.
- $p(0)=2/9,\;p(1)=3/9,\;p(2)=2/9,\;p(3)=2/9.$

**Practice**

- Q: For histogram $h=\{2,3,2,1\}$ of a 2×4 image, give $p(r_k)$.
- A: $MN=8$. $p=\{2/8,3/8,2/8,1/8\}=\{0.25,0.375,0.25,0.125\}.$

### 2.3 Cumulative Distribution Function (CDF)

**Definition**

- The CDF is $\mathrm{CDF}(r_k) = \sum_{j=0}^k p(r_j).$

**Example**

- From 2.2:
  $\mathrm{CDF}(0)=2/9$,
  $\mathrm{CDF}(1)=5/9$,
  $\mathrm{CDF}(2)=7/9$,
  $\mathrm{CDF}(3)=1.$

**Practice**

- Q: Compute CDF for $p=\{0.25,0.375,0.25,0.125\}$.
- A: CDF = $\{0.25,0.625,0.875,1.0\}$.

### 2.4 Histogram Sliding

**Definition**

- A simple intensity shift: $s = r + \Delta$, clipped to $[0,L-1]$.
- Effect: histogram shifts right ($\Delta>0$) or left ($\Delta<0$).

**Example**

- 8-bit image: $\Delta=50$. Pixel $r=100\to s=150$.

**Practice**

- Q: Shift $\begin{pmatrix}10&20\\30&40\end{pmatrix}$ by $\Delta=30$.
- A: $\begin{pmatrix}40&50\\60&70\end{pmatrix}$.

### 2.5 Histogram Stretching (Contrast Stretching)

**Definition**

- Given input range $[r_{\min},r_{\max}]$, map to $[0,L-1]$:
  $s = \frac{(r - r_{\min})(L-1)}{r_{\max} - r_{\min}}.$

**Example**

- 8-bit image with $r_{\min}=50,\;r_{\max}=200$.
- For $r=125$:
  $s = \frac{(125-50)\cdot255}{150} = \frac{75\cdot255}{150}=127.5\to128.$

**Practice**

- Q: Stretch $[30,180]\to[0,255]$. What is $s$ at $r=100$?
- A: $\frac{(100-30)255}{150}=\frac{70\cdot255}{150}=119$.

### 2.6 Histogram Equalization

**Definition**

- Define transform
  $s_k = T(r_k) = (L-1)\sum_{j=0}^k p(r_j) = (L-1)\,\mathrm{CDF}(r_k).$
- Apply $s = T(r)$ to each pixel.

**Worked Example**

- Use the 3×3 image from 2.1. $L=4$.
- CDF values: $\{2/9,5/9,7/9,1\}$.
- Mapping:
  $s(0)=3\cdot2/9=0.67\to1,$
  $s(1)=3\cdot5/9=1.67\to2,$
  $s(2)=3\cdot7/9=2.33\to2,$
  $s(3)=3\cdot1=3.$
- Equalized image:
  $\begin{pmatrix}
  s(0)&s(1)&s(2)\\
  s(1)&s(2)&s(3)\\
  s(0)&s(1)&s(3)
  \end{pmatrix}
  =
  \begin{pmatrix}
  1&2&2\\
  2&2&3\\
  1&2&3
  \end{pmatrix}.$

**Practice**

- Q: Equalize $\{0,0,1,1,2,3\}$ with $L=4$.
- A: Histogram $h=\{2,2,1,1\}$,
  $p=\{2/6,2/6,1/6,1/6\}$,
  CDF=$\{1/3,2/3,5/6,1\}$.
  $s=\{1,2,2,3\}$.
  New sequence: $\{1,1,2,2,2,3\}$.

### 2.7 Histogram Specification (Matching)

**Definition**

- Given input CDF $S(r)=\sum_{j=0}^r p_i(r_j)$ and desired CDF $G(z)=\sum_{j=0}^z p_z(z_j)$.
- Compute $s=T(r)=(L-1)\,S(r)$.
- Find for each $s$ the $z$ such that $G(z)$ is nearest to $s/(L-1)$.

**Example**

- Input PDF $p_i=\{0.5,0.5\}$ on levels $\{0,1\}$. Desired uniform on $\{0,1,2\}$: $p_z=\{1/3,1/3,1/3\}$.
- Input CDF $S=\{0.5,1\}$. Mapping $s=T(r)\times2$:
  $r=0\to s=0.5\cdot2=1$,
  $r=1\to s=1\cdot2=2$.
- Desired CDF $G=\{1/3,2/3,1\}$.
- Find $z$ with $G(z)\approx1/2\to z=1$; and $G(z)\approx1\to z=2$.
- Result: $r=0\to z=1$, $r=1\to z=2$.

**Practice**

- Q: Match histogram of $\{0,1,2\}$ with $p_i=\{0.2,0.5,0.3\}$ to $p_z=\{0.4,0.6\}$ on levels $\{0,1\}$.
- A: Compute CDFs and mappings similarly.

---

## Part 3. Spatial Filtering (Spatial Domain)

### 3.1 Convolution Process

**Definition**

- The convolution of image $f$ with kernel $w$ of size $(2a+1)\times(2b+1)$ is
  $g(i,j)=\sum_{m=-a}^{a}\sum_{n=-b}^{b} w(m,n)\,f(i-m,j-n).$
- Note the kernel is "flipped" before sliding.

**Example**

- $f=\begin{pmatrix}1&2&3\\4&5&6\\7&8&9\end{pmatrix},\;
  w=\begin{pmatrix}0&-1&0\\-1&5&-1\\0&-1&0\end{pmatrix}.$
- Center pixel $(i,j)=(2,2)$:
  $g(2,2)=
  (1\cdot0+2\cdot(-1)+3\cdot0)+
  (4\cdot(-1)+5\cdot5+6\cdot(-1))+
  (7\cdot0+8\cdot(-1)+9\cdot0)
  =-2 + ( -4+25-6 ) + (-8) =5.$

**Practice**

- Q: Convolve the same $f$ with $w=\begin{pmatrix}1&0&-1\\1&0&-1\\1&0&-1\end{pmatrix}$ at center.
- A: $g(2,2)=(1-3)+(4-6)+(7-9) = -2 -2 -2 = -6$.

### 3.2 Padding Methods

#### 3.2.1 Zero Padding

- Pad image border with zeros. Simple, but can introduce dark border artifacts.

#### 3.2.2 Border Replication

- Pad by replicating nearest border pixel value. Reduces artificial darkening.

#### 3.2.3 Discard Borders

- Compute $g(i,j)$ only where the full kernel fits inside the original image—output is smaller by the kernel radius.

### 3.3 Smoothing (Low-Pass) Filters

Purpose: blur to reduce noise and fine detail.

#### 3.3.1 Mean Filter

**Definition**

- Kernel of size $N\times N$ with all weights $1/N^2$.

**Example**

- 3×3 mean on $\begin{pmatrix}10&20&30\\20&30&40\\30&40&50\end{pmatrix}$.
- Sum of neighborhood = $10+20+30+20+30+40+30+40+50=270$.
- Mean = $270/9=30$.

**Practice**

- Q: What is the filtered value at center of $\begin{pmatrix}1&2&3\\4&5&6\\7&8&9\end{pmatrix}$?
- A: Sum=45, mean=45/9=5.

#### 3.3.2 Weighted Average (Gaussian) Filter

**Definition**

- Weights approximate a discrete Gaussian; e.g., 3×3 $\tfrac1{16}\begin{pmatrix}1&2&1\\2&4&2\\1&2&1\end{pmatrix}$.

**Example**

- Same data as 3.3.1. Weighted sum
  $(10+2·20+30 + 2·20+4·30+2·40 + 30+2·40+50)/16 = 270/16=16.875\to17$.

**Practice**

- Q: Compute on $\begin{pmatrix}1&1&1\\1&9&1\\1&1&1\end{pmatrix}$.
- A: Sum numerator = $(1+2·1+1)+(2·1+4·9+2·1)+(1+2·1+1)=(1+2+1)+(2+36+2)+(1+2+1)=4+40+4=48.$
  Output=48/16=3.

#### 3.3.3 Median Filter

**Definition**

- Replace center by median of neighborhood. Excellent for salt-and-pepper noise.

**Example**

- Neighborhood $\{10,200,10;200,30,200;10,200,10\}$. Sorted
  $\{10,10,10,10,30,200,200,200,200\}$. Median=30.

**Practice**

- Q: Median of $\{1,2,3;4,100,6;7,8,9\}$?
- A: Sorted $\{1,2,3,4,6,7,8,9,100\}$. Median=6.

#### 3.3.4 Min and Max Filters

**Definition**

- Min-filter yields the neighborhood minimum—useful for removing bright spikes.
- Max-filter yields the maximum—useful for removing dark spots.

**Example**

- On $\{1,200,3;4,5,6;7,8,9\}$: min=1, max=200.

**Practice**

- Q: Min/max of $\{0,3,1;2,5,4;6,7,8\}$?
- A: min=0, max=8.

### 3.4 Sharpening (High-Pass) Filters

Purpose: emphasize edges and fine detail.

#### 3.4.1 First-Derivative Filters (Gradient)

##### 3.4.1.1 Sobel Operator

**Definition**

- Two 3×3 kernels $G_x,G_y$ approximate derivatives.
  $G_x=\begin{pmatrix}-1&0&1\\-2&0&2\\-1&0&1\end{pmatrix},\quad
  G_y=\begin{pmatrix}-1&-2&-1\\0&0&0\\1&2&1\end{pmatrix}.$
- Gradient magnitude $G=\sqrt{G_x^2+G_y^2}$ or $|G_x|+|G_y|$.
- Angle $\theta=\tan^{-1}(G_y/G_x)$.

**Example**

- On $\begin{pmatrix}10&10&10\\10&0&10\\10&10&10\end{pmatrix}$:
  $G_x=-10-20-10+10+20+10=0,\;G_y=-10-20-10+10+20+10=0.$
- No edge.

**Practice**

- Q: Compute $G_x,G_y$ at center of $\begin{pmatrix}0&0&0\\0&10&0\\0&0&0\end{pmatrix}$.
- A: Both zero (isolated bright spot).

##### 3.4.1.2 Prewitt Operator

**Definition**

- Similar to Sobel but uniform weights:
  $P_x=\begin{pmatrix}-1&0&1\\-1&0&1\\-1&0&1\end{pmatrix},\;
  P_y=\begin{pmatrix}-1&-1&-1\\0&0&0\\1&1&1\end{pmatrix}.$

**Example & Practice**

- Analogous to Sobel above.

#### 3.4.2 Second-Derivative Filters

##### 3.4.2.1 Laplacian Filter

**Definition**

- Isotropic 2nd derivative:
  $\nabla^2f = f(x+1,y)+f(x-1,y)+f(x,y+1)+f(x,y-1)-4f(x,y)$.
- Kernel examples:
  $\begin{pmatrix}0&1&0\\1&-4&1\\0&1&0\end{pmatrix}$ or
  $\begin{pmatrix}1&1&1\\1&-8&1\\1&1&1\end{pmatrix}$.

**Example**

- On $\{1,2,3;4,5,6;7,8,9\}$ at center:
  $2+4+6+8 -4·5=20-20=0$.

**Practice**

- Q: Laplacian on $\{0,0,0;0,10,0;0,0,0\}$?
- A: $0+0+0+0-4·10=-40$.

##### 3.4.2.2 Combined Laplacian Enhancement Masks

**Definition**

- Sharpen by adding original: $g = f + \alpha\nabla^2 f$.
- Combined kernel example ($\alpha=1$): $\begin{pmatrix}0&-1&0\\-1&5&-1\\0&-1&0\end{pmatrix}$.

**Example**

- On $\{1,2,3;4,5,6;7,8,9\}$, center: 5 (see 3.1).

**Practice**

- Q: Apply $\begin{pmatrix}-1&-1&-1\\-1&9&-1\\-1&-1&-1\end{pmatrix}$ at center.
- A: $-(2+4+6+8) +9·5 - (1+3+7+9) = -20+45-20=5$.

##### 3.4.2.3 Laplacian of Gaussian (LoG)

**Definition**

- Filter $\nabla^2 (G_\sigma * f) = (\nabla^2 G_\sigma)* f$.
- Zero-crossings in LoG output indicate edges.

**Approximate 5×5 LoG kernel ($\sigma=1$)**:
$\frac{1}{16}\begin{pmatrix}
0&0&-1&0&0\\
0&-1&-2&-1&0\\
-1&-2&16&-2&-1\\
0&-1&-2&-1&0\\
0&0&-1&0&0
\end{pmatrix}.$

**Practice**

- Q: Why use LoG rather than Laplacian alone?
- A: Pre-smoothing reduces noise sensitivity.

### 3.5 Correlation and Template Matching

**Definition**

- Cross-correlation of image $f$ with template $w$:
  $R(i,j)=\sum_{m}\sum_{n} w(m,n)\,f(i+m,j+n).$
- Unlike convolution, $w$ is not flipped.

**Template Matching**

- Compute $R(i,j)$ for all candidate positions; pick peak response.

**Example**

- Template $w=\begin{pmatrix}1&0\\0&-1\end{pmatrix}$ on
  patch $f=\begin{pmatrix}2&3\\4&5\end{pmatrix}$ at a given location:
  $R=1\cdot2 +0\cdot3 +0\cdot4 +(-1)\cdot5 =2-5=-3$.

**Practice**

- Q: Given image $\begin{pmatrix}1&2&1\\2&1&0\\1&0&1\end{pmatrix}$,
  template $\begin{pmatrix}1&0\\0&1\end{pmatrix}$,
  find $R(1,1)$.
- A: Overlap top-left 2×2: $1·1 + 0·2 + 0·2 +1·1 =1+0+0+1=2$.

---

## Part 4. Edge and Feature Detection

### 4.1 Edge Types

**Definition**

- Step edge: abrupt change in intensity over one pixel.
- Ramp edge: gradual change over multiple pixels.
- Roof edge: intensity increases then decreases (peak).
- Ridge: narrow bright line on dark background.

**Worked Examples**

- Step: $\{0,0,0,255,255,255\}$.
- Ramp: $\{0,64,128,192,255\}$.
- Roof: $\{0,100,200,100,0\}$.
- Ridge: $\{0,0,255,0,0\}$.

**Practice**

- Q1: Classify $\{10,10,50,100,150\}$ and $\{0,200,100,200,0\}$.
- **Solution**
  - $\{10,10,50,100,150\}$ → ramp (gradual).
  - $\{0,200,100,200,0\}$ → roof (peak then valley).

### 4.2 Gradient-Based Edge Detection

**Definition**

- Gradient vector  
  $\nabla f = \bigl[\tfrac{\partial f}{\partial x},\,\tfrac{\partial f}{\partial y}\bigr]$.
- Magnitude $M = \sqrt{G_x^2 + G_y^2}$.
- Direction $\theta = \tan^{-1}(G_y/G_x)$.

##### 4.2.1 Sobel Operator

**Definition**

- Two 3×3 kernels $G_x,G_y$ approximate derivatives.
  $G_x=\begin{pmatrix}-1&0&1\\-2&0&2\\-1&0&1\end{pmatrix},\quad
  G_y=\begin{pmatrix}-1&-2&-1\\0&0&0\\1&2&1\end{pmatrix}.$
- Gradient magnitude $G=\sqrt{G_x^2+G_y^2}$ or $|G_x|+|G_y|$.
- Angle $\theta=\tan^{-1}(G_y/G_x)$.

**Example**

- On $\begin{pmatrix}10&10&10\\10&0&10\\10&10&10\end{pmatrix}$:
  $G_x=-10-20-10+10+20+10=0,\;G_y=-10-20-10+10+20+10=0.$
- No edge.

**Practice**

- Q: Compute $G_x,G_y$ at center of $\begin{pmatrix}0&0&0\\0&10&0\\0&0&0\end{pmatrix}$.
- A: Both zero (isolated bright spot).

##### 4.2.2 Prewitt Operator

**Definition**

- Similar to Sobel but uniform weights:
  $P_x=\begin{pmatrix}-1&0&1\\-1&0&1\\-1&0&1\end{pmatrix},\;
  P_y=\begin{pmatrix}-1&-1&-1\\0&0&0\\1&1&1\end{pmatrix}.$

**Example & Practice**

- Analogous to Sobel above.

### 4.3 Line Detection

**Definition**

- Use directional masks to detect lines at 0°, 90°, ±45°.
  $L*h=\begin{pmatrix}-1&2&-1\\-1&2&-1\\-1&2&-1\end{pmatrix},\;
  L_v=L_h^T,\;
  L*{+45}=\begin{pmatrix}2&-1&-1\\-1&2&-1\\-1&-1&2\end{pmatrix},\;
  L\_{-45}=\begin{pmatrix}-1&-1&2\\-1&2&-1\\2&-1&-1\end{pmatrix}.$

**Example**

- Detect horizontal line in $\begin{pmatrix}0&0&0\\10&10&10\\0&0&0\end{pmatrix}$:
  Convolution with $L_h$ → strong positive response.

**Practice**

- Q3: Apply $L_v$ to the same patch.
- **Solution**
  - No vertical line → response = 0.

### 4.4 Laplacian of Gaussian (LoG)

**Definition**

- LoG filter = Laplacian of a Gaussian–smoothed image:
  $\mathrm{LoG}(x,y)
  = \nabla^2\bigl[G_\sigma(x,y)\bigr]\;\*\;f(x,y).$
- Zero-crossings of LoG output mark edges.

**Approximate 5×5 LoG ($\sigma=1$)**:
$\frac1{16}\begin{pmatrix}
0&0&-1&0&0\\
0&-1&-2&-1&0\\
-1&-2&16&-2&-1\\
0&-1&-2&-1&0\\
0&0&-1&0&0
\end{pmatrix}.$

**Practice**

- Q4: Given LoG output $\begin{pmatrix}1&1&1\\1&0&-1\\1&-1&-1\end{pmatrix}$, where are zero-crossings?
- **Solution**
  - Between center (0) and any neighbor of opposite sign (e.g., right, down).

### 4.5 Marr-Hildreth Algorithm

**Steps**

1. Smooth with Gaussian $G_\sigma$.
2. Compute LoG.
3. Detect zero-crossings in LoG.
4. (Optional) Interpolate for sub-pixel accuracy.

**Example (1D)**  
Input $\{0,0,255,255\}$, Gaussian smoothing → $\{0,64,192,255\}$, LoG detects sign change between 64→192 → edge there.

**Practice**

- Q5: Why smooth before LoG?
- **Solution**
  - Reduces noise sensitivity; prevents spurious zero-crossings.

### 4.6 Canny Edge Detector

**Criteria**: detection, localization, single response.  
**Steps**

#### 4.6.1 Smoothing

- Convolve with Gaussian $G_\sigma$.

#### 4.6.2 Gradient Computation

- Use Sobel/Prewitt → $G_x,G_y$, compute $M$, $\theta$.

#### 4.6.3 Non-Maximum Suppression

- For each pixel, keep $M$ only if it is a local maximum along the gradient direction; else set to 0.

#### 4.6.4 Double Thresholding

- Two thresholds $T_H>T_L$:
  - Strong: $M\ge T_H$.
  - Weak: $T_L\le M< T_H$.
  - Discard: $M<T_L$.

#### 4.6.5 Edge Tracking by Hysteresis

- Start from strong edges, include weak edges connected to strong ones; discard isolated weak.

**Worked Example**
5×5 patch (center at (3,3)):  
$f_s:
\begin{pmatrix}
10&10&10&10&10\\
10&50&80&50&10\\
10&80&100&80&10\\
10&50&80&50&10\\
10&10&10&10&10
\end{pmatrix}$

1. Smoothing assumed done.
2. Sobel at center → $G_x=0,G_y=0$, $M=0$. Edges at pixels around center.
3. Non-max suppression → thin edges.
4. $T_H=70,T_L=30$. Pixels with $M>70$ strong; $30\le M\le70$ weak.
5. Link weak→strong via connectivity.

**Practice**

- Q6: At a pixel $M=60$ with neighbors $M=80$ (strong) and $M=20$, does it survive hysteresis?
- **Solution**
  - Yes—60 is weak but connected to 80, so kept.

---

## Part 5. Morphological Image Processing

### 5.1 Structuring Element

#### 5.1.1 Definition and Origin

- A small binary template $B\subset\mathbb{Z}^2$ with a designated origin.

#### 5.1.2 Fit, Hit, Miss

- Fit at $z$: every 1 in $B$ aligns with a 1 in $A$ when $B$'s origin is at $z$.
- Hit: at least one 1 in $B$ aligns with 1 in $A$.
- Miss: no alignment of 1's.

**Example**  
$A=\begin{pmatrix}1&1\\1&0\end{pmatrix},\;
B=\begin{pmatrix}1&0\\0&1\end{pmatrix}.$  
At origin=(1,1):  
Fit? Yes; Hit? Yes.

**Practice**

- Q7: For the same $A,B$, is there a hit at origin=(1,2)?
- **Solution**
  - Shift $B$ so its origin at A(1,2): overlap positions do not match → Hit? no.

### 5.2 Erosion \((A\ominus B)\)

**Definition**

- $A\ominus B = \{z\mid B_z\subseteq A\}$.
- Shrinks foreground.

**Example**

- $A=\begin{pmatrix}
1&1&1\\
1&1&1\\
1&1&1
\end{pmatrix},\;
B=\begin{pmatrix}1&1\\1&1\end{pmatrix}.$  
   Erosion →  
   $\begin{pmatrix}1&1\\1&1\end{pmatrix}$ at center.

**Practice**

- Q8: Erode  
  $\begin{pmatrix}1&1&0\\1&1&0\\0&0&0\end{pmatrix}$ by 2×2 ones.
- **Solution**
  - No location where full 2×2 fits → all zeros.

### 5.3 Dilation \((A\oplus B)\)

**Definition**

- $A\oplus B = \{z\mid (\,\hat B\,)\_z\cap A\neq\emptyset\}$.
- Expands foreground.

**Example**

- Same $A,B$.
- Dilation → border grows by one: a 4×4 block of ones (clipped to image).

**Practice**

- Q9: Dilate  
  $\begin{pmatrix}1&0\\0&0\end{pmatrix}$ by 2×2 ones.
- **Solution**
  - One location "hit" →  
    $\begin{pmatrix}1&1\\1&1\end{pmatrix}$.

### 5.4 Duality

- $(A\ominus B)^c = A^c\oplus \hat B$,
  $(A\oplus B)^c = A^c\ominus \hat B$.

**Practice**

- Q10: Verify on  
  $A=\{0,1,1,0\}$, $B=\{1,1\}$.
- **Solution**
  - Compute both sides; both yield same complement.

### 5.5 Opening \((A\circ B)\)

**Definition**

- $A\circ B = (A\ominus B)\oplus B$.
- Removes small objects; smoothes boundary.

**Example**

- $A$ has a 1-pixel spur; opening with 3×3 SE deletes spur.

**Practice**

- Q11: Open  
  $\begin{pmatrix}0&1&0\\1&1&1\\0&1&0\end{pmatrix}$ with 2×2 ones.
- **Solution**
  - Erosion kills plus-shape → empty; dilation remains empty.

### 5.6 Closing \((A\bullet B)\)

**Definition**

- $A\bullet B = (A\oplus B)\ominus B$.
- Fills small holes; connects nearby objects.

**Example**

- A "C" shape closes into "O" if hole small relative to SE.

**Practice**

- Q12: Close  
  $\begin{pmatrix}1&1&1\\1&0&1\\1&1&1\end{pmatrix}$ with 2×2 ones.
- **Solution**
  - Dilation → full block; erosion leaves full block (all ones).

### 5.7 Boundary Extraction

- Inner boundary: $A - (A\ominus B)$.
- Outer boundary: $(A\oplus B) - A$.

**Example**  
$A=\{0,1,1,0\},B=\{1,1\}$.  
Inner = pixels lost by erosion. Outer = pixels added by dilation.

**Practice**

- Q13: For  
  $\begin{pmatrix}0&1&1&0\end{pmatrix}$, B=2×1 ones, find inner boundary.
- **Solution**
  - Erosion → $\{0,0,1,0\}$.
    Inner = $\{0,1,0,0\}$.

### 5.8 Morphological Gradient

**Definition**

- $(A\oplus B)\;-\;(A\ominus B)$.
- Highlights object contours.

**Example**

- On a 3×3 square, gradient gives a ring of ones.

### 5.9 Hit-or-Miss Transform

**Definition**

- $A\circledast (B_1,B_2)
= (A\ominus B_1)\;\cap\;(A^c\ominus B_2)$.
- Detects a specific pattern of foreground ($B_1$) and background ($B_2$).

**Example**

- Detect 1-pixel isolated dots: $B_1=\{1\},B_2=\text{eight surrounding 0's}$.

**Practice**

- Q14: Find pixels in  
  $\begin{pmatrix}0&0&0\\0&1&0\\0&0&0\end{pmatrix}$ matching HMT.
- **Solution**
  - The center matches → output a 1 at center.

### 5.10 Corner Detection (Morphological)

**Definition**

- Use four L-shaped SEs in HMT to find convex corners in all orientations.

**Example**

- An "L" shaped pattern in image → HMT with same "L" detects corner pixel.

### 5.11 End-Point Detection

**Definition**

- HMT with SEs that match a 1-pixel with exactly one neighbor → finds line ends.

### 5.12 Thinning

**Definition**

- Iteratively delete border pixels (via HMT) without breaking connectivity until no more deletions possible.

### 5.13 Thickening

**Definition**

- Iteratively add pixels (via HMT) to grow objects until no more additions.

---

## Part 6. Color Image Processing

### 6.1 Human Visual System

#### 6.1.1 Cone Types (L-, M-, S-cones)

**Definition**

- The retina contains three classes of cones, each with a distinct peak sensitivity:
  - S-cones ("short"): peak ≈ 445 nm (blue)
  - M-cones ("medium"): peak ≈ 535 nm (green)
  - L-cones ("long"): peak ≈ 565 nm (red)
- Relative populations: L ≈ 64 %, M ≈ 32 %, S ≈ 2 %.
- Each cone's response to light of wavelength $\lambda$ is proportional to the product of stimulus intensity and its spectral sensitivity curve.

**Example**

- Suppose unit‐intensity monochromatic lights at 450 nm, 535 nm, 565 nm produce normalized cone excitations:
  - At 450 nm: S=1.0, M=0.2, L=0.05
  - At 535 nm: S=0.1, M=1.0, L=0.8
  - At 565 nm: S=0.01, M=0.2, L=1.0

If a scene has 50 units at 450 nm, 100 units at 535 nm, 80 units at 565 nm, total cone responses are:  
\[
R_S = 50·1.0 + 100·0.1 + 80·0.01 = 50 + 10 + 0.8 = 60.8,
\]  
\[
R_M = 50·0.2 + 100·1.0 + 80·0.2 = 10 + 100 + 16 = 126,
\]  
\[
R_L = 50·0.05 + 100·0.8 + 80·1.0 = 2.5 + 80 + 80 = 162.5.
\]

**Practice**

- Q6.1: If you have only 200 units of 565 nm light, what are the cone responses?
- **Solution**
  - At 565 nm: S=0.01, M=0.2, L=1.0
  - $R_S = 200·0.01 = 2,\quad R_M = 200·0.2 = 40,\quad R_L = 200·1.0 = 200.$

#### 6.1.2 Visual Pathway

**Definition**

- The path from retina to visual cortex involves:
  - Optic nerve
  - Optic chiasm (partial crossing)
  - Optic tract
  - Lateral geniculate nucleus (LGN)
  - Optic radiation
  - Primary visual cortex (V1)

**Example**

- Visual field defect: e.g., homonymous hemianopia (optic tract or radiation damage).

**Practice**

- Q6.2: What would damage to the optic chiasm cause?
- **Solution**
  - Bitemporal hemianopia (loss of peripheral vision in both eyes).

### 6.2 Color Models

#### 6.2.1 RGB (Additive)

##### 6.2.1.1 RGB Color Cube

**Definition**

- Visualize all colors as points in a unit cube $[0,1]^3$, axes = R, G, B.
- Origin (0,0,0)=black; (1,1,1)=white; along diagonal = grays.

**Example**

- Pure Red = (1,0,0), Green = (0,1,0), Blue = (0,0,1).
- Mid‐gray = (0.5,0.5,0.5).

**Practice**

- Q6.2: What is the point for medium yellow?
- **Solution**
  - Yellow = Red + Green → (1,1,0) normalized → (1,1,0). Medium yellow = (0.5,0.5,0).

##### 6.2.1.2 Additive Mixing

**Definition**

- Intensity of each channel adds.
- R+G = Yellow; G+B = Cyan; B+R = Magenta; R+G+B = White.

**Example**

- Mix R=(0.8,0,0) with G=(0,0.6,0) → (0.8,0.6,0) (bright yellow).

**Practice**

- Q6.3: Mix B=(0,0,1) with G=(0,0.5,0) → ?
- **Solution**
  - (0,0.5,1) (bright cyan‐blue).

##### 6.2.1.3 Luminance Approximation

**Definition**

- Convert RGB to gray via weighted sum:
  $Y = 0.299\,R + 0.587\,G + 0.114\,B.$

**Example**

- Pixel (R,G,B)=(0.6,0.2,0.1) →
  $Y=0.299·0.6+0.587·0.2+0.114·0.1
  =0.1794+0.1174+0.0114=0.3082.$

**Practice**

- Q6.4: For (R,G,B)=(255,100,50) in 8-bit, find gray.
- **Solution**
  - Normalize: R=1, G≈0.392, B≈0.196.
  - $Y=0.299·1+0.587·0.392+0.114·0.196=0.299+0.230+0.022=0.551.$
  - Back to 8-bit: 0.551·255≈140.

#### 6.2.2 CMY(K) (Subtractive)

##### 6.2.2.1 CMY Mixing

**Definition**

- CMY = 1 – corresponding RGB channel (normalized).
  $C = 1 – R,\; M = 1 – G,\; Y = 1 – B.$
- Adding inks subtracts light wavelengths.

**Example**

- If a display shows (R,G,B)=(0.2,0.5,0.8), the equivalent printer inks are
  $C=0.8,\; M=0.5,\; Y=0.2.$

**Practice**

- Q6.5: For R=0.4, G=0.6, B=0.2, find C,M,Y.
- **Solution**
  - C=0.6, M=0.4, Y=0.8.

##### 6.2.2.2 Pigment Absorption/Reflection

**Definition**

- A pigment with Cyan dye absorbs Red and reflects Green+Blue → appears cyan.
- Magenta absorbs Green, Yellow absorbs Blue.

**Example**

- A patch printed with C=0.7, M=0.2, Y=0.1 yields approximate reflectances:
  R=1–C=0.3, G=1–M=0.8, B=1–Y=0.9 → color ≈ (0.3,0.8,0.9).

**Practice**

- Q6.6: A printer uses C=0.1, M=0.9, Y=0.4. What is printed color?
- **Solution**
  - R=0.9, G=0.1, B=0.6 → (0.9,0.1,0.6).

#### 6.2.3 HSI Model

**Definition**

- Separates Intensity (I) from Color (Hue H, Saturation S).
- Normalized RGB in [0,1].
- $I = \frac{1}{3}(R+G+B)$.
- $S = 1 - \frac{3}{R+G+B}\min(R,G,B)$.
- $H = \begin{cases}
  \cos^{-1}\!\bigl[\frac{(R-G)+(R-B)}{2\sqrt{(R-G)^2+(R-B)(G-B)}}\bigr], & B\leq G,\\
  360° - \cos^{-1}[\text{same}], & B > G.
  \end{cases}$

**Example**

- (R,G,B)=(0.6,0.4,0.2):
  - $I=(0.6+0.4+0.2)/3=0.4$.
  - $\min=0.2 \Rightarrow S=1-\frac{3·0.2}{1.2}=1-0.5=0.5$.
  - Numerator = $(R-G)+(R-B)=0.2+0.4=0.6$.
  - Denominator = $2\sqrt{(0.2)^2+(0.4·0.2)}=2\sqrt{0.04+0.08}=2\sqrt{0.12}=0.6928$.
  - $\cos^{-1}(0.8660)=30°$.
  - Since $B=0.2\leq G=0.4$, $H=30°$.
  - HSI=(30°,0.5,0.4).

**Practice**

- Q6.7: Compute HSI for (R,G,B)=(0.2,0.7,0.7).
- **Solution**
  - $I=(0.2+0.7+0.7)/3=0.5333$.
  - $\min=0.2$, sum=1.6 → $S=1-\frac{3·0.2}{1.6}=1-0.375=0.625$.
  - Num=$(0.2-0.7)+(0.2-0.7)=-1.0$; Den=$2\sqrt{(-0.5)^2+(-0.5)^2}=2·0.707=1.414$.
  - $\cos^{-1}(-0.707)=135°$. Since $B=0.7>G=0.7$ (equal, choose $H=360-135=225°$).

---

## Part 7. Image Segmentation

### 7.1 Fundamentals

**Definition**

- Partition an image into meaningful regions (objects vs background).
- Each pixel is assigned a label; regions ideally homogeneous by some criterion.

#### 7.1.1 Similarity-Based Methods

##### 7.1.1.1 Thresholding

**Definition**

- Basic segmentation by intensity:
  $g(x,y)= \begin{cases}
  1, & f(x,y)>T,\\
  0, & f(x,y)\leq T.
  \end{cases}$
- Global threshold $T$ chosen via histogram valley or Otsu's method.

**Example**

- Image intensities $\{10,50,100,150,200,250\}$, two histogram peaks at 50,200 → $T=(50+200)/2=125$.
- Binary: $\{0,0,0,1,1,1\}$.

**Practice**

- Q7.1: Given $\{20,80,90,120,200\}$ and $T=100$, segment?
- **Solution**
  - Values >100 →1: $\{0,0,0,1,1\}$.

##### 7.1.1.2 Region-Based (Growing, Split & Merge)

**Definition**

- Region Growing: start from one or more seed points, iteratively add neighboring pixels whose intensities differ by ≤thr.
- Split & Merge: recursively split image into quadrants until each region is "homogeneous" (range ≤thr), then merge adjacent homogeneous regions.

**Example (Region Splitting)**

- 4×4 image:
  $\begin{pmatrix}
  50&52&200&202\\
  49&51&201&203\\
  48&50&202&204\\
  47&49&203&205
  \end{pmatrix}, \text{thr}=5.$
- Whole 4×4 range=205–47=158>5 → split into four 2×2 blocks.
- Each 2×2 range=≤5 → stop splitting.

**Practice**

- Q7.2: For thr=3, split the top‐left 2×2 block $\{50,52;49,51\}$.
- **Solution**
  - Range=52–49=3≤thr → no split.

##### 7.1.1.3 Template Matching

**Definition**

- Detect pixels matching a known template $t(u,v)$ by normalized cross‐correlation:
  $C(x,y)=
  \frac{\sum_{u,v}[f(x+u,y+v)-\mu_f][t(u,v)-\mu_t]}
  {\sqrt{\sum(f-\mu_f)^2\sum(t-\mu_t)^2}}.$
- Peaks in $C$ above threshold indicate matches.

**Example**

- Image patch
  $\begin{pmatrix}100&102\\101&103\end{pmatrix}$,
  template $\begin{pmatrix}1&1\\1&1\end{pmatrix}$.
- Uniform template → correlation tests local mean stability.

**Practice**

- Q7.3: Compute unnormalized correlation at (x,y) for above:
- **Solution**
  - $\sum f·t =100+102+101+103=406$.

#### 7.1.2 Discontinuity-Based Methods

##### 7.1.2.1 Edge-Based Segmentation

**Definition**

- Use edge detector (e.g., Canny) → binary edge map.
- Post‐process edges (link, close gaps) → closed contours.
- Fill interior regions to get segmented objects.

**Example**

- Binary image
  $\begin{pmatrix}
  0&0&0&0\\
  0&1&1&0\\
  0&1&1&0\\
  0&0&0&0
  \end{pmatrix}$.
- Edges at boundary of 2×2 block. Fill interior → region label.

**Practice**

- Q7.4: Apply Sobel to
  $\begin{pmatrix}0&0&0\\0&10&0\\0&0&0\end{pmatrix}$, threshold $T=5$.
- **Solution**
  - Gradient=0 → no edges → no region.

##### 7.1.2.2 Watershed

**Definition**

- Interpret gradient magnitude as topography.
- Flood from regional minima; build barriers where floods meet → watershed lines segment regions.

**Example (1-D)**

- Gradient $[5,2,3,1,4,6]$.
- Minima at 1 → catchment at index 3. Flooding partitions neighbors.

**Practice**

- Q7.5: In 1-D data $[3,1,2,1,3]$, minima at indices 1,3. Label catchment regions?
- **Solution**
  - Values rise from minima → region1 covers indices 0–2; region2 covers indices 2–4; watershed at index 2.

### 7.2 Canny Edge Detection (Summary)

- See Part 4.6. Used for edge-based segmentation.

---

## Part 8. Image Compression and Coding

### 8.1 Types of Redundancy

#### 8.1.1 Coding Redundancy

**Definition**

- Occurs when we use more bits than necessary to represent symbols.
- If an image has intensity levels $0\dots L-1$, naïvely we need $\log_2L$ bits per pixel, but if some levels occur more often we can use shorter codes for them (e.g., Huffman).

**Example**

- 8‐bit image ($L=256$) → 8 bits per pixel. If only 16 levels actually occur, you could use 4 bits per pixel instead.

**Practice**

- Q8.1.1: An image uses only levels $\{0,1,2,3\}$. What is the minimum bits/pixel needed?
- **Solution**
  - $\log_2 4 = 2$ bits/pixel.

#### 8.1.2 Spatial/Temporal Redundancy

**Definition**

- Spatial: neighboring pixels are often similar (correlated).
- Temporal: successive frames in video are similar.

**Example**

- A region of sky: all pixels ∼ blue. Run‐length or predictive coding exploits this.

**Practice**

- Q8.1.2: In a 1D scanline $[10,10,11,11,11,12]$, identify runs and suggest a simple predictor.
- **Solution**
  - Runs: 10×2, 11×3, 12×1. Predictor: predict next pixel = current; error sequence small: 0,1,0,0,1.

#### 8.1.3 Irrelevant Information

**Definition**

- Bits that the human visual system cannot perceive, e.g., very fine high‐frequency texture.
- Exploited by lossy schemes (JPEG).

**Example**

- High‐frequency checker noise superimposed on smooth background can be dropped.

**Practice**

- Q8.1.3: Why can JPEG discard some high‐DCT‐coefficients?
- **Solution**
  - Because human eye is less sensitive to fine high‐frequency changes.

### 8.2 Entropy

**Definition**

- Shannon entropy measures the average information per symbol:
  $H = -\sum_{k} p_k \log_2 p_k,$
  where $p_k$ is the probability of symbol $k$.
- Unit: bits/symbol.

**Properties**

- $H\leq \log_2 M$ for an alphabet of size $M$.
- Lower bound on average code length for any lossless code.

**Worked Example**

- Alphabet $\{A,B,C\}$ with $p(A)=0.5,\;p(B)=0.3,\;p(C)=0.2$.
- $H = -[0.5\log_2 0.5 + 0.3\log_2 0.3 + 0.2\log_2 0.2]$
  $= -[0.5(-1) + 0.3(-1.737) + 0.2(-2.322)]$
  $= 0.5 +0.521+0.464 =1.485\text{ bits/symbol.}$

**Practice**

- Q8.2: Compute entropy for $p=\{0.4,0.4,0.2\}$.
- **Solution**
  - $-[0.4\log_2 0.4 +0.4\log_2 0.4 +0.2\log_2 0.2]$
    $= -[2·0.4·(-1.322)+0.2·(-2.322)]$
    $=2·0.529+0.464=1.522$ bits/symbol.

### 8.3 Compression Ratio and Relative Data Redundancy

**Definition**

- Compression ratio
  $C_R = \frac{\text{Original size}}{\text{Compressed size}}.$
- Relative data redundancy
  $R_D = 1 - \frac{1}{C_R}.$

**Worked Example**

- Original image: 100 kB; compressed: 25 kB.
- $C_R = \frac{100}{25}=4,$
  $R_D =1-\frac{1}{4}=0.75\;(75\%\text{ redundant}).$

**Practice**

- Q8.3: Original 512×512×8 bits = 2,048 kbits. Compressed to 512 kbits. Compute $C_R,R_D$.
- **Solution**
  - $C_R=2048/512=4,\;R_D=1-1/4=0.75.$

### 8.4 Huffman Coding

##### Definition

- A variable‐length, prefix‐free code minimizing average length given symbol probabilities.
- Build a binary tree: at each step merge two least‐probable symbols.

##### Worked Example

- Symbols \(\{A,B,C,D,E\}\) with counts \(\{6,4,2,1,1\}\) (total 14).
- Probabilities: \(p(A)=6/14,\dots,p(E)=1/14\).
- Build tree:

1. Merge D(1) + E(1) → node DE(2)
2. Merge C(2) + DE(2) → CDE(4)
3. Merge B(4) + CDE(4) → BCDE(8)
4. Merge A(6) + BCDE(8) → root(14)  
   Assign 0/1 on branches:  
   A: 0 (length 1)  
   BCDE: 1  
   B: 10 (2)  
   CDE: 11  
   C: 110 (3)  
   DE: 111  
   D: 1110 (4)  
   E: 1111 (4)  
   Average code length  
   \[
   L\_{\rm avg}
   = \sum p(k)\,l(k)
   = \frac6{14}·1 + \frac4{14}·2 + \frac2{14}·3 + \frac1{14}·4 + \frac1{14}·4
   = \frac{6 + 8 + 6 + 4 + 4}{14}
   = \frac{28}{14} = 2\text{ bits/symbol.}
   \]

##### Practice

- Q8.4: For counts \(\{5,4,3,2,1\}\), build Huffman codes and compute \(L\_{\rm avg}\).  
  (Solution is analogous: merge smallest iteratively, assign bits, sum.)

### 8.5 LZW (Lempel–Ziv–Welch) Encoding

**Definition**

- A dictionary‐based, lossless algorithm.
- Initialize dictionary with all single symbols (codes 0–\(L-1\)).
- Read longest match \(S\) in dictionary, output its code, then add \(S +\) next character to dictionary.

**Worked Example**

- Sequence “ABABABA”. Alphabet {A,B}. Initial dict: A→65, B→66. Next code=256.

1. S=“A”, C=“B” → output 65; add “AB”→256; S←“B”.
2. S=“B”, C=“A” → output 66; add “BA”→257; S←“A”.
3. S=“A”, C=“B” → “AB” in dict as 256; output 256; add “ABA”→258; S←“”.  
   Actually convention: after outputting 256, S←“B”.
4. S=“B”, C=“A” → “BA”→257; output 257; add “BAB”→259; S←“A”.
5. S=“A”, C=“” (end) → output 65.  
   Output codes: [65,66,256,257,65].  
   Practice  
   Q8.5: Encode “ABCABCA” with initial dict {A,B,C}.  
   (Solution: follow same steps, adding “AB”→3, “BC”→4, ….)

### 8.6 Run‐Length Coding (RLE)

**Definition**

- Replace runs of the same symbol by (value, run‐length).

**Worked Example**

- Binary row: 0001111000 → (0,3),(1,4),(0,3).

**Practice**

- Q8.6: RLE “AAAABBBCCDAA”.
- **Solution**
  - (A,4),(B,3),(C,2),(D,1),(A,2).

### 8.7 Arithmetic Coding

**Definition**

- Encodes an entire message as a single number in [0,1) by successively subdividing intervals proportional to symbol probabilities.

**Worked Example**

- Alphabet {a,b,c} with \(p(a)=0.5,p(b)=0.3,p(c)=0.2\). Intervals:  
  a:[0,0.5), b:[0.5,0.8), c:[0.8,1).  
  Message “bac”:

1. Start [0,1). Symbol b → sub‐interval [0.5,0.8).
2. Within [0.5,0.8), symbol a → new width=0.3; a-interval=[0.5,0.5+0.5·0.3)=[0.5,0.65).
3. Within [0.5,0.65), symbol c → width=0.15; c-interval=[0.5+0.8·0.15,1·0.15)=  
   [0.5+0.12,0.5+0.15)=[0.62,0.65).  
   Any number in [0.62,0.65) represents “bac”.

**Practice**

- Q8.7: Encode “acb” with same probs.
- **Solution**
  - a→ [0,0.5)
  - c→ sub‐interval of [0,0.5): length=0.5, c-range=[0+0.8·0.5,1·0.5)=[0.4,0.5).
  - b→ within [0.4,0.5): length=0.1, b-range=[0.4,0.4+0.3·0.1)=[0.4,0.43).

### 8.8 Golomb Coding

**Definition**

- For non‐negative integer \(n\) and parameter \(m\):
  – \(q=\lfloor n/m\rfloor\); code \(q\) in unary (i.e., \(q\) ones then 0).
  – \(r=n\mod m\). Let \(k=\lceil\log_2m\rceil\), \(c=2^k-m\).
  – If \(r<c\), encode \(r\) in \(k-1\) bits; else encode \(r+c\) in \(k\) bits.

**Worked Example**

- \(m=4,n=9\).
- \(q=\lfloor9/4\rfloor=2\)→ unary=“110”.
- \(r=9\bmod4=1\). \(k=2\), \(c=2^2-4=0\).
- \(r<c\)? \(1<0\)? no → encode \(r+c=1\) in \(k=2\) bits=“01”.
- Golomb code=“11001”.

**Practice**

- Q8.8: Compute \(G_3(10)\).
- **Solution**
  - \(q=\lfloor10/3\rfloor=3\)→ unary=“1110”; \(r=10\bmod3=1\); \(k=2\), \(c=2^2-3=1\).
  - Since \(r<c\) (1<1? no), encode \(r+c=2\) in \(k=2\) bits=“10”.  
    Code=“1110 10”.

### 8.9 Root Mean Square Error (RMSE)

**Definition**

- Measures fidelity between original \(f(x,y)\) and reconstructed \(g(x,y)\):
  $\mathrm{RMSE}
  = \sqrt{\frac{1}{MN}\sum_{x=0}^{M-1}\sum_{y=0}^{N-1}[g(x,y)-f(x,y)]^2}.$

**Worked Example**

- 2×2 images  
   \(f=\begin{pmatrix}10&20\\30&40\end{pmatrix},\;
  g=\begin{pmatrix}12&18\\33&37\end{pmatrix}.\)  
   Errors: \(\{2,-2,3,-3\}\). Squared errors \(\{4,4,9,9\}\), sum=26.  
   \(\mathrm{RMSE}=\sqrt{26/4}=\sqrt{6.5}\approx2.55.\)  
   Practice  
   Q8.9: For \(f=\{0,50,100\}\), \(g=\{5,45,95\}\), compute RMSE.  
   Solution  
   Errors \{5,-5,-5\}, squares \{25,25,25\}, mean=25, RMSE=5.
