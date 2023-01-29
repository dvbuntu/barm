---
title: BARN Data Science Library Update
author:
- Danielle Van Boxel
date: 02 Feb 2023
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

# Recent Progress

## Raising a Skeleton

* Want to include `pip`-installable module, testing, docs, and examples.
* Tested module skeleton libs: `cookiecutter` and PyScaffold
    * Both introduce too much boilerplate to keep track of.
* Found [BartPy](https://github.com/JakeColtman/bartpy/), a pure Python implementation of BART.
    * Has structure I'm looking for, adapted to `barmpy`

## `barmpy` skeleton

* Copied old `barn.py` to `./barmpy/barn.py`
* Excluded `simple_nn_mcmc.py` as that's the overall data testing procedure
* Basic set of `requirements.txt`, `setup.py`, etc
* Unit test basics in place with `tests/test_basic.py`, but not functional
* Documentation via Sphinx, skeleton in `docs` and [online](https://dvbuntu.github.io/barmpy/) (takes some manual generation each change to docs source)

## Other Work

* Drafted academic website, available at [https://dvbuntu.github.io/](https://dvbuntu.github.io/), still adding publications
* Reviewed old resume/CV; need to update since 2019 and make clean for wider availaability
* Started clarifying ML formulation, added and revised `vanboxel_comps.tex` to [BARM repo](https://github.com/dvbuntu/barm)

# Plans

## Schedule

![Skeleton complete, 2/2/23](figs/research_schedule_rot.pdf){ height=140% }

## Next Time (16 Feb 2023)

* Add to skeleton such basic BARN calls are possible
* One example unit test in place with BARN
* One example documentation page showing reference to BARN code
* (stretch) Add control of number of epochs via `max_iter` parameter of `MLPRegressor` (defaults to 200, which is probably too many)
* (stretch) Start to sketch/setup hyperparameter exploration (look for general Python libs to handle for comparing against other methods and including cross-validation)

## Links

Email: vanboxel@math.arizona.edu

Old Repo: [https://github.com/dvbuntu/barn](https://github.com/dvbuntu/barn)

Math Repo: [https://github.com/dvbuntu/barm](https://github.com/dvbuntu/barm)

Library Repo: [https://github.com/dvbuntu/barmpy](https://github.com/dvbuntu/barmpy)

## Bibliography

\footnotesize

[//]: # (Fixing highlighting_)
