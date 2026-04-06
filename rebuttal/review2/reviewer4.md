We thank the reviewer for the clarification and agree that the role of individual components should be supported more explicitly. Below we isolate each component and summarize both existing and additional evidence.

---

### (a) λ-continuation (essential)
This has already been ablated in Sec. 5.3.1 (Fig. 4). Without continuation (large Δλ), Newton iterations fail to converge, whereas sufficiently small steps yield 100% convergence. This demonstrates that continuation is not a heuristic but a necessary mechanism for solving stiff, nonlinear equilibrium problems.

---

### (b) Strain-based representation (essential)
This is quantitatively demonstrated in Table 5. Using raw coordinates leads to 0% convergence, while strain-based inputs achieve 100% convergence (for both ICNN and MLP). This isolates representation as critical: strain enforces locality and invariance, whereas coordinate-based models violate these properties and fail to learn meaningful constitutive behavior.

---

### (c) ICNN vs MLP (structural component)
We agree that quantitative comparison is necessary. Below are train and test losses for ICNN vs MLP architectures on the ribbon example:

| Architecture | Train Loss | Test Loss |
|--------------|------------|-----------|
| ICNN         | ...        | ...       |
| MLP          | ...        | ...       |

Both architectures achieve similar performance in this setting, consistent with Table 5, where convergence rates are identical.

In addition, we evaluated a highly nonlinear 1D system (slinky), where the learned energy dominates the prior. The resulting force–displacement curves are shown here:  
[ICNN vs MLP for slinky 1D](https://figshare.com/s/46aafad3f50d6bc0d67c)

In this regime, ICNN significantly outperforms MLP. Convexity enforces positive semi-definite stiffness, improving stability and extrapolation. This complements our observation that differences are smaller when the network learns only a residual correction to a strong prior.

We will revise the manuscript to clarify that ICNN is not strictly required for accuracy, but provides a stability bias that becomes important in strongly nonlinear regimes.

---

### (d) Stencil decomposition (local energy structure)
We agree this component warrants clarification. Rather than introducing an additional ablation, we emphasize that stencil-based decomposition follows directly from the constitutive modeling assumption: the total energy is expressed as a sum of local strain energies.

Removing this structure corresponds to learning a global energy over all DOFs, which:
- breaks locality inherent to constitutive laws,  
- leads to severe overfitting in the equilibrium-only supervision setting, and  
- destroys sparsity of the Hessian, making both forward (Newton) and backward (IFT) solves significantly harder.

Thus, stencil decomposition is not an architectural heuristic but a modeling assumption aligned with classical continuum mechanics. We will clarify this distinction in the revision.

---

### (e) Energy vs force parameterization
We further clarify why we learn energy rather than force directly. A force-based model:
- does not guarantee a conservative field (violating integrability), and  
- suffers from aliasing in strain space, where different deformation modes can produce similar strain magnitudes but require different force directions.

In contrast, parameterizing energy ensures physically consistent forces via \(F = \nabla E\), which is critical under equilibrium-only supervision.

---

### Summary

| Component | Effect when removed |
|----------|-------------------|
| λ-continuation | Solver divergence |
| Strain representation | 0% convergence |
| Energy formulation | Non-physical / inconsistent forces |
| ICNN | Improves stability (important in nonlinear regimes) |
| Stencil structure | Encodes locality and preserves sparsity |

Overall, we adopt an energy-based formulation to ensure a conservative force field. Continuation and strain-based energy modeling are essential for stable and physically consistent learning, while ICNN and stencil decomposition provide additional stability and scalability aligned with physical modeling principles.