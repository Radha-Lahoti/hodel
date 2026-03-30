We thank the reviewer for the positive assessment and for highlighting important technical points regarding (i) baseline scope, (ii) architectural design, and (iii) computational scaling. We address each below.

------------------------------------------------------------------------

### 1. Baseline selection (W1)

We thank the reviewer for raising this point.

The choice of baselines is driven primarily by compatibility with the problem setting, rather than recency. HoDEL operates in a regime with:

- equilibrium-only supervision,
- implicit nonlinear solves,
- strongly stiff energy landscapes.

Among existing approaches, PINNs, DEQs, and NODE-style models are the closest formulations that can be adapted to this setting:

- PINNs optimize equilibrium residuals directly,
- DEQs formulate implicit fixed-point problems,
- NODEs provide a continuous-depth baseline via learned dynamics.

To apply NODE to our static setting, we formulated a static equilibria evolution operator (requiring a linear solve per evaluation). We included it to demonstrate that tracking pseudo-dynamics remains prohibitively stiff and infeasible for this problem.

While these methods were originally introduced some time ago, they remain the standard formulations for implicit or physics-constrained learning, and are still widely used as baselines in recent work.

More recent methods in differentiable physics and simulator learning typically rely on trajectory supervision or forward rollouts, and therefore are not directly comparable in our setting. As a result, the set of applicable baselines is inherently more limited.

Our goal is to compare against the closest compatible formulations, rather than the most recent architectures, and to highlight the distinction between:

- global residual minimization or unrolled dynamics (PINN/DEQ/NODE), and
- continuation-based equilibrium solving with IFT gradients (HoDEL).

We will revise the related work section to clarify the rationale behind our baseline selection (as outlined above), and explicitly state that the comparison focuses on methods that operate under equilibrium-only supervision.

------------------------------------------------------------------------
### 2. Role and necessity of architecture components (W2, Q1)

We agree that the architectural choices should be better justified.

The components serve distinct roles, and can be grouped into essential algorithmic ingredients and structural inductive biases:

**Essential components** (removing these breaks the method):

- **Continuation (λ-stepping):** necessary to avoid divergence in stiff regimes; without it, Newton iterations frequently fail to converge.
- **Energy-based formulation:** ensures conservative force field by design and avoids energy drift.
- **Strain-based representation:** enforces locality and invariance; removing it leads to significantly worse generalization (Table 5).

**Structural components** (not strictly required, but beneficial):
- **Stencil decomposition:** induces sparse, local interactions, enabling scalable linear solves.
- **ICNN parameterization:** enforces convexity of local energy densities, improving stability and preventing pathological behaviors (e.g., negative stiffness).

We will revise the manuscript to clarify the design criteria (stability, locality, scalability) and explicitly distinguish essential vs optional components.

------------------------------------------------------------------------

### 3. Computational scaling beyond 1D (W3, Q2)

We thank the reviewer for this important question on scalability to higher-dimensional meshes. We agree that the strict $O(N)$ complexity arises from banded structure, which is specific to 1D connectivity (rods and ribbon centerlines). However, this efficiency does not fundamentally rely on bandedness. The key property is the locality of the energy decomposition
$$
E = \sum_i \Phi_\theta(\epsilon_i),
$$
which ensures each degree of freedom interacts only with a fixed-size neighborhood. As a result, the Hessian has $O(N)$ nonzeros regardless of dimensionality.

In 1D, this structure is banded, yielding $O(N)$ solves. For 2D shells and 3D volumetric meshes, the Hessian remains sparse but not banded, and standard sparse solvers apply. In these settings, sparse direct methods exhibit sub-quadratic complexity (e.g., $O(N^{1.5})$ in 2D), while iterative methods with suitable preconditioning achieve near-linear scaling in practice. This is the same sparsity structure exploited in classical FEM, and HoDEL inherits these scalability properties while enabling differentiable learning.

Importantly, the same sparse system appears in both the forward (Newton) and backward (IFT) passes, allowing factorization reuse, which significantly reduces training cost.

We will revise the manuscript to clarify that the $O(N)$ claim applies to 1D connectivity, and clarify expected scaling in higher dimensions where the Hessian is sparse but not banded.


## Summary of revisions
We will revise the manuscript to:
- clarify the applicability of baselines under equilibrium-only supervision,
- provide a clearer design rationale and essential vs. optional classification of architectural components,
- refine the computational complexity discussion, distinguishing 1D banded vs. general sparse settings.

------------------------------------------------------------------------

We thank the reviewer again for the helpful feedback.
