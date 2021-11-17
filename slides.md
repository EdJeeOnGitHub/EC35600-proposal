---
# try also 'default' to start simple
theme: seriph 
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: 'text-center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# persist drawings in exports and build
drawings:
  persist: false
defaults:
  layout: 'center'
# Paper Discussion: This is a 10-minute discussion designed to simulate what a discussant would 85 
# present at an economics conference.  Students may choose any of the non-starred papers on the 86 
#  5 
# syllabus.  Students  choosing  other  papers  should  confirm  their  choice  to  us.    The  presentation 87 
# should spend 3-4 minutes summarizing the paper and its empirical strategy.  The next 3-4 minutes 88 
# of the presentation should highlight the paper’s contribution, any criticisms, and avenues for 89 
# further work.  The remaining 2 minutes should be reserved for discussion and questions.
---

# Synthesising Observational and Experimental Evidence 


Ed Jee
<!-- <div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    Press Space for next page <carbon:arrow-right class="inline"/>
  </span>
</div> -->

<!-- <div class="abs-br m-6 flex gap-2">
  <button @click="$slidev.nav.openInEditor()" title="Open in Editor" class="text-xl icon-btn opacity-50 !border-none !hover:text-white">
    <carbon:edit />
  </button>
  <a href="https://github.com/slidevjs/slidev" target="_blank" alt="GitHub"
    class="text-xl icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div> -->

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---

# Motivation

- Whilst conducting field experiments researchers often have access to additional, non-experimental, data sources or samples. 
  - This can be used to inform pilots or as baseline covariates.

But observational data can also be used to inform our estimates of 
treatment effects beyond simple covariate adjustment in our experimental sample.


- Combining evidence is attractive for multiple reasons:
  - increase power cheaply.
  - discarding data seems wrong from a scientific/Bayesian angle.



---

# Challenges


Depending on the assumptions we're prepared to make this can be very easy, or a little more difficult:

<v-clicks>

- Homogeneous treatment effects + selection on observables
  - pool observational + experimental together. [LaLonde, 1986](http://www.jstor.org/stable/1806062) suggests this probably isn't a great idea.
- Rank preserving endogeneity
  - "Bigger causal effects imply bigger bias" [Peysakhovich and Lada, 2016](https://arxiv.org/abs/1611.02385)
- Primary outcome only measured in observational sample, "un-selected" RCTs
  - [Athey, Chetty, and Imbens, 2020](https://arxiv.org/abs/2006.09676)
- Internal selection bias observationally, site-selection into RCTs
  - "Mutual Debiasing" [Gechter and Meager, 2021](https://www.personal.psu.edu/mdg5396/MGRM_Combining_Experimental_and_Observational_Studies.pdf)

</v-clicks>


<v-click>

The setting I consider is most similar to Gechter and Meager.

</v-click>


---

# Context

- Suppose we observe the universe of data in a country regarding an outcome $Y_i$ and treatment $T_i$.

- Due to implementation constraints can only randomise encouragement $Z_i$ for treatment $T_i$ in certain regions.
  - Implementing partner/researcher has preferences over experimental site that may be correlated to treatment effects. 
  - i.e. decision $D_i = \{e, o\}$ whether an individual is assigned to experimental or observational study is potentially endogenous. 


Akin to Gechter and Meager (GM) but with only one study; full micro-data; and, 
critically, institutional knowledge of study design assignment.
---

# The Problem
Using the notation of GM, we observe a result $R$, with switching equation:

$$
\begin{align*}
R &= \mathbb{I}\{D = e\}\underbrace{R^e}_{TE} + (1 - \mathbb{I}\{D = e\})\underbrace{R^o}_{TE + SB}
\end{align*}
$$


- Fundamental problem of causal inference, we never observe the pair: ($R^e, R^o$).

- $\hat{R}^o$ is contaminated with "internal" selection bias whilst $\hat{R}^e$ 
is affected by site selection bias. 

- If we can find some way of debiasing one study we can mutually debias the other.

---

# Internal Selection Bias


$$
\begin{align*}
E[Y | T = 1] - E[Y | T = 0] &= E[Y_1 - Y_0 | T = 1] + \underbrace{E[Y_0 | T = 1] - E[Y_0 | T = 0]}_\text{selection bias}
\end{align*}
$$
<v-clicks>

- Internal selection bias arises in observational studies because individuals 
taking treatment are fundamentally different to units that decide not to take treatment.

- But an RCT with partial compliance nests a __hypothetical observational study__ and we can
back out the two bias terms.

- $E[Y_0 | T = 0]$  ⟶ corresponds to the _never takers_ in an experiment  ($E[Y | \underbrace{T = 0, Z = 1}_{nt}]$).


- $E[Y_0 | T = 1]$ ⟶ the untreated outcome for a treated unit. This is just the 
untreated _complier_ mean.


- Whilst we don't observe this directly we can back it out since $p_c, p_n, E[Y | nt]$ are all known and 
makeup the control group.


</v-clicks>

---

# Internal Selection Bias II


We've uncovered the internal selection bias for a _hypothetical_ observational 
study, given our study is experimental.

- We have $\{R^e | R^o = r^o, D = e\}$

- But we want $\{R^e | R^o = r^o, D = o\}$
    - The hypothetical experimental study, given our study is observational.


This is a classic Heckman sample selection problem - we only observe:

$$
(\{R^e | R^o, D = o\}, \{R^o | D = e\})
$$


In the GM setting we need to find some instrument that shifts research design choice, $D$, _but is 
independent of treatment effects_. 


---

# A Solution


However, in this context we have institutional knowledge of the site selection 
assignment rule.

- Given rich micro-data we can recast the problem as whether _individuals_ 
are assigned to observational or experimental studies.

- Individuals just ineligible for the experimental study are a valid counterfactual 
for individuals just eligible for the RCT.

- In neighbourhood of cut-off $\{R^e | R^o = r^o , D = e\} = \{R^e | R^o = r^o, D = o\}$ 
  - i.e. $D$ as good as randomly assigned.

We can debias observational studies by inferring their hypothetical experimental 
result.


---


# Next Steps

- Simulations.
- Figuring out how much more we can do with knowledge of assignment mechanism.
- Extrapolate away from cutoff. 
