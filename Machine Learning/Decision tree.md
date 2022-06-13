
### Decision tree classification
- tree base model don't require same scale beetween feature
- A classification tree divides the feature space into **rectangular regions**.
![[Pasted image 20220601091854.png]]
### Decision tree regression

### Generalization Error
- generalization error of $\hat{f}$   mean does $\hat{f}$  generalize on unseen data
- can be decompose into 3 terms: **bias**, **variance**, and **irreducible**
$$\Huge \hat{f}\:=\:b i a s^{2}+va ri a n c e+\mathrm{irreducible\;error}$$
- **bias**: error term that tell how much ${\hat{f}}\neq f$
 - high bias lead to underfitting

![[Pasted image 20220601095117.png]]
- high variance lead to overfitting

#### Bias - Variance Tradeoff

![[Pasted image 20220601095401.png]]
![[Pasted image 20220601095510.png]]

#### Handle bias and variance problem

- Using cross validation to check model overfitting or underfitting
K-Fold cross
![[Pasted image 20220601101429.png]]
![[Pasted image 20220601101822.png]]
![[Pasted image 20220601101833.png]]
