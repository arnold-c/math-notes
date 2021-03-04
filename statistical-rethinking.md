---
title: "Statistical Rethinking Notes"
subtitle: "2nd Edition"
author: "Callum Arnold"
date: "2021-03-04"
output: 
  html_document:
    toc: TRUE
    toc_float: TRUE
    number_sections: TRUE
    keep_md: TRUE
    code_folding: hide
---

This is a notebook that contains notes and code generated whilst going through
Richard McElreath's Statistical Rethinking book (2nd ed). Each chapter's comments
and answers to the questions will be copied and posted on my website.

# The Golem of Prague

Chapter one primarily summarizes the motivations behind the book, and a shift
away from traditional frequentist statistical analysis that aim to falsify a 
null hypothesis using a statistical model selected from a flowchart, to a 
Bayesian framework that focuses on model comparison and evaluation, multilevel 
models as the norm, and evaluating the different process models that may link
your statistical model to your hypotheses.

# Small Worlds and Large World

- Can think of models as small worlds vs large worlds:
  - They are at best a small version of reality
  - Often they fail to test/be able to test for a number of errors and possibilities
  - Should always seek to ensure the model performs under favourable assumptions
- Bayesian models are well suited as they are designed to account for this
  - Particularly when frequently tested against reality and updated
  
## The garden of forking data
- Bayesian inferece is primarily just counting and comparing possibilities
  - All possible outcomes (conjectures) are considered, and options "pruned" as 
  more data is considered that do not provide evidence for these outcomes
- Example:
  - Count all the ways the observed data can be observed for each conjecture
  - Consider the relative plausibilities of each conjecture
    - Take counts from the first round, and instead of starting again, use as
    your *prior* for each additional data point
      - Count the number of ways each conjecture can produce the individual data
      point, and multiply this value by the *prior* count
      - Requires that new data is logically independent of previous, otherwise
      recalculate from the start
      - Doesn't require new data to be the same type
    - Convert to probabilities
      - Counts become harder to manage as they increase and we're only interested
      in the relative size of the counts
- Important components of Bayesian formula:
  - Parameter value
    - Conjectured proportion
  - Likelihood
    - Relative number of ways the parameter value can produce the data
  - Prior probability
    - Initial probability of seeing the parameter value
  - Posterior probability
    - Probability of seeing the parameter value after accounting for the new data
    
## Building a model
- Basic process of Bayesian modelling
  1. Determine possible ways data can be observed
    - Often need to describe underlying reality as well as sampling process
    - Be specific with story and resolve ambiguities
  2. Update model by adding data
    - Plausibility of each value of *p*, the estimate of the true value of the 
    parameter, is updated as more data is added
    - Because it it iterative, you can calculate the distributions both forwards
    and backwards given all the data points
    - Means that no minimum sample size is required to be valid (just the 
    inferences will be less clear as wider distribution), but small sample sizes
    are sensitive to priors
  3. Evaluate model and revise
    - Inferences are conditional to the model, so may be incredibly confident
    in model A, but very different with a different model
    - Need to consider the model assumptions, e.g. order of data doesn't matter,
    so that we can consider validity of the model's inferences (which aren't 
    affected by data it doesn't consider)
    - These checks confirm the model's adequacy for a specific purpose

## Components of the model
- Need to explicitly name the variables and provide definitions
- Variables
  - First variable is target of interest (often a proportion)
  - Unobserved variables often called parameters
  - Observed variables are the data
- Definitions
  - Build a model that relates a variable to the others
  - For each parameter, define the probability of seeing each observed variable
  values (data point)
    - Then, for each parameter, define the prior plausibility of each of these 
    values
      - Each specific value of *p* corresponds to a specific plausibility of the
      data
      - We define the plausibility with a distribution (sometimes called the 
      likelihood, but not technically correct in a Bayesian sense)
      - When there are only 2 possible outcomes, we use the binomial distribution
      - For 6 successes in 9 bernoulli trials with prior plausibility 0.5 (*p*), 
      the plausibility = 0.1640625
    - Each parameter must have a prior plausibility
      - This becomes a *prior* when you have previous estimates
      - Can specify and test multiple priors, like with other model components
  - Binomial distribution example
    - $W \sim \text{Binomial}(N, p):  \text{with } p \sim \text{Uniform}(0, 1)$
      - Uniform (flat) prior plausibility
    
## Making the model go
- Model outputs that posterior distribution that is the probability of the 
  parameters conditional on the data (and the model)
- The joint probability of the data $W$ and $L$ with probability $p$ is:

\begin{aligned}
  \text{Pr}(W, L, p) &= \text{Pr}(W, L \mid p)  \times \text{Pr}(p) \\
  &= \text{Pr}(p \mid W, L)  \times \text{Pr}(W, L) \\
  
  &\therefore \\
  \text{Pr}(p \mid W, L)  \times \text{Pr}(W, L) &= \text{Pr}(W, L \mid p)  \times \text{Pr}(p) \\
  \text{Pr}(p \mid W, L) &= \frac{\text{Pr}(W, L \mid p)  \times \text{Pr}(p)}{\text{Pr}(W, L)}
\end{aligned}

- Here $\text{Pr}(W, L)$ is the *average* probability of data (over the prior)
  - AKA the *marginal likelihood*

\begin{aligned}
  \text{Pr}(W, L) &= \text{E}\left(\text{Pr}(W, L \mid p)\right) \\
  &= \int \text{Pr}(W, L \mid p)  \times \text{Pr}(p) dp
\end{aligned}

- Most importantly 
$\text{Pr}(W, L \mid p) \propto  \text{Pr}(W, L \mid p) \times \text{Pr}(p)$
- Often it is not possible to *condition* the prior on the data, so much use
numerical approximations each with different limitations and assumptions
  - Grid approximation
    - Converts continuous parameters to finite grids of values
    - Doesn't scale well with increasing number of parameters
  - Quadratic approximation
  - MCMC
