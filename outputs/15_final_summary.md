# Final summary

## Key findings

1. Traffic speed is approximately, not exactly, low rank. After removing road means, one singular value carries 65.0% of the energy, yet 102 of 209 components are needed for 95% and the matrix is numerically full rank (209).

2. The rank should be chosen by held-out validation, not by the elbow. Held-out RMSE fell from 8.34 (k = 0) to 4.26 at k = 8 and rose to 6.81 at k = 80, while the elbow rule suggested k = 43.

3. PCA and the SVD are the same computation: all equivalence checks agree to 2e-12. PCA adds monitoring statistics, but the residuals are heavy-tailed (excess kurtosis 7.6), so Gaussian control limits over-alarm (25.5% of slots above the SPE limit against a nominal 1%).

4. Low-rank structure adds real detection power over simple baselines: the weakest low-rank variant exceeds the strongest baseline in average precision at every severity (smallest margin 0.047).

5. The tensor (Tucker) model is better than the matrix model in a consistent but modest way: it was ahead in every injection in 11 of 12 paired comparisons; exception(s): Tucker |res| vs matrix, avg. precision, severe (1/3 injections). At mild severity the road-standardised gain is +0.0080 ROC-AUC and +0.0190 average precision; at severe severity the methods are close.

6. Tucker reaches slightly better reconstruction accuracy than the rank-8 matrix model (relative error 0.4623 vs 0.4691) with 9.1x fewer stored numbers (7,878 vs 71,952); on this machine it was 3.7x slower to fit (14.2 s vs 3.8 s).

7. Absolute performance on mild anomalies is limited: the best average precision is 0.291 (Tucker road-std). Natural anomalies in the data are counted as false alarms, so absolute precision is pessimistic, but relative comparisons are fair.

## Limitations

- Anomalies are synthetic (multiplicative drops and spikes); real incidents may look different, and natural anomalies are unlabelled.
- The rank k and the Tucker ranks were chosen on the clean data; without clean data another selection rule is needed.
- Only 3 injections per severity were run; consistent signs across them are evidence, but no significance test was done.
- 5 roads (> 20% missing) were removed and 0.02% of the remaining cells imputed; day-of-week is unknown, so weekly effects are not modelled explicitly.
- Low-rank models can output negative speeds; regional events used random roads because the data has no coordinates.
- The top-2% flagging rule assumes the contamination rate is known.

## Future work

- Robust PCA (low rank + sparse) so that anomalies do not distort the subspace they are measured against.
- Spatial information (road adjacency) and calendar information (weekday / weekend) as extra structure.
- Other tensor models (CP, Tucker with non-negativity) and tensor completion for the missing roads.
- Evaluation on real labelled incidents and online / streaming updates of the subspace.

## Results ledger

| stage | quantity | value |
|---|---|---|
| 2-4 Data | Raw observations | 1,855,589 rows |
| 2-4 Data | Roads x days x time slots (raw) | 214 x 61 x 144 |
| 2-4 Data | Absent grid cells | 24,187 (1.29%) |
| 2-4 Data | Roads dropped (> 20% missing) | 5: [48, 55, 123, 146, 147] |
| 2-4 Data | Cells imputed on kept roads | 427 (0.0233%) |
| 5 Matrix | Matrix X (roads x day-time slots) | 209 x 8,784 |
| 5 Matrix | Frobenius norm ||X||_F | 54,971.08 |
| 6 Rank | Numerical rank of X | 209 (maximum possible 209) |
| 6 Rank | sigma_1 energy share (raw X / centred Xc) | 97.98% / 64.96% |
| 6 Rank | Components for 90% / 95% / 99% energy (centred) | 55 / 102 / 174 |
| 6 Rank | Stable rank (centred) | 1.54 |
| 7 SVD | Relative reconstruction error U S V^T vs Xc | 2.5e-15 |
| 7 SVD | Max gap between eig(Xc Xc^T) and sigma^2 | 2.4e-14 |
| 7 SVD | Corr(u1 of X, road mean) / Corr(v1 of Xc, network deviation) | 0.9979 / 0.9992 |
| 8 Low rank | Energy retained by rank 8 | 77.99% |
| 8 Low rank | Relative error vs ||Xc||_F at rank 8 | 0.4691 |
| 8 Low rank | Storage compression at rank 8 | 25.4x |
| 9 Error | Rank by elbow rule / best held-out / selected | 43 / 10 / 8 |
| 9 Error | Held-out RMSE: k=0 -> selected k -> largest k tested | 8.34 -> 4.26 -> 6.81 (k=80) |
| 9 Error | In-sample RMSE / MAE at rank 8 | 4.050 / 2.706 |
| 9 Error | Residual excess kurtosis / share beyond 3 robust sigma | 7.61 / 5.96% |
| 9 Error | Median road R^2 (variance explained) | 0.759 |
| 10 Injection | Injected anomalous cells | 36,807 (2.005%) |
| 10 Injection | Events (point / incident / regional) | 7,344 / 1,473 / 156 |
| 10 Injection | Weyl: max |sigma shift| vs ||E||_2 | 114.6 <= 673.6  (True) |
| 11 PCA | Largest gap in all PCA-vs-SVD equivalence checks | 2.2e-12 |
| 11 PCA | Slots above SPE limit / T^2 limit (nominal 1%) | 25.5% / 5.7% |
| 11 PCA | Top-SPE slots whose biggest contributor is an injected cell | 70% |
| 12 Tensor | Tensor shape (roads, days, times) | (209, 61, 144) |
| 12 Tensor | Components for 95% energy per mode (roads, days, times) | 102, 41, 34 |
| 13 Tucker | Selected multilinear ranks | (16, 14, 10) |
| 13 Tucker | Stored numbers: Tucker vs matrix | 7,878 vs 71,952  (9.1x fewer) |
| 13 Tucker | Relative error: matrix vs Tucker (HOOI) | 0.4691 vs 0.4623 |
| 14 Comparison | Best ROC-AUC at mild severity | Tucker road-std (0.9345) |
| 14 Comparison | Best avg. precision at mild severity | Tucker road-std (0.2914) |
| 14 Comparison | Best ROC-AUC at moderate severity | Tucker |res| (0.9845) |
| 14 Comparison | Best avg. precision at moderate severity | Tucker road-std (0.6995) |
| 14 Comparison | Best ROC-AUC at severe severity | Tucker |res| (0.9944) |
| 14 Comparison | Best avg. precision at severe severity | Correlation PCA (0.8676) |
