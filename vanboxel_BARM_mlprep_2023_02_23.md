---
title: BARNstorm and Transform
subtitle: Bayesian Additive Regression Networks
author:
- Danielle Van Boxel
date: 23 Feb 2023
institute: University of Arizona
theme: AnnArbor
colortheme: albatross
fontsize: 10pt
aspectratio: 169
toc: true
toc-title: I Table of Contents
header-includes:
    - \definecolor{branch}{rgb}{0.33,.8,0.99}
    - \definecolor{leaf}{rgb}{0.1,.1,.1}
    - \definecolor{offwhite}{rgb}{0.9,.9,0.9}
    - \definecolor{tan}{rgb}{0.97,0.66,0.72}
    - \usecolortheme[named=leaf]{structure}
    - \setbeamercolor*{palette primary}{bg=branch, fg = leaf}
    - \setbeamercolor*{palette secondary}{bg=tan, fg = leaf}
    - \setbeamercolor*{palette tertiary}{bg=branch, fg = leaf}
    - \setbeamercolor*{palette quaternary}{bg=tan, fg = leaf}
    - \setbeamercolor*{titlelike}{bg=branch, fg = leaf}
    - \setbeamercolor*{section name}{bg=tan, fg = leaf}
    - \setbeamercolor*{subsection name}{bg=branch, fg = leaf}
    - \setbeamercolor*{part name}{bg=tan, fg = leaf}
    - \setbeamercolor*{background canvas}{bg=white, fg = black}
    - \setbeamercolor*{frametitle}{bg=branch, fg = leaf}
    - \setbeamercolor*{frametitle}{bg=tan, fg = leaf}
    - \setbeamercolor*{normal text}{bg=white, fg = black}
    - \usepackage{textpos}
    - \addtobeamertemplate{frametitle}{}{\begin{textblock*}{100mm}(.95\textwidth,-0.9cm)\includegraphics[height=0.9cm,width=0.9cm]{figs/cute_ua.png}\end{textblock*}}
...

# Overall Goals

## Comps Goals: Math and Machine Learning
 
* *More rigorous formulation of machine learning model: Bayesian posterior, NN optimization, and inference usage*
* Better motivation for BARN (automatic architecture search, broad applicability, more background research needed)
* *Ensure understanding of inspirational BART paper (background and MCMC)*
* Exploration and guidance for setting of hyperparameters
* (stretch) Explore convergence properties/error analysis of BARN
    * More rigorous computing of convergence metrics for MCMC
    * Any provable properties/conditions for convergence (and speed of convergence)
    * Or non-convergence
    * Explore Gelman-Rubin statistic
* (stretch) Development of Support Vector Machine for BARN
* (stretch) Develop more general neural network architecture for BARN to explore

## Comps Goals: Data Science and Implementation

* *Refactor BARN code into Python module with arbitrary base model.*
* *Write practical tutorial with small example for users.*
* (stretch) Develop R library as well.
* (stretch) Improve speed by order of magnitude by cleverly implementing ensemble of NNs.
* (stretch) Implement classification form of BARN (e.g. Probit model)
* Inclusion of additional data sets (larger scale or demonstrating some capability/problem with BARN).
* Expansion of testing metrics/procedure

# Progress

## Full BARN Posterior

\small
Big overall posterior:
$$
P((M_1, w_1), \ldots, (M_m, w_m),\sigma|Y,X) = \frac{P(Y| (M_1, w_1), \ldots, (M_m, w_m), X, \sigma)P((M_1, w_1), \ldots, (M_m, w_m),\sigma)}{P(Y)}
$$

::: columns
:::: {.column width=50%}
Suppose individual priors are independent.

$$
\begin{aligned}
P((M_1, w_1), \ldots, (M_m, w_m),\sigma) &= P(\sigma) \prod_j P(M_j,w_j)\\
P((M_1, w_1), \ldots, (M_m, w_m),\sigma) &= P(\sigma) \prod_j P(w_j|M_j)P(M_j)\\
P(w_j|M_j) &= \prod_i P(w_{ij} | M_j)
\end{aligned}
$$

::::
:::: {.column width=50%}
Adapt from BART

* $w_{ij}$ is $i$th weight in model $j$.
* $P(w_{ij}|M_j) = P(w_{ij})$?
* $P(w_{ij}|M_j) = P(w_{ij}'|M_j)$? instead?
* $P(w_{ij}|M_j) \sim P(\mu_{ij}|T_j)$ from BART.  They use normal, with wide enough variation such that summing $m$ of these terminal nodes means you very likely to include $(y_{min},y_{max})$.

::::
:::

\normalsize

## Sufficiency of $R_j$ in "sum-of-X"

\small
$R_j$ is sufficient stat for $T_j,M_j$ (and likewise for any such summation model).  Consider a generic likelihood for a single data point (the joint density over many points being a product of these, so we can simply factorize to generalize).  Recall $R_j = y - \sum_{k\neq j} g(x; T_k, M_k)$

$$
\begin{aligned}
f_X(y| T_j,M_j,x) &= \frac{1}{\sqrt{2\pi\sigma^2}} e^{ \frac{-(y - \sum_{k=1}^m g(x;T_k, M_k))^2}{2\sigma^2}}\\
f_X(y| T_j,M_j,x) &= \frac{1}{\sqrt{2\pi\sigma^2}} e^{ \frac{-(R_j - g(x;T_j,M_j))^2}{2\sigma^2}}\\
\end{aligned}
$$

So by the Neyman-Fisher Factorization theorem, $R_j$ is a sufficient statistic for the output given $T_j,M_j$ (cite book?).  And by (566 book remark), this also means that the posterior $\pi(T_j,M_j|x,y,T_{(j)},M_{(j)}) = \pi(T_j,M_j|x,R_j)$.  This enables BART equation 14:

$$
p(T_j| M_j, \sigma) \propto p(T_j) \int p(R_j|T_j, M_j, \sigma) p(M_j|T_j,\sigma) dM_j
$$

This sufficiency, however, applies to any such "sum-of-x" model with a similar normal likelihood function.

\normalsize

## Full BARN Posterior Redux

\small
Apply products and simplify by removing components which don't change across ensembles:

$$
\begin{aligned}
P((M_1, w_1), \ldots, (M_m, w_m),\sigma|Y,X) &= P(Y|\ldots)\frac{P(\sigma)\prod_j (\prod_i P(w_{ij}|M_j))P(M_j)}{P(Y)}\\
P((M_1, w_1), \ldots, (M_m, w_m),\sigma|Y,X) &= P(Y|\ldots)\frac{P(\sigma)\prod_j P(w_{ij}|M_j)^KP(M_j)}{P(Y)}\\
P((M_1, w_1), \ldots, (M_m, w_m),\sigma|Y,X) &\propto P(Y|\ldots)P(\sigma)\prod_j P(w_{ij}|M_j)^KP(M_j)\\
\text{adapting BART } P(Y|T_j,M_j,X) &= \frac{1}{\sqrt{2\pi\sigma^2}} e^{ \frac{-(R_j - g(X;T_j,M_j))^2}{2\sigma^2}}\\
\end{aligned}
$$

\normalsize

## Distribution of NN output

How to model with an NN, distribution of output is not so clearcut?

$$
g_j = \sum_{k \in \text{second layer}} w_{kj} h_k(X) = \sum_{k \in \text{second layer}} w_{kj} h(\sum_{i \in \text{first layer}} x_{ij} w_{ij})
$$

So output depends on input more directly.  Might be able to sum over these.

If we assume $h_k(X) \sim N(0,1)$, as when doing batch normalization, then we want $w_{kj} \sim N(\mu_k, \sigma^2_k)$!

*But the number of neurons varies*!  Give each net an equal "share" of the variance/mean?  Is it ok if the weight prior shifted in this way?

Need a prior on $\sigma$ as well, BART uses $\chi^2$, and they est with an OLS.  Just measure standard deviation of residuals.


## Other Work

* Updated [documentation](https://dvbuntu.github.io/barmpy/) for BARN methods.
* Added [unit tests](https://github.com/dvbuntu/barmpy/tests/) for NN and BARN overall.
* Updated old resume/CV; revising citation style.
* Accepted invitation to Speak at Women in Data Science Conference 2023, April 20 ~~21~~, Afternoon.  Will describe BART theory and applications.

# Plans

## Schedule

![Skeleton complete, 2/2/23](figs/research_schedule_rot.pdf){ height=140% }

## Schedule Slippage

* BARN motivation clarifications (architecture search, etc) (1 week)
* Rigorous NN optimization (2 weeks) 
* Hyperparameter search (nominally starting now, but may be post-comps)
* New data analysis (1 week, may be post-comps)

## Next Time (2 Mar 2023?)

* 9 Mar 2023 is Spring Break, and Cristian is out next week, so borrow the slot?
* Further develop BARN fundamentals from BART analogies
    * Address flat prior on weights and how this affects expected distribution
* Update comps draft with these explanations and additional review
* (stretch) Start drafting/citing for motivating BARN (arch search, general applicability, where do other methods fail, what does BART add to trees)

## Links

Email: vanboxel@math.arizona.edu

Old Repo: [https://github.com/dvbuntu/barn](https://github.com/dvbuntu/barn)

Math Repo: [https://github.com/dvbuntu/barm](https://github.com/dvbuntu/barm)

Library Repo: [https://github.com/dvbuntu/barmpy](https://github.com/dvbuntu/barmpy)

## Bibliography

\footnotesize

[//]: # (Fixing highlighting_)
