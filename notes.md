# 12 Jan 2023

Meet with Xueying to discuss math/ml goals for research.

She advised focusing on mathematical formulation (especially Bayesian method) rather than trying this with SVMs.  In particular, write down explicit forms for 

1. Bayesian posterior (i.e. error likelihood times prior prob, but more explicit)
2. Loss function for optimization portion (i.e. NN training)

Also, bear in mind that I should better motivate *why* I'm taking this new ML approach.  This should emphasize automatic architecture search as well as the generalizability of the method (i.e. works on a variety of data problems).

Another important avenue of research is exploring guidance on how to change the hyperparameters.  Things like the transition probability, prior distribution (params and dist itself), and number of models in the ensemble.  At a minimum, I should explore how changing them changes the results of the analysis.

Other topics discussed were possibility of using Gelman-Rubin stat for error analysis, and generally updating the paper to use a more explicit mathematical style.

Goal for 26 Jan 2023: Formalize above goals as part of all-committee meeting dry run.
