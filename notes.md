# 12 Jan 2023

Meet with Xueying to discuss math/ml goals for research.

She advised focusing on mathematical formulation (especially Bayesian method) rather than trying this with SVMs.  In particular, write down explicit forms for 

1. Bayesian posterior (i.e. error likelihood times prior prob, but more explicit)
2. Loss function for optimization portion (i.e. NN training)

Also, bear in mind that I should better motivate *why* I'm taking this new ML approach.  This should emphasize automatic architecture search as well as the generalizability of the method (i.e. works on a variety of data problems or running as initial baselining).

Another important avenue of research is exploring guidance on how to change the hyperparameters.  Things like the transition probability, prior distribution (params and dist itself), and number of models in the ensemble.  At a minimum, I should explore how changing them changes the results of the analysis.

Other topics discussed were possibility of using Gelman-Rubin stat for error analysis, and generally updating the paper to use a more explicit mathematical style.

Goal for 26 Jan 2023: Formalize above goals as part of all-committee meeting dry run.

# 19 Jan 2023

Meet with Cristian

* Include schedule of tasks in presentation to committee next week
* More test metrics (other than RMSE, maybe focus on a single data set this way)
* Why use BARN?  Work on motivation for what it's good at/what problem it addresses
* Include a small tutorial with the library component

Cristian said he would look for data for me.

# 23 Jan 2023

Cristian suggested some updates to my presentation.

Key observation: I've been misusing/misreading BART.  The inference (i.e. predicted value) really should be the average of N iterations of the MCMC procedure, not just "last/best ensemble we stopped on".  See equation 18 of BART paper.  I've been using $K = 1$.  Having many observations also allows us to see some distribution on the predicted result (not clear how to distinguish error on specific prediction vs error on expected result for this construction).

# 26 Jan 2023

Dry run for Cristian and Xueying.

Several helpful comments on content to include in presentation (mostly things I had removed earlier :) )

Also suggestion that for point inference, $K=1$ with BART (or BARN) would be appropriate.  If we want an uncertainty estimate, that's when we can pick a large $K$.  Note that this is model agnostic!  It only depends on the underlying MCMC procedure.

Also, I should consider making many synthetic datasets with known functions + some error.  Then we can see what kinds of functions we can recover.  Ideally, fix the number of data points or number of features (probably former).

# 27 Jan 2023

All committee meeting.  Good feedback around, and agreement that concept is sound and interesting.  Suggestions for focus areas:

* Explore hyperparameter space (e.g. try more models with smaller nets, maybe lambda=2)
* Explain bayes prior/posterior in more detail
* Can parallelization improve speed?  Maybe, maybe not.  There is a paper on Parallel BART.

# 2 Feb 2023

Meeting with Cristian, good feedback on additional library options

* Include continuous integration via github actions/travis.yml (see old BARN repo)
* Think carefully about synthetic datasets to generate for testing
* Make visualization of histogram plots showing both prior neuron count dist vs actual (i.e. posterior) histogram plot

# 4 Feb 2023

Work on ML formulation.

Making posterior explicit and showing weights have no prior/cancels out anyway.

sum-of-NNs slightly more explicit.

Still significant work to do integrating this into the comps manuscript.

Possible proof/example: Show that an ensemble of NN with final sum can be written as big NN with restricted weights.  Approach by enforcing zero weights on what would be cross-network connections and enforce final sum weights to be 1.  

$w_{(i),(j)} = 0$, where $(i)$ is a neuron belonging to model $i$, and $(j)$ is neuron belonging to model $j \neq i$.

# 9 Feb 2023

Meeting with Xueying.  Clarification on need to specify bayesian posterior for full ensemble.  Then I can show that conditioning on N-1 nets is equivalent to using the residual.  Follow the description/explanation laid out in BART.

More explicitly state that $P(w_j|M_j)$ is flat.  This technically causes issues b/c it doesn't integrate to 1 on a continuous spectrum, but in practice, it's not a big deal.  As a practical matter, one could specify a normal with 0 mean and very large variance.  But still, should be mentioned.  Or maybe a normal distribution on weights is a good idea?

$P(w_j|M_j) \times P(M_j)

# 10 Feb 2023

Work on entire ensemble posterior, see bart.

Big overall posterior:
$$
P((M_1, w_1), \ldots, (M_m, w_m),\sigma|Y,X) = \frac{P(Y| (M_1, w_1), \ldots, (M_m, w_m), X, \sigma)P((M_1, w_1), \ldots, (M_m, w_m),\sigma)}{P(Y)} \\
$$

Suppose individual priors are independent.

$$
\begin{aligned}
P((M_1, w_1), \ldots, (M_m, w_m),\sigma) &= P(\sigma) \prod_j P(M_j,w_j)\\
P((M_1, w_1), \ldots, (M_m, w_m),\sigma) &= P(\sigma) \prod_j P(w_j|M_j)P(M_j)\\
P(w_j|M_j) &= \prod_i P(w_{ij} | M_j)
\end{aligned}
$$

$w_{ij}$ is $i$th weight in model $j$.  

$P(w_{ij}|M_j) = P(w_{ij})$?  Or simply $P(w_{ij}|M_j) = P(w_{ij}'|M_j)$?  Not quite the same as no prior on weights...

$P(w_{ij}|M_j} \sim P(\mu_{ij}|T_j)$ from BART.  They use normal, with wide enough variation such that summing $m$ of these terminal nodes means you very likely to include $(y_{min},y_{max})$.

But harder with an NN, distribution of output is not so clearcut.  Tried looking up typical output distribution, but no dice.

$$
g_j = \sum_{k \in \text{second layer}} w_{kj} h_k(X) = \sum_{k \in \text{second layer}} w_{kj} h(\sum_{i \in \text{first layer}} x_{ij} w_{ij})
$$

So output depends on input more directly.  Might be able to sum over these.

If we assume $h_k(X) \sim N(0,1)$, as when doing batch normalization, then we want $w_{kj} \sim N(\mu_k, \sigma^2_k)$!

But the number of neurons varies!  Give each net an equal "share" of the variance/mean?  Is it ok if the weight prior shifted in this way?

Need a prior on $\sigma$ as well, BART uses $\chi^2$, and they est with an OLS.  Just measure standard deviation of residuals.

$$
P(M_j, w_j | M_{(j)},w_{(j)}, \sigma, Y,X) = P(M_j, w_j| R_j, \sigma)
$$

$$
R_j = Y - \sum_{i\ne j} g(X, M_i, w_i)
$$

How to *prove* that conditioning on $R_j$ is equivalent to conditioning on all other components of ensemble?  In BART this is handwaved.  It is clearly true.

Show some equivalence class of $(M_{(j)}, w_{(j)})$ that all give the same $R_j$.  Note that $R_j$ is sufficient stat (probably minimal suff) for dist of  $(M_{(j)}, w_{(j)})$.

## Transition changes ;)

Transition prob ignores weights:

$T((M_j',w_j'), (M_j, w_j)) = T((M_j',w_j"), (M_j, w_j))$

## Back to BART

They do draw of the tree arch (MCMC), then a quick random sample of terminal node values from normal (a prior).  I guess I really could do that sample on weights as well, but seems crazy.

# 12 Feb 2023

Yesterday updated BARN code so it's properly callable as a library.  Added some unit tests for relevant methods, and implemented github actions to run said tests.

Today, put together basic BARN page to be part of sphinx documentation.  Had to fiddle with settings a fair bit to actually output the code blocks and autodoc components (`automodule` vs `autoclass`).  Also add some testing badges builtin to github.

# 16 Feb 2023

Discussion with Cristian about updating BARN code.

* Test how no. epochs changes run time, if much at all.
* If possible, bold name in papers in CV
* for tutorial, RMarkdown, or jupyter notebook, or likely just markdown

## code updates

* Add comments to unittests, and clarify origin of constants
* different test for log_likelihood
* datatype checking in unittests, make sure it fails as expected
    * similar test if give negative priors that bad things happen/catch

## doc updates

* change "simple linear" to "ordinary linear" (double check this is what I mean, maybe just remove adjective)
* test the fitting process on a small random dataset
* Source links in the docs don't actually work, hmm

# 19 Feb 2023

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

# 23 Feb 2023

Meeting with Xueying.

Bayesian posterior looks good.  Should add remark to paper *briefly* stating this and that we have a full bayes setup.  Also comment on flat weight prior isn't fully appropriate but shouldn't hurt.  Or we can try with a generic normal prior, or uniform on a wide interval.

Hmm, prior on weights is a form of regularization, isn't it?

Don't need full sufficiency to be able to use $R_j$ in place of others.  It's really far more of just a substitution.  Maybe mention that it works this way, though

Maybe fix slide 9, no power of K?  double check this, if even needed at all.

# 25 Feb 2023

Rebuilding comps paper from manuscript.  Add notes about sections/details to add like schedule and things from last meeting.

# 11 Mar 2023

(previously worked on tutorial and paper updates)

Put together BARN motivation better now.  Fill in remaining comments about data science, prior and bits.  Leaving comments in future work to get feedback.
