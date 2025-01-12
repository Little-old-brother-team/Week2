# Mid Term Review

## 1 Errors and Uncertainties

### Storage
1. Fixed point notation:  
   Fixed decimal point;  
   Same absolute error.  
2. Floating point notation:  
   Single: Sign(1) + Exponent(8) + Fraction(23) + Bias(127)  
   Double: Sign(1) + Exponent(11) + Fraction(52) + Bias(1023)  
   **First mantissa bit is always 1**  

### Error
1. Round off error  
    **From storage limit**.  
    Appear significantly in:
    1. subtractive cancellation (big - big = small) 
    2. large computation (many rounds) 
    3. adding a large and a small number  
    4. smearing (summation from large to small)
    5. inner products  

2. Truncation errors  
   **From mathematic approximation**


## 2 Roots

### Bisection Method
**Basic Method**
1. Method:   
    1. search root in $[x_1, x_2]$, and $f(x_1)f(x_2)<0$
    2. divide $[x_1, x_2]$ **equally**, determine where is the root
    3. repeat 1. and 2., until reach the target

2. Pseudocode and Example: 
```
Define F(x)
Define BisectionMethod(F, x1, x2, target):
    Repeat N times or until error < target :
        mid = (x1 + x2) / 2
        if (F(x1)F(mid) < 0): x2 = mid
        else: x1 = mid
    return mid
 
Find x1, x2 which make F(x1) * F(x2) < 0
Call BisectionMethod(F, x1, x2, eps)
```

### Inverse Linear Interpolation
**Improved Bisection Method**
Method: Change the middle point $\frac{x_1+x_2}{2}$ of Bisection Method to learn prediction $x_2-f(x_2)\frac{x_2-x_1}{f(x_2)-f(x_1)}$.

### Secant Method
**Can't find a initial bracket**
Method: Don't need to consider the signs of boundaries. And mid point is the same as Inverse Linear Interpolation, which is $x_2-f(x_2)\frac{x_2-x_1}{f(x_2)-f(x_1)}$. 

### Brute Force Method
1. Method:  
    1. Divide the interval into small segments
    2. Find the segment $[x_1, x_2]$ containing a root
    3. Final result: $x_2-f(x_2)\frac{x_2-x_1}{f(x_2)-f(x_1)}$
2. Pseudocode and Example: 
```
Define F(x)
Define BruteForceMethod(F, x1, x2, n):
    divide [x1, x2] into segments [x1, x2, x3 ... xn]
    while F(xi) * F(xj) > 0:
        ++ i
        ++ j

    return xj - F(xj) * (xj - xi) / (F(xj) - F(xi))

Find x1, x2 
Call BisectionMethod(F, x1, x2, n)
```

### Fixed Point Method
1. Method:  
    **Iteration:** $x_n = g(x_{n-1})$ for equation $g(x)-x=0$.

2. Pseudocode and Example:  
    find solution of $F(x) = x$:
```
Define F(x)
Define FixedPointMethod(F, x1, N, target):
    Repeat N times or until error < target:
        x = F(x)
    return x

Find x1
Call BisectionMethod(F, x1, N, eps)
```

### Newton Raphson Method
1. Method: 
    **Iteration:** $\Delta x = -\frac{f(x)}{f^\prime (x)}$
2. Pseudocode and Example: 
```
Define F(x), F_prime(x)
Define NewtonMethod(F, F_prime, x1, target):
    Repeat N times or until error < target:
        x -= F(x)/F_prime(x)
    return x

Find x1
Call BisectionMethod(F, F_prime, x1, eps)
```

## Matrix

### (non-linear) Newton-Raphson Algorithm
1. Method: **Just a high dimensional Newton method**  
    1. Calculate Jacobi matrix $F^\prime$ of equations $f(x)$.
    2. Solve $\Delta x$ by $F^\prime \Delta x = -f(x)$
2. Pseudocode and Example: 
```
Define F(x), F_prime(x)
Define NewtonMethod(F, F_prime, x1, target):
    Repeat N times or until error < target:
        solve F_prime(x) * delta_x = F(x)
        x -= delta_x
    return x

Find x1
Call BisectionMethod(F, F_prime, x1, eps)
```

### (linear) Gaussian Elimination Method with pivoting
1. Method: for $Ax=b$  
    1. Reduce matrix $A$ to upper triangular matrix with $b$. Before each elimination try to put a row with largest pivot on the eliminating row. 
    2. Back substitution.
2. Pseudocode and Example:
```
Define Elimination(M, b):
    # reduce to an upper triangular matrix
    for row i from 0 to n:
        exchange the row with largest pivot to row i
        for row j from i to n:
            eliminate row j with row i
    
    # get solution
    for row i from n to 0:
        eliminate column i and get solution

    return solution
```

### (linear tridiagonal) Thomas Method
**a specialized Gaussian elimination method**
1. Method: Elimination becomes very easy when the matrix is tridiagonal. Just apply eliminate for one time when reducing a row. 
2. Pseudocode and Example:
```
Define Thomas(M, b):
    # reduce to an upper triangular matrix
    for row i from 1 to n:
        eliminate row i with row i-1
    
    # get solution
    for row i from n to 0:
        eliminate column i and get solution

    return solution
```

### (linear) Jacobi Method
**high dimensional fixed-point method**
1. Method:
    1. Split the given matrix $A$ by 
    $$
    A=D+L+R
    $$
    where $D$ is a diagonal matrix, $L$ and $R$ are left and right part of the matrix.  
    **Iteration:**  
    $$
    D(x^{k+1}-x^k)=b-Ax^k
    $$
    which is:
    $$
    \Delta x=D^{-1}(b-(L+R)x)
    $$
1. Pseudocode and Example:
```
Define A
Define FixedPointMethod(A, x1, N, target):
    split A to D, L, R
    Repeat N times or until error < target:
        x1 = D^(-1) * (b - (L + R) * x1)
    return x1

Find x1
Call BisectionMethod(A, x1, N, eps)
```

### Jacobi method
Indirect algorithm to solve linear equation by iteration.
* Attention: Only valid when A is diagonal dominant!
* Main idea: First convert A to L + U + D (Lower, Upper and Diag). Then calculate $J = -D^{-1}(L+R)$, find the eigenvalue of J and check whether the max absolute value is greater than 1. If so, the iteration will not convergence, you'd better find another algorithm to solve it! 
* Algorithm: $x_j^{(k+1)} = \frac{1}{a_{jj}}(b_j-\sum_{m\neq j}a_{jm}x_m^{(k)})$
```python
def jacobi(A, b, N=1000, x=None):
    """Solves the equation Ax=b via the Jacobi iterative method."""
    # Create an initial guess if needed                                                                                                                                                            
    if x is None:
        x = zeros(len(A[0]))

    # Create a vector of the diagonal elements of A                                                       
    # and subtract them from A                                                                                     
    D = diag(A)
    R = A - diagflat(D)
    # Iterate for N times                                                                                         
    for i in range(N):
        x_new = (b - dot(R,x)) / D
        if allclose(x, x_new, rtol=1e-8):
            break
        
        x = x_new
    return x
```

### Gauss-Seidel method
* Main idea:  Gauss-Seidel method is a refinement of the Jacobi method, it converges faster than Jacobi method by introduce the updated value in the same iteration.
* Algorithm: $x_j^{(k+1)} = \frac{1}{a_{jj}}(b_j-\sum_{m>j}a_{jm}x_m^{(k)}-\sum_{m<j}a_{jm}x_m^{(k+1)})$
```python
def Gauss_Seidel(A, b, N=25, x=None, omega=1.5):
    """Solves the equation Ax=b via the Jacobi iterative method."""
    # Create an initial guess if needed                                                                                                                                                            
    if x is None:
        x = zeros_like(b)

    # Iterate for N times  
    for it_count in range(N):
        x_new = zeros_like(x)
        print("Iteration {0}: {1}".format(it_count, x))
        for i in range(A.shape[0]):
            s1 = dot(A[i, :i], x_new[:i])
            s2 = dot(A[i, i + 1:], x[i + 1:])
            x_new[i] = (b[i] - s1 - s2) / A[i, i]
        if allclose(x, x_new, rtol=1e-8):
            break
        x = x_new
    return x
```

### Conjugate Gradient Descent Method
**Method:**   
    Compared to Gradient Descent Method, the descent vector is conjugate with each other. Here conjugate means $u^T A v=v^T A u = 0$, where A is a symmetrical matrix.  
    Problem becomes 
    $$ 
    f=\frac{1}{2}x^T A x -b^T x \\
    \Delta f = Ax -b =0
    $$
    Residue: $r_k=b-Ax_k=-\Delta f(x_k)$  
    **Iteration:**  
    $x_{n+1}=x_{n} + \alpha_n p_n, \alpha_n = \frac{r_n^T r_n}{p^T_n A p_n}$  
    $r_{n+1}=r_n - a_n A p_n$  
    p_{n+1}=r_{n+1}+\beta_n p_n, \beta_n = \frac{r_{n+1}^T r_{n+1}}{r_n^T r_n}

### Power method
* Main idea: Find the largest or smallest eigenvalue by mutiply $A$ or $A^{-1}$ for k times (k>>1). Since $\frac{\lambda_{i}}{\lambda_{max}} < 1$, then $(\frac{\lambda_{i}}{\lambda_{max}})^k \approx 0$
* Algorithm: $\lambda_{max} = \frac{x^T_kx_{k+1}}{x^T_kx_{k}}$, the same algorithm for $\lambda_{min}$.

```python
def power(A,x):
    def rayleigh_quotient(A,x):
        return np.dot(x, np.dot(A, x))/np.dot(x,x)
        
    # function to normalise a vector
    def normalise(x,eps=1e-10):
        N = np.sqrt(np.sum(abs(x)**2))
        if N < eps: # in case it is the zero vector!
            return x
        else:
            return x/N

    RQnew = rayleigh_quotient(A,x)
    RQold = 0

    # perform the power iteration
    while np.abs(RQnew-RQold) > 1e-6:
        RQold = RQnew
        x = normalise(np.dot(A, x))
        RQnew = rayleigh_quotient(A, x)
```

### SOR
* Main idea: SOR is also a refinement of the Gauss Seidel method, by introducing a parameter $\omega$ to balence the x value between the old and new iteration, SOR can achieve the highest speed among all algorithm introduced above.
* Algorithm: $x_i^{(k+1)}=(1-\omega)x_i^{(k)}+\frac{\omega}{a_{ii}}\left(b_i-\displaystyle\sum_{j<i}a_{ij}x_j^{(k+1)}-\displaystyle\sum_{j>i}a_{ij}x_j^{(k)}\right)$
```python
def SOR(A, b, N=25, x=None, omega=1.5):
    """Solves the equation Ax=b via the Jacobi iterative method."""
    # Create an initial guess if needed                                                                                                                                                            
    if x is None:
        x = zeros_like(b)

    # Iterate for N times  
    for it_count in range(N):
        x_new = zeros_like(x)
        print("Iteration {0}: {1}".format(it_count, x))
        for i in range(A.shape[0]):
            s1 = dot(A[i, :i], x_new[:i])
            s2 = dot(A[i, i + 1:], x[i + 1:])
            x_new[i] = (1-omega) * x[i] + omega * (b[i] - s1 - s2) / A[i, i]
        if allclose(x, x_new, rtol=1e-8):
            break
        x = x_new
    return x
```