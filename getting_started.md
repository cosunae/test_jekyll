---
layout: page
title: Getting started
permalink: /getting-started/
index: getting-started
menu:
 - Coordinate System
 - Storages
 - Stencils
order: 2
---

This chapter is for readers with the first contact to the GridTools library. We will show how to use GridTools to solve a 
(simple) PDE. The example will be a slightly modified version of the fourth-order horizontal smoothing 
filter from \cite{Gysi2015}. The way to the final GridTools implementation will be explained step by step with a lot of 
example codes. We will not go into too much details in this chapter but refer to later chapters 
for more details once they are written\TODO{}. We also cannot cover all GridTools features in this getting started guide.

Our example PDE is given by

$$ \frac{\partial \phi}{\partial t} =\begin{cases}
- \alpha \nabla^4 \phi & z \leq z_\text{max}\\
0 & z > z_0
\end{cases}$$

where $$\nabla^4$$ is the squared two dimensional horizontal Laplacian and we apply the filter only up to some maximal 
$$z_0$$ (to make the example more interesting).

The fourth order derivative on the r.h.s. will be calculated in two steps: first we calculate the Laplacian of $$\phi$$

$$
 L = \Delta \phi =\left(\frac{\partial^2}{\partial x^2} + \frac{\partial^2}{\partial y^2}\right) \phi
$$

then we calculate the Laplacian of $$L$$

$$
 - \alpha \nabla^4 \phi =-\alpha \Delta L.
$$

For the time step we will use a simple Euler integration step (with step size $$\Delta t = 1$$)

$$
 \phi^{t+1}(\vec x) = \phi^{t}(\vec x ) + \text{r.h.s.}
$$



* The GridTools coordinate system and its notation.
* Storages: how does GridTools manage the input and output fields.
* The first stencil: calculating $$L$$, the second order Laplacian of $$\phi$$.
* The final computation: function calls, Do-method overloads and temporaries
* MISSING: How to pass the $$\alpha$$: Global accessors TODO
* MISSING: Distributing the work over multiple nodes: GCL TODO
* MISSING: Boundaries TODO

## Coordinate System ##
For a finite difference discretization we restrict the field $$\phi \in \mathbb{R}^3$$ to a discrete grid.
We use the notation $$i = x_i$$ and $$j = y_j$$ for the horizontal dimension and $$k = z_k$$ for the vertical dimension, 
where $$x_i, y_j, z_k$$ are the $$x,y,z$$ coordinates restricted on the grid. The \emph{computation domain} is defined by 
all grid points in our domain of interest
\begin{equation}
 \Lambda = (i,j,k) \quad \text{with}\quad i \in \{ 0\dots N_i-1\}, j \in \{0\dots N_j-1\}, k\in\{0 \dots N_k-1\}
\end{equation}


GridTools supports any number of dimension, however it will treat one dimension, here the $$k$$, dimension differently: 
the $$ij$$-plane is executed in parallel while the computation in $$k$$ can be sequential. The consequence is that there 
must not be a 
dependency in $$ij$$ within a stencil while there can be a dependency in $$k$$. For now (this chapter) it is sufficient to 
just remember that the $$ij$$-plane and the $$k$$ dimension are treated differently by GridTools.

The calculation domain is surrounded by a \emph{boundary region}\footnote{Note that in later chapters we will also 
deal with \emph{halos}, e.g.\ in the context of loop tiling or multi-node environments. For now the differences 
between the global boundary and the halo regions are not important.} as depicted in 
\reffig{fig:getting_started:coordinates}. Computation happens only within the calculation domain but values might be 
read from grid points in the boundary region.


![Coordinates]({{ site.url }}/images/coordinates.png)

## Storages ##

### Backend ###

### Memory layout ###

### The storage type ###

### Storage with halo ###

## Stencils ##

### Example: Naive 2D Laplacian ###

{% highlight c++ %}
{% include getting_started/code-snippets/naive_laplacian.hpp %}
{% endhighlight %}


