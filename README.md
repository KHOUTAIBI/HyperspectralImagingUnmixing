# Hyperspectral Spectral Decomposition via Plug-and-Play Regularization

## 1. Motivation

Hyperspectral images (HSI) differ fundamentally from RGB images.
Instead of 3 broad spectral bands, HSIs contain **tens to hundreds of narrow spectral bands**, often extending beyond the visible spectrum (e.g., near-infrared).

Because these bands are narrow, each detector receives less light per band, which increases **noise**. To compensate, sensors often increase pixel size, improving signal-to-noise ratio but reducing **spatial resolution**.

The ultimate goal of hyperspectral imaging is **material identification**.
Each material has a characteristic spectral signature (e.g., hydrogen emission lines occur at specific frequencies). However, due to limited spatial resolution, a single pixel often contains **multiple materials**.

Thus, each hyperspectral pixel is typically modeled as a **linear mixture**:

$$ X = A S + N $$

* $ X \in \mathbb{R}^{L \times P} $: observed HSI (L bands, P pixels)
* $ A \in \mathbb{R}^{L \times K} $: endmembers (material spectra)
* $ S \in \mathbb{R}^{K \times P} $: abundances
* $ N $: noise

Each column of ( S ) satisfies:

* Non-negativity
* Sum-to-one constraint

---

## 2. Problem Difficulty

Spectral unmixing is:

* **Ill-posed**
* Non-convex
* Not uniquely identifiable (e.g., permutation ambiguity)

Without regularization, infinitely many solutions exist.

Classical regularization includes:

* Sparsity on abundances (L1)
* Smoothness (L2)
* Non-negativity constraints

However, these priors are often too simplistic.

---

## 3. Deep Learning Approaches

Deep learning methods (autoencoders, CNNs, transformers) have been applied to hyperspectral unmixing.

Typical approaches:

* Encoder → abundance map
* Decoder → reconstruct spectrum
* Softmax layer to enforce sum-to-one

However:

* These methods separate optimization and learning
* They lack interpretability
* They may still suffer from non-uniqueness
* Training data is scarce

---

## 4. Proposed Approach: Plug-and-Play (PnP)

Instead of fully learned end-to-end networks, we propose to combine:

* Explicit optimization for data fidelity
* Learned denoisers as regularizers

This fits within the **Plug-and-Play (PnP)** framework:

[
\min_{A,S} \frac{1}{2}|X - AS|_2^2 + \lambda R(A,S)
]

where ( R ) is implicitly defined via a learned denoiser ( D_\sigma ).

However, standard PnP methods do not always guarantee convergence.
Therefore, we will:

* Use a single carefully designed denoiser
* Study convergence behavior
* Possibly reinterpret the denoiser as a proximal operator

Goal: combine interpretability + strong priors.

---

## 5. Major Challenge: No Hyperspectral Denoisers

There are very few hyperspectral-trained denoisers available.

Using RGB denoisers performs poorly because:

* RGB models assume 3 channels
* HSI has high spectral correlation structure

Therefore, we must train a **hyperspectral denoiser from scratch**.

---

## 6. How to Generate Synthetic Data?

Since large HSI datasets are scarce, we propose synthetic generation.

Possible strategies:

### 1. Statistical modeling of abundances

* Sample abundances from Dirichlet distributions
* Enforce physical constraints

### 2. Spatial structure simulation

* “Dead leaves” model (random shapes, rectangles, blobs)
* Patch concatenation
* Structured region sampling

### 3. Spectral library usage

* Use known spectral libraries (e.g., USGS library)
* Combine real spectra with synthetic abundances

### 4. Gaussian / Dirichlet modeling

* Model spectral variability
* Introduce controlled noise

Synthetic data must:

* Respect physical mixing constraints
* Contain realistic noise
* Capture spatial correlations

---

## 7. Project Roadmap

### Phase 1 — Baseline

* Download real hyperspectral datasets (urban, vegetation, etc.)
* Visualize spectra
* Fix ( S ) (known endmembers)
* Implement simple PnP with fixed spectra
* Evaluate reconstruction quality

Goal: understand behavior of PnP in this setting.

---

### Phase 2 — Semi-blind setting

* Fix spectral matrix ( S )
* Optimize abundances ( A )
* Train/test denoiser on abundance maps
* Verify stability and convergence

---

### Phase 3 — Alternating optimization

* Alternating updates for ( A ) and ( S )
* Integrate learned denoiser inside loop
* Study convergence properties

---

## 8. Expected Contributions

* Hyperspectral denoiser trained on synthetic data
* PnP-based unmixing algorithm
* Analysis of convergence behavior
* Comparison with classical and deep methods

---
