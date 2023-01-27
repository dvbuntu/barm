---
title: Brewing a Better Model with BARN
subtitle: Bayesian Additive Regression ~~Models~~ Networks
author:
- Danielle Van Boxel
date: 27 Jan 2023
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

# Background

## Reintroduction

::: columns

:::: {.column width=70%}

* Danielle (she/her)
* 3rd Year Applied Math PhD Student
* Undergrad in Applied Math and Civil Engineering
* Recently applied for small grant, [Out To Innovate](https://noglstp.org/programs-projects/career-development-fellowship/)
* Previous 10 year career in data science and statistics
* Part-time data scientist and machine learning engineer in Tucson
* Other interests: RPGs, LGBTQ+ representation, birding

![Festive Danielle](figs/danielle_festive.jpg){ height=45% }

::::
:::: {.column width=30%}

![Professional Danielle](figs/danielle_prof.jpg){ height=40% }

![Cute Danielle](figs/danielle_cute.jpg){ height=25% }

::::
:::

## BARN High Level Overview

* *Concept*: Find a method for regression models that is broadly applicable and automatically designs an architecture.
* *Implementation*: Adapt BART procedure to use MCMC to sample from space of neural network *models* conditioned on error.
* *Related Work*: Related Regression Techniques [@chipman2010bart; @breiman2001random], MCMC/Gibbs' [@metropolis1953equation; @geman1984stochastic]
* *Data*: UCI Benchmark regression problems [@dua2017uci], isotope measurement problem, [@roman2022bayclump]

## Current State of Research

::: columns

:::: {.column width=70%}

* Started in Spring 2022 for Monte Carlo (MATH577) class project
* Revised Summer 2022 with additional data
* Presented at Arizona-Los Alamos Days, Los Alamos, NM, August 2022
* Presented at AMS Central Section Meeting, El Paso, TX, September 2022
* Poster at GIDP Showcase, Tucson, AZ, December 2022
* Seeking venue for peer-reviewed publication
* Exploring grant to fund additional research

::::
:::: {.column width=30%}

![](figs/ams_logo.gif){ height=30% }

![](figs/uala_logo.png){ height=40% }

::::
:::


# Motivation

## Regression Problem

::: columns

:::: {.column width=30%}

* Many numeric input variables, $x_i \in \mathbb{R}^d$
* Single continuous output, $y_i \in \mathbb{R}$
* Seek $f(x_i) \approx y_i$
* What should $f$ look like?  Linear regression, decision trees, neural networks, etc
::::
:::: {.column width=70%}

![Regression models can take many forms but predict continuous output](figs/regress.png){ height=80% }

::::
:::

## Features of Ideal Regression Model

* Accurate - low testing error, typically measured by Root Mean Square Error (RMSE)
* Fast - low computation time relative to problem size, measured in seconds or $O(.)$
* Broadly Applicable - functional form can handle different types of data relationships (linear and nonlinear), collinearity, and other issues.  Beware "No Free Lunch" theorems
* Easy to specify - Doesn't require too many hyperparameters to achieve other goals.

## Original Cause: Neural Random Forests

![Neural Random Forest performing well on a variety of problems [@biau2019neural]](figs/nrf_nobart.png){ height=80% }

## Original Cause: ~~Neural Random Forests~~ BART

![BART outperforms NRF and related methods [@biau2019neural]](figs/nrf_all.png){ height=80% }

## BART Motivation [@chipman2010bart]

* Bayesian Additive Regression Trees
* Sum-of-trees ensemble model, similar to Random Forest [@breiman2001random] in structure
* Introduce prior on tree size to enforce ensemble of weak learners
* MCMC update procedure enables inference on model prediction and variable importance
* *Comment:* BART paper strongly favors a sum-of-*trees* model without much mention of sum-of-"other method" alternatives


## Model Ensembling [@hastie2009elements]

::: columns

:::: {.column width=30%}

* Train many small models
* Individually weak, but together effective
* Tend to resist overfitting
* May require fewer parameters than a large model

::::
:::: {.column width=70%}

![Many small models can be more effective than one large model.](figs/ensemble.png){ height=80% }

::::
:::

## Ensemble of Decision Trees: Random Forests [@breiman2001random]

::: columns

:::: {.column width=30%}

* Sample data points with replacement
* Each tree gets different sampling, build with CART
* Measure error of tree by how well it performs on unseen data (Out Of Bag error)
* Individual trees are unstable, but averaged are more general
* Overall performance tends to improve as well

::::
:::: {.column width=70%}

![Multiple trees trained with different data vote](figs/pres_rf.png){ height=77% }

::::
:::


## Aside: Markov Chain Monte Carlo

::: columns

:::: {.column width=40%}

* Sample from some difficult to simulate probability distribution, $P(x)$
* Instead, simulate Markov process that has $P(x)$ as stationary distribution
* Propose new state and accept with probability, $A(x,x')$, to enforce stationary distribution
* From random initialization, step some number of *burn-in* iterations until convergence
* NB: Successive sample states are correlated

::::
:::: {.column width=60%}

![Share of probability from $x$ moving to $x'$ is equal to share from $x'$ moving to $x$ for all pairs of states, ensuring overall distribution is constant.  Figure from [@stepanov2021math]](figs/balance.png){ height=80% }

::::
:::


$$ A(x, x') = min(1, \frac{q(x', x) P(x')}{q(x, x') P(x)})$$

## BART Basics

::: columns

:::: {.column width=30%}

* Sample prior distribution on tree parameters (e.g. depth)
* Trees *sum* to result (not average!)
* Train tree $i$ on $Y-\sum_{k \neq i} T_k$, residual of other trees
* MCMC to iteratively mutate trees to expected distribution
* Predict output by averaging over $K$ successive MCMC ensembles

::::
:::: {.column width=70%}

![Held out tree trained trained against residual](figs/pres_bart.png){ height=77% }

::::
:::

## BART MCMC Process

::: columns

:::: {.column width=30%}

* Use of residual from fixed trees *conditions* on them, i.e. Gibbs' Sampling
* Propose mutation, $T'_j$ of tree, $T_j$.
* Compute posterior of model based on tree probability and fit

::::
:::: {.column width=70%}


![Propose a new tree by splitting or merging nodes](figs/bart_mcmc.png){ height=60% }

::::
:::

$$ \alpha(T_j, T_j') = min(1, \frac{q(T_j', T_j) P(R_k|X,T_j') P(T_j')}{q(T_j, T_j') P(R_k|X,T_j) P(T_j)})$$

## BART MCMC Acceptance Ratio Explanation

$$ \alpha(T_j, T_j') = min(1, \frac{q(T_j', T_j) P(R_k|X,T_j') P(T_j')}{q(T_j, T_j') P(R_k|X,T_j) P(T_j)})$$

* $\alpha(T_j, T_j')$ - Probability of accepting new tree, $T_j'$
* $q(T_j, T_j')$ - Probability of proposing new tree, $T_j'$, from old tree, $T_j$, a.k.a. the transition probability
* $P(R_k|X,T_j')$ - Probability of target residual, $R_k$, given data, $X$, and proposed new tree, $T_j'$, a.k.a. the error probability
* $P(T_j')$ - Prior probability of proposed tree, $T_j'$, based on tree size
* $P(R_k|X,T_j') P(T_j')$ - Posterior probability of proposed tree given the data and model fit

## Hypothesis

What if we modify BART to use an ensemble of small Neural Networks instead of decision trees?  Possible benefits:

1. NNs, having been shown to produce highly accurate models in some situations [@szegedy2013ipn], may when ensembled outperform BART.
2. The BART MCMC procedure may rigorously address the neural network architecture search problem [@idrissi2016genetic] with Bayesian posteriors.
3. By carefully assigning priors to model size parameters, we can adapt computational effort to problem *difficulty* rather than number of data points or features.

Aside: Can this BART-like procedure improve models other than trees and NNs?

## Neural Networks

::: columns

:::: {.column width=30%}
* Take weighted sum of input for each node at layer $k+1$
* Apply nonlinear "activation" function
* $h_{j,k+1} = f(b_j + \sum w_{ij}h_{ik})$
* Next layer uses output of previous layer
* All calculations differentiable so we can apply gradient descent like algorithm
::::

:::: {.column width=70%}
![Neural Network as sequence of nonlinear combinations](figs/pres_nn.png){ height=80% }
::::

:::

# Methodology

## Bayesian Additive Regression Networks

::: columns

:::: {.column width=30%}
* Replace trees in BART with small NNs
* Sample from space of *networks* (architectures and weights)
* Same Gibbs' sampling, still additive
* Apply MCMC to new model proposal and acceptance
::::

:::: {.column width=70%}
![Ensemble of neural networks additively combine](figs/barn_basic.png){ height=80% }
::::

:::

$$ A(M_k, M_k') = min(1, \frac{q(M_k', M_k) P(R_k|X,M_k') P(M_k')}{q(M_k, M_k') P(R_k|X,M_k) P(M_k)})$$

## BARN Transition Probability

::: columns

:::: {.column width=30%}
* Based on model *architecture*
* All networks are simple 1 hidden layer with $m$ neurons
* Add new neuron with $q(m,m+1)=0.4$
* Remove last neuron with $q(m,m-1)=0.6$ (prefer smaller networks)
* *Retain previously learned weights!*
::::

:::: {.column width=70%}
![Proposed network adds or removes a neuron, retaining weights](figs/barn_trans.png){ height=80% }
::::

:::


## Prior Model Probability

::: columns

:::: {.column width=30%}
* Define prior model probability, $P(M_k)$ based on architecture
* Again encourage small size (want ensemble of weak/cheap models)
* Add new neuron with $p=0.4$
* Assume Poisson distribution ($\lambda =10$) on number of neurons:

$$ P(m) = \frac{\lambda^m e^{-\lambda}}{m!} $$

::::

:::: {.column width=70%}
![Poisson count probabilities (Image from [@wiki2022poisson])](figs/poisson.png){ height=80% }
::::

:::

## Error Probability

::: columns

:::: {.column width=30%}
* After proposed model architecture determined, train new NN on *training residual*
* $R_k = Y - \sum_{j \ne k} M_j(X)$
* Assume errors normally distributed
* Compute likelihood over *validation* errors

::::

:::: {.column width=70%}

$$ P(R_k |X, M_k) = \prod_{i\in  valid} \frac{1}{\sigma \sqrt{\pi}} e^{-\frac{1}{2}\left(\frac{R_k^i-M_k(x_i)}{\sigma}\right)^2}$$

::::

:::

## Put It All Together

* Fix number of networks, say $N=10$
* Initialize all networks to $M=1$ neuron and training on $R_k = \frac{Y}{N}$ (equal share)
* Gibbs' sample to propose/update one NN at a time with current residual
* Train proposed model, compute likelihood on validation data
* Accept/reject with $A$
* After burn-in period (each model has up to 100 updates), every step provides an ensemble of models, choose last/best (on validation)

## Toy Example: Three Networks

![Fix $M_1$ and $M_2$, propose change to $M_3$, $M_3'$](figs/barn_ex1.png){ height=80% }

## Toy Example: Three Networks

![Train $M_3'$ against $(x,R_3)$ and compute MCMC probabilities](figs/barn_ex2.png){ height=80% }

## Toy Example: Three Networks

![Compare $U(0,1)$ to $A(M_3, M_3')$ and update $M_3$](figs/barn_ex3.png){ height=80% }

# Results

## Data

::: columns

:::: {.column width=30%}
* Use benchmark regression problems from UCI [@dua2017uci]
* Similar to study of Neural Random Forests [@biau2019neural]
* Also created small random synthetic data set with 2 irrelevant features

::::

:::: {.column width=70%}

| Dataset | Features | Data Points |
|:----------|------:|---------:|
| boston | 13 | 506 |
| concrete | 9 | 1030 |
| crimes | 101 | 1994 |
| diabetes | 10 | 442 |
| fires | 10 | 517 |
| isotope | 1 | 700 |
| mpg | 7 | 398 |
| random | 10 | 1000 |
| wisconsin | 32 | 194 |

::::

:::

## Error Analysis

::: columns
:::: {.column width=30%}
* Batch means, compute $\phi = RMSE$ with validation data
* Typical within run variance $\leq 0.01$ run-to-run variance
* Example $\tau_{int} \approx 7 \ll 100$ samples

::::

:::: {.column width=70%}
![Typical error progression during BARN process](figs/mpg_phi.png){ height=80% }
::::

:::

## RMSE Accuracy

::: columns
:::: {.column width=25%}
* Compare to BART and equivalent size single big NN (i.e. $m = \sum m_k$)
* Compute $\bar{RMSE}(\sigma)$ over 13 independent runs
* BARN more stable and often more accurate

::::

:::: {.column width=75%}

| Dataset   |     BARN |    Big NN |      BART | OLS |
|:----------|---------:|----------:|----------:|----:|
| boston    | 4.64 ( 0.45)|  8.11 ( 4.96)|  *4.10* ( 0.56)|  4.83 (0.45) |
| concrete  | 7.32 ( 0.54)| 12.68 ( 7.99)|  *6.82* ( 0.44)| 10.60 (0.36) |
| crimes    | *0.14* ( 0.01)|  0.18 ( 0.04)|  0.15 ( 0.01)| 0.14(0.01) |
| diabetes  |54.07 ( 2.54)| 53.74 ( 2.56)| 58.72 ( 2.88)| *53.67* (2.36) |
| fires     | *55.01* (39.88)| 55.18 (39.90) | 72.28 (32.11) | 56.46 (38.68)|
| isotope   | 0.03  (<0.01)| 0.04 (0.01) |  0.03 (<0.01 ) | *0.03* (<0.01)|
| mpg       | 3.43 ( 0.40)| 71.88 (52.50)|  3.44 ( 0.45)| *3.33* (0.29) |
| random    |*11.46* ( 0.55)| 11.78 ( 0.64)| 55.41 (14.87)| *10.02* (0.34) |
| wisconsin |*33.48* ( 2.50)| 45.15 (10.37)| 35.76 ( 3.21)| 40.38(4.77) |

::::

:::


## Graphic Accuracy

![BARN results on test data always better than or comparable to previous methods](figs/pres_results.png){ height=80% }

## Isotope Dataset Comparison

::: columns
:::: {.column width=30%}
* Predict carbonate clumped isotope thermometry, $\Delta_{47}$ from temperature
* Recent study [@roman2022bayclump] suggests shows Bayesian Least Squares is effective
* BARN is comparable to these methods, and better than a single neural network and BART

::::

:::: {.column width=70%}
![BARN is competitive with other methods for modeling D47 data](figs/iso_results.png){ height=80% }
::::

:::

## Computation Time (still exploratory)

![BARN computation time appears to scale with *difficulty* not just data size](figs/time_results.png){ height=80% }

# Discussion

## Limitations

* BARN is adaptable, integrating bayesian ensembling and architecture search
    * Ensemble size and MCMC constants are hyperparameters
* RMSE often minimal for BARN, but within expected values (i.e. $\pm 2\sigma$) for other models
	* $\implies$ Better to randomly retry with BART or big NN?
* Computation time much longer for BARN (~100s) than BART/big NN/OLS (~0.1s)
	* BART directly proposes new tree, no training step required
	* Big NN is efficient for loading/running model
	* May mitigate by encoding ensemble as a big NN (zeroing cross model connection weights)
	* Also try GPU/TensorFlow implementation for speed

## Acknowledgements

* Cristian Rom${\'a}$n Palacios - additional data set and ongoing collaboration
* Kevin Lin - many Monte Carlo foundations and suggestions
* Helen Zhang - introduction to BART and related methods

# Comps Goals

## Comps Goals: Math and Machine Learning
 
* More rigorous formulation of machine learning model: Bayesian posterior, NN optimization, and inference usage
* Better motivation for BARN (automatic architecture search, broad applicability, more background research needed)
* Ensure understanding of inspirational BART paper (background and MCMC)
* Exploration and guidance for setting of hyperparameters
* (stretch) Explore convergence properties/error analysis of BARN
    * More rigorous computing of convergence metrics for MCMC
    * Any provable properties/conditions for convergence (and speed of convergence)
    * Or non-convergence
    * Explore Gelman-Rubin statistic
* (stretch) Development of Support Vector Machine for BARN
* (stretch) Develop more general neural network architecture for BARN to explore

## Comps Goals: Data Science and Implementation

* Refactor BARN code into Python module with arbitrary base model.
* Write practical tutorial with small example for users.
* (stretch) Develop R library as well.
* (stretch) Improve speed by order of magnitude by cleverly implementing ensemble of NNs.
* (stretch) Implement classification form of BARN (e.g. Probit model)
* Inclusion of additional data sets (larger scale or demonstrating some capability/problem with BARN).
* Expansion of testing metrics/procedure

## Task Schedule

![](figs/research_schedule_rot.pdf){ height=140% }

## Deliverable Schedule

| Deliverable | Date |
|:----------|------:|
| Initial Presentation | 27 Jan 2023 |
| Comps Paper Progress | 17 Mar 2023 |
| Comps Final Paper | 7 Apr 2023 |
| Professional Resume | 7 Apr 2023 |
| Personal Website | 7 Apr 2023 |
| Comps Final Presentation | 21 Apr 2023 |


## Presentation Schedule

| Presentation | Date | Time | Place |
|:----------|------:|---:|---:|
| All-committee meeting | 27 Jan 2023| 10-11am| MATH402| 
| Applied Math Student Brown Bag (comps dry run) | 31 Mar 2023| 1pm| MATH402| 
| Company Tech Talk (another dry run) | TBD Apr 2023| TBD| TBD| 
| Comprehensive Exam (tentative) | 21 Apr 2023| 9am-12pm| MATH402| 
| All-committee meeting 2 | Autumn 2023| | |
| Defense? | Mid 2024| | |

## Contact

Email: vanboxel@math.arizona.edu

Old Repo: [https://github.com/dvbuntu/barn](https://github.com/dvbuntu/barn)

Math Repo: [https://github.com/dvbuntu/barm](https://github.com/dvbuntu/barm)

Library Repo: [https://github.com/dvbuntu/barmpy](https://github.com/dvbuntu/barmpy)


## Bibliography

\footnotesize

[//]: # (Fixing highlighting_)
