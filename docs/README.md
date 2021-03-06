# Parametric Cubic Spline #

## Boundary Conditions ##
PCS supports four types of boundary conditions
- Natural: The moments at the end point are zero.
- Hermite: The tangent vector to the end point is defined.
- Periodic
- Not-a-knot

## Algorithms ##

### Tridiagonal Matrix Algorithm (TDMA) ###
Computation of the moments requires solving the linear system
$$
Ax = d
$$
where A is of the form
$$
A = 
\begin{bmatrix}
  b_1 & c_1 &     &     & a_0     \\ 
  a_2 & b_2 & c_2 &     &         \\
      & a_3 & b_3 &\cdot&         \\
      &     &\cdot&\cdot& c_{n-1} \\
  c_n &     &     & a_n & b_n
\end{bmatrix}.
$$

By exploiting the structure of $A$, the problem can be solved in linear time $O(n)$ instead of $O(n^3)$ required by Gaussian elimination. 

If $a_0=c_n=0$, e.g. in case of natural bounding conditions, the system is of tridiagonal form and can be readily solved by the tridiagonal matrix algorithm (TDMA), also known as Thomas algorithm. In the perturbed case, e.g. arising from periodic bounding conditions, we can make use of the Sherman-Morrison formula to recover a tridiagonal problem. The above problem can be expressed as
$$
(A'+uv^T)x=d
$$
where
$$
\begin{aligned}
u^T &= \begin{bmatrix} -b_1 & 0 & \cdots & 0 & c_n \end{bmatrix}, \\
v^T &= \begin{bmatrix} 1 & 0 & \cdots & 0 & -\frac{a_1}{b_1}, \end{bmatrix} \\
A'  &= \begin{bmatrix}
  2b_1 & c_1 &     &     & 0       \\ 
  a_2  & b_2 & c_2 &     &         \\
       & a_3 & b_3 &\cdot&         \\
       &     &\cdot&\cdot& c_{n-1} \\
  0    &     &     & a_n & b_n+\frac{c_na_1}{b_1}
\end{bmatrix}.
\end{aligned}
$$

Hence, $A'$ is triadiagonal again. The solution to the original problem is now obtained by solving 
$$
A'\begin{bmatrix}y & q\end{bmatrix} = \begin{bmatrix}d & u\end{bmatrix}
$$
and computing
$$
x = y - \frac{v^Ty}{1+v^Tq}q
$$