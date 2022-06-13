# Scaling

 - distance-based methods such as K-Nearest Neighbors, Principal Component Analysis or Support-Vector Machines will artificially attribute a great importance to a given feature if its range is extremely broad
 - On the contrary, tree-based methods such as Random Forest or XGBoost do not require the input features to be scaled.
 -
## _standardization_, a.k.a Z-score normalization

$$\Huge {\bar{X}}:={\frac{X-X}{{\tilde{\sigma}}}}$$
## _normalization_, a.k.a Min-Max scaling.

$$\Huge\tilde{X}:=\frac{X-m i n(X)}{m a x(X)-m i n(X)}$$
1.  Train-test split the data
2. Scale the train sample
3. Scale the test sample **with the training parameters**
## Dummy variables

Imputer
Pipeline
Resampling is done after the data is split into training, test and validation sets. Resampling is done only on the training set or the performance measures could get skewed. Resampling can be of two types: Over-sampling and Under-sampling.