---
title: BARN Data Science Library Update
author:
- Danielle Van Boxel
date: 16 Feb 2023
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

## Comps Goals: Data Science and Implementation

* *Refactor BARN code into Python module with arbitrary base model.*
* *Write practical tutorial with small example for users.*
* (stretch) Develop R library as well.
* (stretch) Improve speed by order of magnitude by cleverly implementing ensemble of NNs.
* Inclusion of additional data sets (larger scale or demonstrating some capability/problem with BARN).
* Expansion of testing metrics/procedure

# Recent Progress

## BARN Improvements

::: columns
:::: {.column width=30%}

* Callable/Installable as a `pip` library (not uploaded to [PyPi](pypi.org))
* Unit tests added for key methods
    * [NN Tests](https://github.com/dvbuntu/barmpy/blob/main/tests/test_nn.py)
    * [BARN Tests](https://github.com/dvbuntu/barmpy/blob/main/tests/test_barn.py)
* Some cleanup of `requirements.txt` and `setup.py`
* Prototype control over number of training epochs (see right)

::::
:::: {.column width=70%}

\scriptsize
```python
class NN(object):
	...
    def __init__(self, num_nodes=10,weight_donor=None,l=10, lr=0.01, r=None,
            epochs=20):
		...
        ## can set max_iter to set max_epochs
        self.model = sknn.MLPRegressor([num_nodes],learning_rate_init=lr,random_state=r,
                max_iter=epochs)
		...
    def train(self, X, Y):
        '''Train network from current position with given data'''
		# can't set num epochs here
        self.model.fit(X,Y)

class BARN(object):
	...
    def setup_nets(self, l=10, lr=0.01, epochs=10):
		# Specify num epochs here
        self.cyberspace = [NN(1, l=l, lr=lr, epochs=epochs) for i in range(self.num_nets)]
	...
    def train(self, Xtr, Ytr, Xva=None, Yva=None, Xte=None, Yte=None, total_iters=10):
		...
                Np.train(Xtr,Rtr) # Too late to specify epochs!
```
\normalsize
::::
:::

## Docs Updates

* Updated docstrings in BARN for more general usability.
* Sphinx build completed for basic BARN usage, `autoclass` pulls in methods and docstrings
* Documentation live at [https://dvbuntu.github.io/barmpy/](https://dvbuntu.github.io/barmpy/).
* Added build and docs badges to [BARMPy Repo](https://github.com/dvbuntu/barmpy), currently passing!

## Other Work

* Academic website done, available at [https://dvbuntu.github.io/](https://dvbuntu.github.io/)
* Resume/CV mostly done, still soliciting feedback/making revisions.
* Working on full BARN ensemble bayesian posterior, adapting BART methodology (some difficulties)

# Plans

## Schedule

![Basic repo in place, 16 Feb 23](figs/research_schedule_rot.pdf){ height=140% }

## Next Time (16 Feb 2023)

* Start drafting tutorial
	* Explain BARN fundamentals
	* Select small data problem, possibly randomly generated
	* Show basic `BARN` call
	* (stretch) Include visualization/non-BARN comparison
* Continue polishing library
	* Separate script for running meta-analysis (old `simple_nn_mcmc.py`)? Or include as example
	* Expand documentation to minimally complete
* Start analyzing new data sets
	* Review BART testing procedure (obtain their data?)
	* (stretch) Need to refactor testing code (`simple_nn_mcmc.py` as above)
	* (stretch) Generate well-understood synthetic data sets
* (stretch) Start to sketch/setup hyperparameter exploration (look for general Python libs to handle for comparing against other methods and including cross-validation)

## Links

Email: vanboxel@math.arizona.edu

Old Repo: [https://github.com/dvbuntu/barn](https://github.com/dvbuntu/barn)

Math Repo: [https://github.com/dvbuntu/barm](https://github.com/dvbuntu/barm)

Library Repo: [https://github.com/dvbuntu/barmpy](https://github.com/dvbuntu/barmpy)

## Bibliography

\footnotesize

[//]: # (Fixing highlighting_)
