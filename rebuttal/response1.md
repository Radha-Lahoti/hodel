We thank the reviewer for the thoughtful and constructive feedback. We
are encouraged that the empirical evaluation and ablations were found
sound. Below we address the key concerns.

------------------------------------------------------------------------

### 1. Clarification of $\lambda$ and "homotopy"

We agree that the role of $\lambda$ should be stated more explicitly. In our formulation, $\lambda \in [0,1]$ parameterizes a continuous family of equilibrium problems:

$$F(x, \lambda) = \nabla_x E(x;\theta) - f_\text{ext}(\lambda) = 0 $$
where

$$f_\text{ext}(\lambda) = \lambda f_\text{target}, \quad
x_b(\lambda) = x_{b,0} + \lambda (x_{b,\text{target}} - x_{b,0})$$

Thus, $\lambda$ induces a continuation path over loads and/or boundary conditions, and we solve for $x^*(\lambda)$ sequentially using Newton with warm-starts.

We agree with the reviewer that this corresponds to standard load-stepping continuation in FEA. In the revision, we will explicitly define $\lambda$ in the method section and clarify that "homotopy" is used in an algorithmic sense, not as a novel mathematical formulation.

------------------------------------------------------------------------

### 2. Convergence claims

Our method relies on classical assumptions (local invertibility of the Hessian and initialization within the basin of attraction), and does not introduce new global convergence guarantees beyond Newton + IFT.

Importantly, our claim is empirical rather than theoretical:
- continuation reduces divergence in practice (e.g., Fig. 4 shows near-100% completion beyond a threshold number of steps)
- baselines frequently fail or diverge under the same conditions (Tables 1, 3)

We will revise statements such as "guarantees convergence" to “empirically improves robustness and convergence in stiff regimes.”

------------------------------------------------------------------------

### 3. Originality and positioning

We agree that individual components (load stepping, Newton, ICNNs, banded solvers) are known.

Our contribution lies in their integration into a differentiable learning framework, specifically:

- learning neural energy functionals from equilibrium-only supervision
- combining continuation-based forward solves with IFT gradients
- designing structure-aware parameterizations (strain-based, banded Hessians)

More broadly, we bridge classical continuation-based solvers with differentiable learning, demonstrating their importance for stable training in stiff regimes.

------------------------------------------------------------------------

### 4. ICNN vs MLP

Empirically, both strain-MLP and strain-ICNN achieve similar performance in the ribbon setting, which is expected since we use a strong DER prior and the learned energy acts as a small correction.

Our motivation for ICNN is therefore not empirical performance in this regime, but structural correctness of the learned energy. ICNN enforces convexity of the local energy density, ensuring positive semi-definite tangent stiffness (Drucker stability) and well-posed equilibrium solves. In contrast, an unconstrained MLP may learn non-convex energies, leading to non-physical equilibria when its contribution dominates.

We will clarify this positioning in the revision and present ICNN as a physics-informed inductive bias rather than a component that necessarily improves accuracy in this setting.

------------------------------------------------------------------------

### 5. DER clarification

We thank the reviewer for pointing this out. We will correct the description of DER as a discrete numerical method, not “analytical.” Our intended distinction was that DER uses explicit constitutive laws with known parameters, rather than learned models.

------------------------------------------------------------------------

### 6. Synthetic data

We agree this is an important limitation.

All experiments use simulated or FEA-generated data. We will:
- explicitly state this in the limitations section,
- clarify that validation on real-world data is an important direction for future work.

------------------------------------------------------------------------

### 7. On "SOTA" baselines

Our intent was to compare against representative classes of differentiable implicit/physics-informed methods under matched architectures and budgets, not to claim an exhaustive state-of-the-art benchmark. We will revise “SOTA baselines” to “representative baselines.”

------------------------------------------------------------------------
## Summary of revisions
We will: 
1. Precisely define $\lambda$ and clarify "homotopy" as continuation
2. Remove overstated convergence claims
3. Clarify ICNN as a physics-informed inductive bias rather than a performance-driven component
4. Correct DER description
5. Explicitly state synthetic-data limitation and future real-data validation
------------------------------------------------------------------------

We thank the reviewer again for the helpful feedback.
