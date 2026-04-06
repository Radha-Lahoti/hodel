We thank the reviewer for the careful follow-up and for emphasizing the need for clearer quantitative support. We address each point below and incorporate additional evidence where requested.

**(1) Generalization to 2D/3D systems.**  
We agree that sparsity alone does not guarantee scalability of the full HoDEL pipeline. Our key point is that the only component whose computational structure changes between 1D and higher dimensions is the linear solve, while all other components remain unchanged:

- Energy evaluation uses local stencil-based strain computations. While strain definitions differ across 1D/2D/3D (e.g., rod vs shell stencils), computation remains batched over local neighborhoods, so cost scales linearly with the number of stencils.  
- Continuation and Newton updates are algorithmically identical.  
- The same linear system is reused in both forward (Newton) and backward (IFT) passes.

Thus:
- **1D:** banded linear systems  
- **2D/3D:** sparse (non-banded) systems  

which can be handled using standard sparse solvers as in FEM. We acknowledge that empirical validation in 2D/3D is not included, as the paper focuses on DLOs where stiffness and continuation challenges are already pronounced. We will clarify this scope and avoid overgeneralization in the revision.

**(2) Data-regime ablation.**  
We agree this is an important point. We conducted an additional ablation on the 1D system by varying the number of training trajectories ($\eta=0.03$) and compared their final median relative error ($10^{-2}$) (# of trajectories = 1 is from the original experiment. # of trajectories= 3, 5 are averaged over 10 seeds instead due to rebuttal time constraints):

| # trajectories | HoDEL | DEQ | PINN |
|---------------|------|-----|------|
| 1             | $6.18$   | $8.90$   | $18.0$   |
| 3             | $5.13$    | $6.36$   | $13.2$    |
| 5             | $4.73$    | $5.21$   | $10.5$    |

We observe that HoDEL has a clear advantage in the low-data regime (single trajectory), while the gap narrows as more data is provided. This supports our claim that HoDEL benefits from exploiting structured equilibrium paths, rather than purely model capacity. We will include this ablation in the appendix.

**(3) Gradient-step efficiency.**  
We appreciate the request for explicit evidence. Using Fig. 7 and Table 6 (Appendix), we computed the median number of gradient steps required to reach fixed validation loss thresholds ($\eta=0.03$):

| Method | L₂=0.3 | L₂=0.2 | L₂=0.1 |
|--------|--------|--------|--------|
| HoDEL  | 60     | 150    | 1320   |
| DEQ    | 225    | 900    | 4300   |

This shows that HoDEL reaches comparable accuracy with significantly fewer optimization steps, consistent with improved conditioning.

**(4) Relation to continuation methods.**  
We agree that continuation is standard in numerical mechanics. Our contribution is not continuation itself, but its integration with: (i) neural energy learning, (ii) implicit differentiation via IFT, and (iii) equilibrium-only supervision in stiff regimes. To our knowledge, existing continuation methods are not designed for learning constitutive models via gradient-based optimization under equilibrium-only observations. We will clarify this distinction more explicitly.

**(5) ICNN vs MLP.**  
We agree that quantitative comparison is necessary. Below is the MEA for ICNN vs MLP architectures on the ribbon example where ICNN is from the original experiment (MLP is averaged over 10 seeds due to rebuttal time constraints):

| Architecture | MAE Loss  |
|--------------|-----------|
| ICNN         | 0.176     |
| MLP          | 0.183     |

We evaluated a highly nonlinear 1D system (slinky), where the learned energy dominates the prior. The resulting force–displacement curves (MLP vs ICNN) compared to ground truth are shown here:  
[ICNN vs MLP for slinky 1D](https://figshare.com/s/46aafad3f50d6bc0d67c)

In this regime, ICNN significantly outperforms MLP, as convexity enforces positive semi-definite stiffness and improves stability, particularly under extrapolation. This complements our earlier observation that differences are smaller when learning residual corrections.

**Summary.**  
In the revision, we will add: (i) data-regime ablations, (iii) ICNN vs MLP quantitative loss comparison, and (iv) clearer scoping of 2D/3D claims. We thank the reviewer for helping strengthen the paper.
