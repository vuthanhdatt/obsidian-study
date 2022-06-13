#### Binary search

```python
def check(target, guess):
    if target == guess:
        return 0
    elif guess > target:
        return 1
    else:
        return -1
        
def guess_num(end, target):
    low = 0
    high = end-1
    while low <= high:
        mid = (low+high)//2
        if check(target, mid) == 0:
            return mid
        elif check(target ,mid) == -1:
            low = mid +1
        elif check(target ,mid) == 1:
            high = mid -1

print(guess_num(90392039109120930,10))
        
```