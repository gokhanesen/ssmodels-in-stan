---
title: State Space Model Notes
---

## Models

### The Linear State Space Model

[@DurbinKoopman2012, Sec 3.1]

The linear Gaussian state space model (SSM)[^dlm] the the $n$-dimensional observation sequence $\vec{y}_1, \dots, \vec_
$$
\begin{aligned}[t]
\vec{y}_t &= \vec{d}_t + \mat{Z}_t \vec{\alpha}_t + \vec{\varepsilon}_t,  &
\vec{\varepsilon}_t & \sim N(0, \mat{H}_t), \\
\vec{\alpha}_{t + 1} &= \vec{c}_t + \mat{T}_t \vec{\alpha}_t + \mat{R}_t \vec{\eta}_t,  &
\vec{\eta}_t & \sim N(0, \mat{Q}_t), \\
&& \vec{\alpha}_1 &\sim N(\vec{a}_1, \mat{P}_1)
\end{aligned}
$$
for $t = 1, \dots, n$.
The first equation is called the *observation* or *measurement equation*.
The second equation is called the *state*, *transition*, or *system equation*.
The vector $\vec{y}_t$ is a $p \times 1$ vector called the *observation vector*.
The vector $\alpha{\alpha}_t$ is a $m \times 1$ vector called the *state vector*.
The matrices are vectors, $\mat{Z}_t$,$\mat{T}_t$, $\mat{R}_t$, $\mat{H}_t$, $\mat{Q}_t$, $c_t$, and $d_t$ are called the *system matrices*.
The system matrices are considered fixed and known in the filtering and smoothing equations below, but can be parameters themselves.
The $p \times m$ matrix $\mat{Z}_t$ links the observation vector $\vec{y}_t$ with the state vector $\vec{\alpha}_t$.
The $m \times m$ transition matrix $\mat{T}_t$ determines the evolution of the state vector, $\vec{\alpha}_t$.
The $r \times 1$ vector $\vec{\eta}_t$ is called the *state disturbance vector*,
and the $p \times 1$ vector $\vec{\varepsilon}_t$ is called the *observation disturbance vector*.
An assumption is that the state and observation disturbance vectors are uncorrelated, $\Cov(\vec{\varepsilon}_t, \vec{\eta}_t) = 0$.


In a general state space model, the normality assumptions of the densities of $\vec{\varepsilon}$
and $\vec{\eta}$ are dropped.

In many cases $\mat{R}_t$ is the identity matrix.
It is possible to define $\eta^*_t = \mat{R}_t \vec{\eta}_t$, and $\mat{Q}^* = \mat{R}_t \mat{Q}_t' \mat{R}'_t$.
However, if $\mat{R}_t$ is $m \times r$ and $r < m$, and $\mat{Q}_t$ is nonsingular, then it is useful to work with the nonsingular $\vec{\eta}_t$ rather than a singular $\vec{\eta}_t^*$.

The initial state vector $\vec{\alpha}_1$ is assume to be generated as,
$$
\alpha_1 \sim N(\vec{a}_1, \mat{P}_1)
$$
independently of the observation and state disturbances $\vec{\varepsilon}$ and $\vec{\eta}$.
The values of $\vec{a}_1$ and $\mat{P}_1$ can be considered as given and known in most stationary processes.
When the process is nonstationary, the elements of $\vec{a}_1$ need to be treated as unknown and estimated.
This is called *initialization*.

[^dlm]: This is also called a dynamic linear model (DLM).

matrix/vector         dimension
--------------------- --------------
$\vec{y}_t$           $p \times 1$
$\vec{\alpha}_t$      $m \times 1$
$\vec{\varepsilon}_t$ $m \times 1$
$\vec{\eta}_t$        $r \times 1$
$\vec{a}_1$           $m \times 1$
$\vec{c}_t$           $m \times 1$
$\vec{d}_t$           $p \times 1$
$\mat{Z}_t$           $p \times m$
$\mat{T}_t$           $m \times m$
$\mat{H}_t$           $p \times p$
$\mat{R}_t$           $m \times r$
$\mat{Q}_t$           $r \times r$
$\mat{P}_1$           $m \times m$
--------------------- --------------

Table: Dimensions of matrices and vectors in the SSM

### Filtering

From [@DurbinKoopman2012, Sec 4.3]

$$
\begin{aligned}[t]
\vec{v}_t &= \vec{y}_t - \mat{Z}_t \vec{a}_t - \vec{d}_t, \\
\mat{F}_t &= \mat{Z}_t \mat{P}_t \mat{Z}_t\T + \mat{H}_t, \\
\vec{a}_{t|t} &= \vec{a}_t + \mat{P}_t \mat{Z}_t\T \mat{F}_t^{-1} v_t , \\
\mat{P}_{t|t} &= \mat{P}_t - \mat{P}_t \mat{Z}_t\T \mat{F}_t^{-1} \mat{Z}_t \mat{P}_t , \\
\vec{a}_{t + 1} &= \mat{T}_t \vec{a}_t + \mat{K}_t \vec{v}_t + \vec{c}_t \\
&= \mat{T}_{t|t} \vec{a}_{t|t} + \vec{c}_t, \\
\mat{P}_{t + 1} &= \mat{T}_t \mat{P}_t (\mat{T}_t - \mat{K}_t \mat{Z}_t)\T + \mat{R}_t \mat{Q}_t \mat{R}_t\T \\
& = \mat{T} \mat{P}_{t|t} \mat{T}_t\T + \mat{R}_t \mat{Q}_t \mat{R}_t\T
\end{aligned}
$$
The vector $\vec{v}_t$ are the *one-step ahead forecast errors$.

The matrix $\mat{K}_t$ is called the *Kalman gain*,
$$
\mat{K}_t = \mat{T}_t \mat{P}_t \mat{Z}_t\T \mat{F}_t^{-1} .
$$
This gives,
$$
\begin{aligned}[t]
\vec{a}_{t + 1} &= \mat{T} \vec{a}_{t|t}  = \mat{T}_t \vec{a}_t + \mat{K}_t \vec{v}_t , \\
\vec{P}_{t + 1} &= \mat{T}_t \mat{P}_t \left( \mat{T}_t - \mat{K}_t \mat{Z}_t \right)\T + \mat{R}_t \mat{Q}_t \mat{R}_t\T .
\end{aligned}
$$
These two equations are called the *prediction step*.
The steps for $\vec{a}_{t|t}$ and $\mat{P}_{t|t}$ are called the *updating step*.
$$
\begin{aligned}[t]
\vec{a}_t &= \E(\vec{\alpha}_t | y_{1, \dots, t - 1}), &
\vec{P}_t &= \Var(\vec{\alpha}_t | y_{1, \dots, t - 1}), \\
\vec{a}_{t|t} &= \E(\vec{\alpha}_t | y_{1, \dots, t}), &
\vec{P}_{t|t} &= \Var(\vec{\alpha}_t | y_{1, \dots, t}) .
\end{aligned}
$$

matrix/vector         dimension
--------------------- --------------
$\vec{v}_t$           $p \times 1$
$\vec{a}_t$           $m \times 1$
$\vec{a}_{t|t}$       $m \times 1$
$\mat{F}_t$           $p \times p$
$\mat{K}_t$           $m \times p$
$\mat{P}_t$           $m \times m$
$\mat{P}_{t|T|$       $m \times m$
$\vec{x}_t$           $m \times 1$
$\mat{L}_t$           $m \times m$
--------------------- --------------

Table: Dimensions of matrices and vectors in the SSM

See [@DurbinKoopman2012, Sec 4.3.4]:
For a time-invariant state space model, the Kalman recursion for $\mat{P}_{t + 1}$ converges to a constant matrix $\bar{\mat{P}}$,
$$
\bar{\mat{P}} = \mat{T} \bar{\mat{P}} \mat{T}\T - \mat{T} \bar{\mat{P}} \mat{Z}\T \bar{\mat{F}}^{-1} \mat{Z} \bar{\mat{P}} \mat{T}\T + \mat{R} \mat{Q} \mat{R}\T ,
$$
where $\bar{\mat{F}} = \mat{Z} \bar{\mat{P}} \mat{Z}\T + \mat{H}$.

See [@DurbinKoopman2012, Sec 4.3.5]:
The *state estimation error* is,
$$
\vec{x}_t = \vec{\alpha}_t - \vec{a}_t,
$$
where $\Var(\vec{x}_t) = \mat{P}_t$.
The $v_t$ are sometimes called *innovations*, since they are the part of $\vec{y}_t$ not predicted from the past.
The innovation analog of the state space model is
$$
\begin{aligned}[t]
\vec{v}_t &= \mat{Z}_t \vec{x}_t + \vec{\varepsilon}_t ,  \\
\vec{x}_{t + 1} &= \mat{L} \vec{x}_{t} + \mat{R}_t \vec{\eta}_t - \mat{K}_t \vec{\varepsilon}_t , \\
\mat{K}_t &= \mat{T}_t \mat{P}_t \mat{Z}_t\T \mat{F}_t^{-1} , \\
\mat{L}_t &= \mat{T}_t - \mat{K}_t \mat{Z}_t ,
\mat{P}_{t + 1} &= \mat{T}_t \mat{P}_t \mat{L}_t\T +  \mat{R}_t \mat{Q}_t \mat{R}_T\T  .
\end{aligned}
$$
These recursions allow for a simpler derivation of $\mat{P}_{t + 1}$, and are useful for
the smoothing recursions.
Moreover, the one-step ahead forecast errors are indendendent, which allows for a simple derivation of the log-likelihood.

### Smoothing

Vector/Matrix                Dimension
---------------------------- -----------------------
$\vec{r}_t$                   $m \times 1$
$\vec{\vec{\alpha}}_t$        $m \times 1$
$\vec{u}_t$                   $p \times 1$
$\hat{\vec{\varepsilon}}_t$   $p \times 1$
$\hat{\vec{\eta}}_t$          $r \times 1$
$\mat{N}_t$                   $m \times m$
$\mat{V}_t$                   $m \times m$
$\mat{D}_t$                   $p \times p$
---------------------------- -----------------------

Table: Dimensions of vectors and matrices used in smoothing recursions

#### State Smoothing

[@DurbinKoopman2012, Sec 4.4.2]

Smoothing calculates the estimate of the state using all observations, $\hat{\vec{\alpha}} = \E(\vec{\alpha}_t | \mat{Y}_n)$ and $\mat{V}_t = \Var(\vec{\alpha} | \mat{Y}_n)$.


Smoother in Eq 4.44. Let $\hat{\vec{a}} = \E(\alpha_t | \vec{y}_1, \dots \vec{y}_n)$ and $\mat{V}_t = \Var(\vec{\alpha}_t | \vec{y}_1, \dots \vec{y}_n)$, then
$$
\begin{aligned}[t]
\vec{r}_{t - 1} &= \mat{Z}_t\T \mat{F}_t^{-1} \vec{v}_t + \mat{L}_t\T \vec{r}_t , \\
\mat{N}_{t - 1} &= \mat{Z}_t\T \mat{F}_t^{-1} \mat{Z}_t + \mat{L}_t\T \mat{N}_t \mat{L}_t, \\
\hat{\vec{\alpha}}_t &= \vec{a}_t + \mat{P}_t \vec{r}_{t - 1} , \\
\mat{V}_t &= \mat{P}_t - \mat{P}_t \mat{N}_{t - 1} \mat{P}_t ,
\end{aligned}
$$
for $t = n, \dots, 1$, with $\vec{r}_n = \vec{0}$, and $\mat{N}_t = \mat{0}$.

During the filtering pass $\vec{v}_t$, $\mat{F}_t$, $\mat{K}_t$, and $\mat{P}_t$ for $t = 1, \dots, n$ need to be stored.
Alternatively, $\vec{a}_t$ and $\mat{P}_t$ only can be stored, and $\vec{v}_t$, $\mat{F}_t$, $\mat{K}_t$ recalculated on the fly.
However, since the dimensions of $\vec{v}_t$, $\mat{F}_t$, $\mat{K}_t$ are usually small relative to $\vec{a}_t$ and $\mat{P}_t$ is is usually worth storing them.



#### Updating Smoothed States

[@DurbinKoopman2012, Sec 4.4.5]

If a new observation arises, existing smoothed estimates of the states can be updated without
having to run the full state smoother again.

Suppose a new obervation, $\vec{y}_{n + 1}$, is available.
We want to calculate $\hat{\vec{\alpha}}_{t|n + 1} = \E(\vec{\alpha} | \mat{Y}_{n + 1})$ and $\mat{V}_{t|n + 1} = \Var(\vec{\alpha}) | \mat{Y}_{n + 1}$, when we already have  $\hat{\vec{\alpha}}_{t|n} = \E(\vec{\alpha} | \mat{Y}_{n})$ and $\mat{V}_{t|n} = \Var(\vec{\alpha} | \mat{Y}_{n})$ from running smoother when $\mat{Y}_n$ was available.

Let $b_{t|n + 1} = \mat{L}_t\T \cdots \mat{L}_n\T$ with $\vec{b}_{t|n + 1} = \mat{I}_m$.
Then $b_{t|n + 1} = \mat{L}_t\T b_{t+1|n+1}$ for $t = n, \dots, 1$.
Then the states can be updated as,
$$
\begin{aligned}[t]
\hat{\vec{\alpha}}_{t|n + 1} &= \hat{\vec{a}}_{t|n} + \mat{P}_t \vec{b}_{t|n + 1} \mat{Z}_{n + 1}\T \mat{F}_{n + 1}^{-1} \vec{v}_{n + 1} , \\
\mat{V}_{t|n + 1} &= \mat{V}_{t|n} - \mat{P}_t \vec{b}_{t|n + 1} \mat{Z}_{n + 1}\T \mat{F}_{n + 1}^{-1} \mat{Z}_{n + 1} \vec{b}_{t|n + 1}\T \mat{P}_t ,
\end{aligned}
$$
for $n = t, t + 1, \dots$, with
$$
\begin{aligned}[t]
\hat{\vec{\alpha}}_{n|n} &= \vec{a}_n + \mat{P}_n \mat{Z}_n\T \mat{F}_n^{-1} \vec{v}_n, \\
\mat{V}_{n|n} &= \mat{P}_n - \mat{P}_n \mat{Z}_n\T \mat{F}_n^{-1} \mat{Z}_n \mat{P}_n ,
\end{aligned}
$$
The values of $\mat{P}_t$, $\mat{L}_t$, $\mat{F}_{n + 1}$, and $\vec{v}_{n + 1}$ are available from the Kalman filter.


#### Disturbance smoothing

[@DurbinKoopman2012, Sec 4.5]

Disturbance smoothing calculates the smoothed estimates, $\hat{\vec{\varepsilon}}_t = \E(\vec{\varepsilon} | \mat{Y}_n)$ and $\hat{\vec{\eta}} = \E(\vec{\eta}_t | \mat{Y}_n)$.

$$
\begin{aligned}[t]
\hat{\vec{\varepsilon}}_t &= \mat{H}_t (\mat{F}^{-1} \vec{v}_t - \mat{K}_t\T \vec{r}_t) , &
\Var(\vec{\varepsilon}_t | \mat{Y}_n) &= \mat{H}_t - \mat{H}_t (\mat{F}_t^{-1} + \mat{K}_t\T \mat{N}_t \mat{K}_t) \mat{H}_t , \\
\hat{\vec{\eta}}_t &= \mat{Q}_t \mat{R}_t\T \vec{r}_t , &
\Var(\vec{\eta}_t | \mat{Y}_n) &= \mat{Q}_t - \mat{Q}_t \mat{R}_t\T \mat{N}_t \mat{R}_t \mat{Q}_t , \\
\vec{r}_{t - 1} &= \mat{Z}_t\T \mat{F}_t^{-1} \vec{v}_t + \mat{L}_t\T \vec{r}_t , &
\mat{N}_{t - 1} &= \mat{Z}_t\T \mat{F}_t^{-1} \mat{Z}_t + \mat{L}_t\T \mat{N}_t \mat{L}_t
\end{aligned}
$$


Alternatively, these equations can be rewritten as,
$$
\begin{aligned}[t]
\hat{\vec{\varepsilon}}_t &= \mat{H}_t \vec{u}_t , &
\Var(\vec{\varepsilon}_t | \mat{Y}_n) &= \mat{H}_t - \mat{H}_t \mat{D}_t \mat{H}_t , \\
\hat{\vec{\eta}}_t &= \mat{Q}_t \mat{R}_t\T \vec{r}_t , &
\Var(\vec{\eta}_t | \mat{Y}_n) &= \mat{Q}_t - \mat{Q}_t \mat{R}_t\T \mat{N}_t \mat{R}_t \mat{Q}_t , \\
\vec{u}_t &= \mat{F}^{-1} \vec{v}_t - \mat{K}_t\T \vec{r}_t , &
\mat{D}_t &= \mat{F}_t^{-1} + \mat{K}_t\T \mat{N}_t \mat{K}_t , \\
\vec{r}_{t - 1} &= \mat{Z}_t\T \vec{u}_t + \mat{T}_t\T \vec{r}_t , &
\mat{N}_{t - 1} &= \mat{Z}_t\T \mat{D}_t \mat{Z}_t + \mat{T}_t\T \mat{N}_t \mat{T}_t - \mat{Z}_t\T \mat{K}_t\T \mat{N}_t \mat{T}_t - \mat{T}_t\T \mat{N}_t \mat{K}_t \mat{Z}_t .
\end{aligned}
$$
This reformulation can be computationally useful since it relies on the system matrices $\mat{Z}_t$ and $\mat{T}_t$ which are often sparse.

The smoothing error $\vec{u}_t$ and $\vec{r}_t$ are important in their own right.
The vector $\vec{r}_t$ is the scaled smoothed estimator of $\vec{\eta}_t$.

The disturbance smoothing recursions require only $\vec{v}_t$, $\mat{f}_t$, and $\mat{K}_t$ from the Kalman filter.
Unlike the state smoother, they do not require the vector $\vec{a}_t$ or matrix $\mat{P}_t$.


#### Fast state smoothing

[@DurbinKoopman2012, Sec 4.6.2]

If the variances of the states do not need to be calculated, then a faster smoothing algorithm
can be used (Koopman 1993).
The fast state smoother is defiend as,
$$
\hat{\vec{\alpha}}_t = \mat{T}_t \hat{\vec{\alpha}}_t + \mat{R}_t \mat{Q}_t \mat{R}_t\T \vec{r}_t ,
$$
for $t = 1, \dots, n$.
This is initialized via,
$$
\hat{\vec{\alpha}}_1 = \vec{a}_1 + \mat{P}_1 \vec{r}_0 .
$$
Thus the smoother is run, but only calculating $\vec{r}_n, \dots, \vec{r}_0$.
Then, $\hat{\vec{\alpha}}_1, \dots \hat{\vec{\alpha}}_t$ are calculated using the above equation.

#### Classical state smoothing

See [@DurbinKoopman2012, Sec 4.6.1]

The original state smoothing algorithm from Anderson and Moore (1979) is
$$
\begin{aligned}[t]
\hat{\vec{\alpha}}_t &= \vec{a}_{t|t} + \mat{P}_{t|t} \mat{T}_t\T \mat{P}_{t + 1}^{-1}(\hat{\vec{\alpha}}_{t + 1} - \vec{a}_{t + 1}) .
\end{aligned}
$$
Note that $\mat{T} \mat{P}_{t|t} = \mat{L}_t \mat{P}_t$.
Relative to the other state smoothing algorithm the classical smoother is more computationally burdensome because it requires inverting $\mat{P}_{t + 1}$.
The other state smoother only requires inverting $\mat{F}_t$, but that has already been inverted in the filtering pass.

## Jacknife and Deleted observations

Results in West and Harrison (1997), p. 104 and such.

## Simulation smoothers


### Mean corrections

[@DurbinKoopman2012, Sec 4.9]

Simulation smoother by mean corrections. Ch. 4.9.

**TODO**

### de Jong-Shephard method


While the mean-corrections method usually works, it may fail in some cases due
to imposed ill-defined variance matrices (see Jungbacker and Koopman 2007, sec 1).
These recursions were developed in de Jong and Shephard (1995),

**TODO**

### Forward-Filter Backwards smoother

**TODO**

## Missing observations

[@DurbinKoopman2012, Sec 4.10]

When all observations at time $t$ are missing, the filtering recursions become,
$$
\begin{aligned}[t]
\vec{a}_{t|t} &= \vec{a}_t , \\
\mat{P}_{t|t} &= \mat{P}_t , \\
\vec{a}_{t + 1} &= \mat{T}_t \vec{a}_t + \vec{c}_t \\
\mat{P}_{t + 1} &= \mat{T}_t \mat{P}_t \mat{T}_t\T + \mat{R}_t \mat{Q}_t \mat{R}_t\T
\end{aligned}
$$
This is equivalent to setting $\mat{Z}_t = \mat{0}$ (implying also that $\mat{K}_t = \mat{0}$) in the filtering equations.
For smoothing, also replace $\mat{Z}_t = \mat{0}$,
$$
\begin{aligned}[t]
\vec{r}_{t - 1} &= \mat{T}_t\T \vec{r}_t , \\
\mat{N}_{t - 1} &= \mat{T}_t\T \mat{N}_t \mat{T}_t,
\end{aligned}
$$

When some, but not all observations are missing, then replace the observation equation by,
$$
\begin{aligned}[t]
\vec{y}^*_t &= \mat{Z}^*_t \vec{\alpha}_t + \vec{\varepsilon}_t^*, & \vec{\varepsilon}_t^* &\sim N(\vec{0}, \mat{H}_t^*),
\end{aligned}
$$
where,
$$
\begin{aligned}[t]
\vec{y}^*_t &= \mat{W}_t \vec{y}_t, \\
\mat{Z}^* &= \mat{W}_t \mat{Z}_t , \\
\vec{\varepsilon}_t &= \mat{W}_t \vec{\varepsilon}_t , \\
\mat{H}^*_t &= \mat{W}_t \mat{H}_t \mat{W}_t\T ,
\end{aligned}
$$
and $\mat{W}_t$ is a selection matrix to select non-missing values.
In smoothing the missing elements are estimated by the appropriate elements of $\mat{Z}_t \hat{\vec{alpha}}_t$, where $\hat{\vec{\alpha}}_t$ is the smoothed state.


## Forecasting matrices

[@DurbinKoopman2012, Sec 4.11]

Forecasting future observations are the same as treating the future observations as missing,
$$
\begin{aligned}[t]
\bar{\vec{y}}_{n + j} &= \mat{Z}_{n + j} \bar{\vec{a}}_{n + j} \\
\bar{\mat{F}}_{n + j} &= \mat{Z}_{n + j} \bar{\mat{P}}_{n + j} \mat{Z}_{n + j}\T + \mat{H}_{n + j} .
\end{aligned}
$$

## Log log-likelihood

When initial conditions are known, the log likelihood is
$$
\begin{aligned}[t]
\log L(\mat{Y}_n) &= \log p(\vec{y}_1, \dots, \vec{y}_n) = \sum_{t = 1}^n \log p(\vec{y}_t | \mat{Y}_{t - 1}) \\
&= - \frac{np}{2} \log 2 \pi - \frac{1}{2} \sum_{t = 1}^n \left( \log \left| \mat{F}_t \right| + \vec{v}\T \mat{F}_t^{-1} \vec{v}_t \right)
\end{aligned} .
$$

See [@DurbinKoopman2012, Sec. 7.2.1]

## Integrated Sampler

$$
p(\vec{y} | \mat{H}, \mat{\Psi}) = \int p(\vec{y} | \vec{\alpha}, \mat{H}) p(\vec{\alpha} | \mat{\Psi})\,d\vec{\alpha},
$$

The log-likelihood of a state-space function can be calculated analytically and expressed up to an integrating constant marginally of the state vector $\vec{\alpha}$,
$$
\log p(\vec{y} | \mat{H}, \mat{\Psi}) = \text{const} - 0.5 \left( \sum_{t = 1}^n \sum_{i = 1}^p \log f_{t, i} - v_t^2 f^{-1}_{t,i} \right) .
$$
Thus, parameters of the state space model can be sampled as,
$$
p(\mat{H}, \mat{\Psi} | \vec{y}) \propto p(\vec{y} | \mat{H}, \mat{\Psi}) p(\mat{H}) p(\mat{\Psi}) .
$$

## Diagnostic Checking

The *standardized prediction errors* are,
$$
\vec{v}^*_t = \mat{G}_t \vec{v}_t ,
$$
where $\mat{G}_t \mat{G}_t\T = \mat{F}_t^{-1}$.
See Koopmans JSS Sec 3.3.
These residuals should satisfy independence, homoskedasticity, and normality.

- independence: Box-Ljung test statistic
- normality: Bowman and Shenton test statistic
- homoskedasticity: compare variance of standardized prediction errors of the 1st third to that of the last third. See Harvey (1989), Durbin and Koopman (2012), and Commandeur and Koopman (2007).

The *auxiliary residuals* are the standardized smoothed observation and state disturbances,
$$
\begin{aligned}[t]
e^*_t &= \frac{\hat{\varepsilon}_t}{\sqrt{\Var(\hat{varepsilon}_t)}} ,
r^*_t &= \frac{\hat{\eta}_t}{\sqrt{\Var{\hat{\eta}_t}} ,
\end{aligned}
$$
for $t = 1, \dots, n$.
The standardized smoothed observation disturbances allows for the detection of *outliers*,
while the standardized smoothed state disturbances allows for the detection of *structural breaks*.
Each auxiliary residual is a $t$-test that there was no outlier (or structural break).


## Software

### R packages

### Other