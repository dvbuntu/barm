---
title: BARNstorm and Transform
subtitle: Bayesian Additive Regression Networks
author:
- Danielle Van Boxel
date: 2 Mar 2023
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

## Prior on $\sigma$

Currently I implicitly use flat prior on $\sigma$ (closely associated with error likelihood).  BART builds prior of simple OLS model to estimate $\hat{\sigma}$ and applies heuristics to select broad range of conjugate prior from inverse $\chi^2$ distribution.

$$
\sigma^2 \sim \nu \lambda / \chi^2_\nu
$$

Intution: Rather than purely optimizing for smaller residuals, this prior helps prevent overfitting.  This isn't mentioned in BART paper as best I can see.

## Manuscript Updates

* Able to recompile! :)
* Bayes background
* Brief remark on how BARN fits into fully Bayesian framework
* Outline of Dissertation research goals/schedule (much TBD)
* Various other comments/minor tweaks

## Other Work

* Accepted invitation to be *on panel* at Women in Data Science Conference 2023, April 20.  In addition to giving talk on BART.  Busy day!

# Plans

## Schedule

![Skeleton complete, 2/2/23](figs/research_schedule_rot.pdf){ height=140% }

## Schedule Slippage

* BARN motivation clarifications (architecture search, etc) (2 week)
* Rigorous NN optimization (3 weeks) 
* Hyperparameter search (nominally starting now, but may be post-comps)
* New data analysis (2 week, may be post-comps)

## Next Time (23 Mar 2023?)

* Fully integrate Bayes comments into paper.
    * Include comment about flat prior
* Draft/cite motivation BARN (arch search, general applicability, where do other methods fail, what does BART add to trees)
* Update comps draft will be sent to committee on 17 Mar.
* Begin preparation of final presentation (dry run at AM Student Brown Bag on 31 Mar)

## Links

Email: vanboxel@math.arizona.edu

Old Repo: [https://github.com/dvbuntu/barn](https://github.com/dvbuntu/barn)

Math Repo: [https://github.com/dvbuntu/barm](https://github.com/dvbuntu/barm)

Library Repo: [https://github.com/dvbuntu/barmpy](https://github.com/dvbuntu/barmpy)

## Bibliography

\footnotesize

[//]: # (Fixing highlighting_)
