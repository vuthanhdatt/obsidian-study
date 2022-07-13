---
created: ["2022-02-15 14:52"]
tags: ["Matrices", ""]
---
source: [[Linear Algebra]] 


---

# Matrices


$$
\boldsymbol{A}=\left[\begin{array}{cccc}
a_{11} & a_{12} & \cdots & a_{1 n} \\
a_{21} & a_{22} & \cdots & a_{2 n} \\
\vdots & \vdots & & \vdots \\
a_{m 1} & a_{m 2} & \cdots & a_{m n}
\end{array}\right], \quad a_{i j} \in \mathbb{R}
$$

## Matrix Addition and Multiplication


### Addition


### Multiplication

For matrices $\boldsymbol{A} \in \mathbb{R}^{m \times n}, \boldsymbol{B} \in \mathbb{R}^{n \times k}$, the elements $c_{i j}$ of the product $\boldsymbol{C}=\boldsymbol{A} \boldsymbol{B} \in \mathbb{R}^{m \times k}$ are computed as
$$ \Huge
c_{i j}=\sum^{n} a_{i l} b_{l j}, \quad i=1, \ldots, m, \quad j=1, \ldots, k .
$$



```python3
a = [[1,2,3],
     [4,5,6]]
b = [[2,3,2],
     [4,5,2],
     [7,8,9]]
def matrix_multiply(a,b):
    m = len(a)
    k = len(b[0])
    n = len(b)
    result = [[0]*k for _ in range(m)]
    for i in range(m):
        for j in range(k):
            for l in range(n):
                result[i][j] += a[i][l] *b[l][j]
    return result

print(matrix_multiply(a,b))
```


#### Notes
**Matrices can only be multiplied if their "neighboring" dimensions match**


**Identity Matrix**
$$\Huge {I}_{n}:=\left[\begin{array}{c c c c c c}{{1}}&{{0}}&{{\cdots}}&{{0}}&{{\cdots}}&{{0}}\\ {{0}}&{{1}}&{{\cdots}}&{{0}}&{{\cdots}}&{{0}}\\ {{\vdots}}&{{\vdots}}&{{\ddots}}&{{\vdots}}&{{\ddots}}&{{\vdots}}\\ {{0}}&{{0}}&{{\ddots}}&{{\vdots}}&{{\ddots}}&{{\vdots}}\\ {{\vdots}}&{{\vdots}}&{{\ddots}}&{{\vdots}}&{{\ddots}}&{{\vdots}}\\ {{0}}&{{0}}&{{\cdots}}&{{0}}&{{\cdots}}&{{1}}\end{array}\right]\in\mathbf{R}^{n\times n}$$

```python3
numpy.identity(n)

```


$$ \Huge \forall A\in\mathbb{R}^{m\times n},B\in\mathbb{R}^{n\times p},C\in\mathbb{R}^{p\times q}:(A B)C=A(B C)$$

## Determinant
- For any square matrix $\textbf A \in\mathbb{R}^{n\times n}$ it holds that $\textbf A$ is invertible if and only if $\operatorname{det}(A)\neq\ 0$
- Determianant is the ammount of area of $\hat i$ and $\hat j$  change after composition

$$ \Huge \mathrm{det}(A)=\left|_{a_{21}}^{a_{11}}\ \ _{a_{22}}^{a_{12}}\right|=a_{11}a_{22}-a_{12}a_{21}\,$$

![[Pasted image 20220612000010.png]]

## Inverse and Tranpose
### Inverse


### Tranpose

## Reduce row echelon 
**Pseudocode**

```
function ToReducedRowEchelonForm(Matrix M) is
    lead:= 0
    rowCount:= the number of rows in M
    columnCount:= the number of columns in M
    for 0 ≤ r < rowCount do
        if columnCount ≤ lead then
            stop function
        end if
        i = r
        while M[i, lead] = 0 do
            i = i + 1
            if rowCount = i then
                i = r
                lead = lead + 1
                if columnCount = lead then
                    stop function
                end if
            end if
        end while
        if i ≠ r then Swap rows i and r
        Divide row r by M[r, lead]
        for 0 ≤ i < rowCount do
            if i ≠ r do
                Subtract M[i, lead] multiplied by row r from row i
            end if
        end for
        lead = lead + 1
    end for
end function
```


