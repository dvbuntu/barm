---
title: Brewing a Better Model with BARM
subtitle: Bayesian Additive Regression *Models*
author:
- Danielle Van Boxel
date: 12 Jan 2023
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
...

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

::::
:::: {.column width=30%}

![Professional Danielle](figs/danielle_prof.jpg){ height=40% }

![Cute Danielle](figs/danielle_cute.jpg){ height=25% }

::::
:::

# Comps Plan

## Division of Advising

Bayesian Additive Regression Models, a generalization of BART [@chipman2010bart] and BARN [@vanboxel2022barn].  I will demonstrate the utility of replacing decision trees (as from BART) or neural networks (as from BARN) with broader classes of machine learning models.



::: columns

:::: {.column width=50%}

* Xueying Tang - math and machine learning
* BART background
* MCMC details (esp, error analysis)
* Machine learning algorithm fundamentals (SVM, etc)

::::
:::: {.column width=50%}

* Cristian Rom${\'a}$n Palacios - data science and implementation
* Code development
* Finding appropriate data sets
* Ensuring utility for researchers

::::
:::


## Comps Goals: Math and Machine Learning
 
* Development of Support Vector Machine for BARM
    * Implement SVM manually first to better understand
* (stretch) Develop more general neural network architecture for BARM to explore
* Ensure understanding of inspirational BART paper (background and MCMC)
* (stretch) Explore convergence properties of BARM
    * More rigorous computing of convergence metrics for MCMC
    * Any provable properties/conditions for convergence (and speed of convergence)
    * Or non-convergence

## Comps Goals: Data Science and Implementation

* Refactor BARN code into Python module with arbitrary base model.
* (stretch) Develop R library as well.
* (stretch) Improve speed by order of magnitude by cleverly implementing ensemble of NNs.
* Inclusion of additional data sets (larger scale or demonstrating some capability/problem with BARM).
* Revision of testing metrics/procedure

# Administrivia

## Scheduling

* 27 Jan 2023, 10-11am, MATH402, All-committee meeting
* 31 Mar 2023, 1pm, MATH402, Applied Math Student Brown Bag (comps dry run)
* Early Apr 2023, TBD, TBD, Company Tech Talk (another dry run)
* 21 Apr 2023, 9am-12pm, MATH402, Comprehensive Exam (tentative)
* Mid Autumn 2023, All-committee meeting 2
* Late Spring/Summer 2024, Defense?

## Links

Email: vanboxel@math.arizona.edu

Old Repo: [https://github.com/dvbuntu/barn](https://github.com/dvbuntu/barn)

Math Repo: [https://github.com/dvbuntu/barm](https://github.com/dvbuntu/barm)

Library Repo: [https://github.com/dvbuntu/barmpy](https://github.com/dvbuntu/barmpy)

## Bibliography

\footnotesize

[//]: # (Fixing highlighting_)
