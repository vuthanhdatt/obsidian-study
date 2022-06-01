---
created: ["2022-05-30 23:31"]
tags: #classification, #linear-model
source: 
---
#classification, #linear-model
Logistic regression, despite its name, is a linear model for [[Classification]]. 
In this model, the probabilities describing the possible outcomes of a single trial are modeled using a [[#Logistic function]]

### Logistic function
$$\Huge f(x)=\frac{L}{1+e^{-k(x-x_{0})}}$$


![[logistic function 1.png]]

![[logistic function 2.png]]


- Linear model like logistic regression or [[Support Vector Machines - Classification#Linear SVC]] have the **same** **predict** function where :
$$\Huge\mathrm{raw\,\,model\,\,output}=\mathrm{coefficients}\cdot\mathrm{features}+\mathrm{intercept}$$
and **different fit** function.

## How Logistic Regression make predict

![[Pasted image 20220531161455.png]]
![[Pasted image 20220531161528.png]]

- Change coef and intercept will change line seperate class
![[Pasted image 20220531162211.png]]

![[Pasted image 20220531162138.png]]


## Loss function
[Loss Function (Part II): Logistic Regression | by Shuyu Luo | Towards Data Science](https://towardsdatascience.com/optimization-loss-function-under-the-hood-part-ii-d20a239cde11)
[Bài 2: Logistic regression | Deep Learning cơ bản (nttuan8.com)](https://nttuan8.com/bai-2-logistic-regression/)


```python
def log_loss(raw_model_output):
   return np.log(1+np.exp(-raw_model_output))
```

![[Pasted image 20220531164301.png]]

**Minimize logistic loss function**

![[Pasted image 20220531164638.png]]
## Regularization
Regularization help model less being overfiting
![[Pasted image 20220531225843.png]]
### L1 regulariztion(Lasso)

L1 giúp cho hầu hết các hệ số bằng 0.  

Thường thì người ta dùng L2 vì đạo hàm đẹp. L1 không có đạo hàm tại 0 và ít được sử dụng hơn. Khi thực hành, có thể chọn L2 trước xem kq thế nào, nếu tệ thì thử sang L1
### L2 regularization(Ridge)
L2 giúp cho các hệ số nhỏ, không được quá lớn


![[L1 and L2.png]]

### Multi - class logistic regression
![[Pasted image 20220531232300.png]]
#### One vs rest

#### softmax



- processing: dummy, scale, resample
- model selection SVM, Random Forest, 
- improve model






