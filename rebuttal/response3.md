We thank the reviewer for the thoughtful and constructive feedback. Below we address the key concerns.

------------------------------------------------------------------------
### 1. $O(N)$ Linear solve and scaling to 2D/3D

We agree that our experiments focus on 1D and quasi-1D systems (rods and ribbons), where the Hessian is banded and admits $O(N)$ linear solves. However, this efficiency does not solely rely on bandedness. The key property is the locality of the energy decomposition
$$
E = \sum_i \Phi_\theta(\epsilon_i),
$$
which ensures each degree of freedom interacts only with a fixed-size neighborhood, yielding a Hessian with $O(N)$ nonzeros.

In 1D this is banded. For 2D shells and 3D meshes, the Hessian remains sparse but not banded, and standard sparse solvers apply. Sparse direct methods exhibit sub-quadratic scaling (e.g., $O(N^{1.5})$ in 2D), while iterative methods achieve near-linear scaling in practice. This is the same sparsity exploited in classical FEM, which HoDEL inherits.

Importantly, the same sparse system appears in both forward (Newton) and backward (IFT) passes, enabling factorization reuse.

We agree that including a 2D shell example would strengthen the paper; however, our focus is on discrete linear objects (DLOs), where conditioning challenges are already pronounced and continuation benefits are clearly isolated.

------------------------------------------------------------------------
### 2. Training data regime (single trajectory)

Our goal is to study regimes with limited equilibrium observations (even a single trajectory), common in physical experiments. Continuation exploits the quasi-static path structure and enforces equilibrium along the trajectory, whereas PINNs and NODEs rely more on data diversity. Thus, the performance gap reflects a difference in formulation: HoDEL leverages structured equilibrium paths, while baselines treat observations more independently.

------------------------------------------------------------------------
### 3. Time-budget evaluation vs. gradient steps

We report wall-clock time to provide a hardware-realistic comparison. PINNs/DEQs benefit from GPU-parallel batching, whereas HoDEL requires sequential λ-continuation. Comparing by steps or FLOPs would therefore favor HoDEL and not reflect runtime cost. Empirically, HoDEL converges in fewer gradient steps due to better conditioning and larger stable learning rates (e.g., ~1k vs ~5k for DEQ in 1D).

------------------------------------------------------------------------
### 4. Comparison to differentiable simulation methods

Differentiable simulators (e.g., DiffPD, DiffTaichi) typically target dynamic trajectories or supervised forces and rely on backpropagation through time. HoDEL instead targets equilibrium-only supervision, combining quasi-static FEA (via continuation) with implicit differentiation (IFT). The key distinction is this restriction, which enables stable solves and exact gradients in stiff regimes.

------------------------------------------------------------------------
### 5. ICNN vs MLP expressiveness

In our setting, strain-MLP and strain-ICNN achieve similar accuracy when stable, as the learned energy is a small correction to a DER prior. ICNN is used for structural correctness: it enforces convex local energy, ensuring positive semi-definite stiffness and preventing non-physical instabilities, especially in nonlinear or extrapolation regimes.

------------------------------------------------------------------------
### 6. Static formulation and bifurcations

We agree that static formulations cannot resolve branch selection near bifurcations, which depends on dynamics. Our method targets quasi-static regimes where equilibrium trajectories are well-defined and continuation enables stable tracking. Extending HoDEL to quasi-static or dynamic formulations that incorporate inertia is an important direction for future work as mentioned in the manuscript.

------------------------------------------------------------------------
### 7. Noise model

We agree that additive Gaussian noise is a simplified model of experimental uncertainty. It provides a controlled setting to evaluate robustness across methods. In this setting, HoDEL shows improved stability due to its solution-dependent formulation and equilibrium constraints.

------------------------------------------------------------------------
### 8. Implementation details and reproducibility

We will expand the main text to include implementation details currently in the appendix, including DER parameterization, ribbon strain invariants, and ICNN architecture.

------------------------------------------------------------------------
We thank the reviewer again for the helpful feedback.