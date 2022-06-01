---
created: ["2022-05-30 23:31"]
tags: 
source: 
---
#classification #linear-model

# SVC

# Linear SVC

## Loss function
- hinge losses

```python
def hinge_loss(raw_model_output):

   return np.maximum(0,1-raw_model_output)
```

![[Pasted image 20220531164301.png]]




- Hinge loss don't penalty correct prediction(greater than 1) so which are not support vector will not effect model
- small gamma help model more smooth