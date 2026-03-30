We thank the reviewer for the positive assessment and for highlighting key points on (i) baselines, (ii) architecture, and (iii) scaling. We address each below.

------------------------------------------------------------------------
### 1. Baseline selection (W1)
The choice of baselines is driven primarily by compatibility with the problem setting, rather than recency. HoDEL operates in a regime with:
- equilibrium-only supervision,
- implicit nonlinear solves,
- strongly stiff energy landscapes.

Among existing approaches, PINNs, DEQs, and NODE-style models are the closest formulations that can be adapted to this setting:
- PINNs optimize equilibrium residuals,
- DEQs solve implicit fixed-point problems,
- NODEs provide a continuous-depth baseline via learned dynamics.

To apply NODE, we define a static equilibrium evolution operator (requiring a linear solve per step), showing pseudo-dynamics remain stiff and ineffective here.

While these methods were originally introduced some time ago, they remain the standard formulations for implicit or physics-constrained learning and are still widely used as baselines in recent work. Newer differentiable physics methods typically rely on trajectory supervision or rollouts and are not directly comparable. Additionally, while many recent works propose architectural improvements, our goal is to evaluate solver performance without confounding architectural choices. Since our focus is on solvers, such advances are complementary and can be readily incorporated into our framework.

Our goal is to compare against the closest compatible formulations, rather than the most recent ones, and to highlight the distinction between:
- global residual minimization or unrolled dynamics (PINN/DEQ/NODE), and
- continuation-based equilibrium solving with IFT gradients (HoDEL).

We will revise the related work section to clarify this rationale, and state that the comparison focuses on methods that operate under equilibrium-only supervision.

------------------------------------------------------------------------
### 2. Role and necessity of architecture components (W2, Q1)
We agree that the architectural choices should be better justified.

The components fall into essential ingredients and structural biases:

**Essential components** (removing these breaks the method):
- **Continuation (λ-stepping):** avoids divergence in stiff regimes; without it, Newton iterations frequently fail to converge.
- **Energy-based formulation:** ensures a conservative force field and avoids energy drift.
- **Strain-based representation:** enforces locality and invariance; removing it degrades generalization (Table 5).

**Structural components** (beneficial but not strictly required):
- **Stencil decomposition:** induces sparse, local interactions, enabling scalable linear solves.
- **ICNN parameterization:** enforces convexity of local energy densities, ensuring PSD stiffness and improved stability.

We will clarify the design criteria (stability, locality, scalability) and distinguish essential vs. optional components in the revision.

------------------------------------------------------------------------
### 3. Computational scaling beyond 1D (W3, Q2)
We thank the reviewer for this important question on scalability to higher-dimensional meshes. We agree that strict $O(N)$ complexity arises from banded structure, which is specific to 1D connectivity. However, this efficiency does not fundamentally rely on bandedness. The key property is the locality of the energy
$$
E = \sum_i \Phi_\theta(\epsilon_i),
$$
so each DOF interacts only with a fixed-size neighborhood, yielding $O(N)$ nonzeros in the Hessian.

In 1D, this is banded, yielding $O(N)$ solves. In 2D/3D, the Hessian remains sparse but not banded, and standard sparse solvers apply. Sparse direct methods exhibit sub-quadratic complexity (e.g., $O(N^{1.5})$ in 2D), while iterative methods with suitable preconditioning achieve near-linear scaling in practice, mirroring classical FEM.

Importantly, the same sparse system appears in both the forward (Newton) and backward (IFT) passes, allowing factorization reuse.

We will revise the manuscript to clarify that the $O(N)$ claim applies to 1D connectivity, and discuss expected scaling in higher dimensions where the Hessian is sparse but not banded.

------------------------------------------------------------------------
## Summary of revisions
We will revise the manuscript to:
- clarify the applicability of baselines under equilibrium-only supervision,
- clarify design rationale and distinguish essential vs. optional components,
- refine the computational complexity discussion (1D banded vs. general sparse).

------------------------------------------------------------------------
We thank the reviewer again for the helpful feedback.
