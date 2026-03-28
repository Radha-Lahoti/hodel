We thank the reviewer for the positive assessment and for highlighting two important technical points. Below we address the key concerns.

------------------------------------------------------------------------

### 1. Training PINNs with second order optimizer

We agree that optimizer choice is important in ill-conditioned physics problems, and that second-order or natural-gradient methods can significantly improve PINN training.

Following the reviewer’s suggestion, we experimented with L-BFGS for the PINN baseline on the 3D ribbon benchmark. We observe that:

- L-BFGS reduces the number of required training iterations from ~$10^4$ (Adam) to ~$10^3$, indicating improved optimization efficiency,
- however, the final converged loss remains comparable or slightly worse, and training remains sensitive to initialization.

In contrast, HoDEL converges within ~$10^2$ iterations while achieving lower final error, demonstrating both faster and more reliable convergence.

Overall, while second-order optimization improves PINN training speed, it does not eliminate the performance gap with HoDEL. This is consistent with a key distinction in problem formulation:

- PINNs optimize a global residual objective, whose ill-conditioning persists regardless of optimizer choice,
- HoDEL instead solves a sequence of well-conditioned equilibrium problems via continuation and Newton updates.

Thus, HoDEL improves conditioning at the solver level, whereas second-order methods act only at the optimizer level.

We will include a brief discussion of these observations to clarify that our conclusions are not driven by optimizer choice.

------------------------------------------------------------------------

### 2. ICNN expressivity and convexity assumptions
We thank the reviewer for raising this important point.

The ICNN enforces convexity of the local strain energy density $\Phi_\theta(\epsilon)$, which corresponds to enforcing positive semi-definite material stiffness (Drucker stability). This is a material-level constraint, not a constraint on the global energy landscape. The global potential energy remains non-convex due to the nonlinear mapping from configuration to strain, allowing geometric instabilities such as buckling and bifurcations. Thus, ICNN constraint does not prevent non-convex system behavior, but rather excludes unphysical material instabilities.

The ICNN does restrict the model class to convex strain energy densities, which excludes materials exhibiting non-convex constitutive behavior (e.g., softening or phase transitions). However, for the class of purely elastic materials considered in this work (rods, ribbons), convexity is physically well-motivated and improves stability. We will clarify this scope and discuss possible extensions in the revision.

## Summary of revisions
We will: 
- Add a brief discussion on the effect of second-order optimizers (L-BFGS) on PINN training, including convergence speed and final performance.
- Clarify the role of ICNN convexity as a material-level constraint, and discuss its implications for expressivity and applicability.

------------------------------------------------------------------------

We thank the reviewer again for the helpful feedback.
