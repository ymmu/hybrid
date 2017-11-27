---
layout:            post
title:             "17/11/24- TIL - Support vector machines(SVMs)"
enutitle:          "17/11/24- TIL - Support vector machines(SVMs)"
category:          Machine learning
author:            myohyun
tags:              Machine learning
---
<script src='https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.2/MathJax.js?config=TeX-MML-AM_CHTML'></script>

#용어정리


**duality**:
If {\displaystyle (x_{1},x_{2},....,x_{n})} (x_{1},x_{2},....,x_{n}) is a feasible solution for the primal minimization linear program and {\displaystyle (y_{1},y_{2},....,y_{m})} (y_{1},y_{2},....,y_{m}) is a feasible solution for the dual maximization linear program, then the weak duality theorem can be stated as {\displaystyle \sum _{i=1}^{m}b_{i}y_{i}\leq \sum _{j=1}^{n}c_{j}x_{j}} \sum _{{i=1}}^{m}b_{i}y_{i}\leq \sum _{{j=1}}^{n}c_{j}x_{j}, where {\displaystyle c_{j}} c_{j} and {\displaystyle b_{i}}  b_i  are the coefficients of the respective objective functions.

Generalizations:
More generally, if {\displaystyle x} x is a feasible solution for the primal minimization problem and {\displaystyle y} y is a feasible solution for the dual maximization problem, then weak duality implies {\displaystyle g(y)\leq f(x)} g(y)\leq f(x) where {\displaystyle f} f and {\displaystyle g} g are the objective functions for the primal and dual problems respectively.
