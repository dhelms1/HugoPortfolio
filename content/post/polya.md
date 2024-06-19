---
title: "Polya's Distribution (Beta-Binomial) Parameter Estimation"
date: 2023-12-15
slug: polya
hero: "/images/polya_img/banner.jpg"
excerpt: "This project deals with estimating the parameters for the Polya’s distribution, specifically the: Fisher Information Matrix, Cramer-Rao Lower Bound, Maximum Likelihood Estimator, and Method of Moments." 
authors:
  - Hugo Authors
math: mathjax
---

This project deals with estimation of the parameters of the Polya’s distribution. The PMF of the observations $x_1, \ldots , x_m$ is given by:
$$ p(x_1, x_2, \ldots, x_m) = \prod_{i=1}^{m} \Biggl( \frac{n(x_i)!}{\prod_{k} n_k(x_i)!} \frac{\Gamma(\sum_k \alpha_k)}{\Gamma(n(x_i) + \sum_k \alpha_k)} \prod_k \frac{\Gamma(n_k(x_i) + \alpha_k)}{\Gamma(\alpha_k)} \Biggl) $$

For our problem, we focus on the beta binomial model, with $k \in \{1, 2\}$.

## Defining parameter vector $\theta$.
In general form: $ \theta = [\alpha_1, \alpha_2, \ldots, \alpha_k]^T$

Beta binomial form: $\theta = [\alpha_1, \alpha_2]^T$

## Compute the FIM and CRLB.
For our problem, we are given the following assumptions:
- $m = 20$
- $n(x_i) = 20$ for $1 \leq i \leq m$
- $k \in \{1, 2\}$

Note: since $k \in \{1, 2\}$, then $\sum_k \alpha_k = \alpha_1 + \alpha_2$

{{< math >}}
\begin{align*}
        p(x_1, \ldots, x_m) &= \prod_{i=1}^{m} \Biggl( \frac{n(x_i)!}{\prod_{k} n_k(x_i)!} \frac{\Gamma(\sum_k \alpha_k)}{\Gamma(n(x_i) + \sum_k \alpha_k)} \prod_k \frac{\Gamma(n_k(x_i) + \alpha_k)}{\Gamma(\alpha_k)} \Biggl) \\
        \log (p(x_1, \ldots, x_m)) &= \log \Biggl( \prod_{i=1}^{m} \Biggl( \frac{n(x_i)!}{\prod_{k} n_k(x_i)!} \frac{\Gamma(\sum_k \alpha_k)}{\Gamma(n(x_i) + \sum_k \alpha_k)} \prod_k \frac{\Gamma(n_k(x_i) + \alpha_k)}{\Gamma(\alpha_k)} \Biggl)\Biggl)\\
        &= \sum_{i=1}^{m} \log \Biggl( \frac{n(x_i)!}{\prod_{k} n_k(x_i)!} \frac{\Gamma(\sum_k \alpha_k)}{\Gamma(n(x_i) + \sum_k \alpha_k)} \prod_k \frac{\Gamma(n_k(x_i) + \alpha_k)}{\Gamma(\alpha_k)} \Biggl) \\
        &= \sum_{i=1}^{m} \log \Biggl( \frac{n(x_i)!}{\prod_{k} n_k(x_i)!} \Biggl) + \log \Biggl( \frac{\Gamma(\sum_k \alpha_k)}{\Gamma(n(x_i) + \sum_k \alpha_k)} \Biggl) + \log \Biggl( \prod_k \frac{\Gamma(n_k(x_i) + \alpha_k)}{\Gamma(\alpha_k)} \Biggl) \\
        &= \sum_{i=1}^{m} \log \Biggl( \frac{n(x_i)!}{\prod_{k} n_k(x_i)!} \Biggl) + \log \Biggl( \frac{\Gamma(\alpha_1 + \alpha_2)}{\Gamma(20 + \alpha_1 + \alpha_2)} \Biggl) + \log \Biggl( \frac{\Gamma(n_1(x_i) + \alpha_1)\Gamma(n_2(x_i) + \alpha_2)}{\Gamma(\alpha_1)\Gamma(\alpha_2)} \Biggl) \\
        & \text{Note: we can drop the first term since it does not depend on $\alpha$, equivalent to $\frac{\partial \log p}{\partial \theta} = 0$} \\
        &= \sum_{i=1}^{m} \log \Biggl( \frac{\Gamma(\alpha_1 + \alpha_2)}{\Gamma(20 + \alpha_1 + \alpha_2)} \Biggl) + \log \Biggl( \frac{\Gamma(n_1(x_i) + \alpha_1)\Gamma(n_2(x_i) + \alpha_2)}{\Gamma(\alpha_1)\Gamma(\alpha_2)} \Biggl) \\
        &=  m*\log \Biggl( \frac{\Gamma(\alpha_1 + \alpha_2)}{\Gamma(20 + \alpha_1 + \alpha_2)} \Biggl) + \sum_{i=1}^{m} \log ( \Gamma(n_1(x_i) + \alpha_1)\Gamma(n_2(x_i) + \alpha_2)) - \log (\Gamma(\alpha_1)\Gamma(\alpha_2)) \\
        &=  m*[\log(\Gamma(\alpha_1 + \alpha_2)) - \log(\Gamma(20 + \alpha_1 + \alpha_2))] + \sum_{i=1}^{m} \log ( \Gamma(n_1(x_i) + \alpha_1)) + \log (\Gamma(n_2(x_i) + \alpha_2)) \\ & \;\;\;\; - \log (\Gamma(\alpha_1)) - \log(\Gamma(\alpha_2)) \\
    \end{align*}
{{< /math >}}


We now find $\frac{\partial \log p}{\partial \theta} = 
    \begin{bmatrix}
    \frac{\partial \log p}{\partial \alpha_1}
    \frac{\partial \log p}{\partial \alpha_2}
    \end{bmatrix} $ from the above log likelihood function.

{{< math >}}
\begin{align*}
        \frac{\partial \log p}{\partial \alpha_1} &= m(\psi(\alpha_1 + \alpha_2) - \psi(20 + \alpha_1 + \alpha_2)) + \sum_{i=1}^m \psi(n_1(x_i) + \alpha_1) - \psi(\alpha_1)\\
        &= m(\psi(\alpha_1 + \alpha_2) - \psi(20 + \alpha_1 + \alpha_2) - \psi(\alpha_1)) + \sum_{i=1}^m \psi(n_1(x_i) + \alpha_1)\\
        \frac{\partial \log p}{\partial \alpha_2} &= m(\psi(\alpha_1 + \alpha_2) - \psi(20 + \alpha_1 + \alpha_2)) + \sum_{i=1}^m \psi(n_1(x_i) + \alpha_1) - \psi(\alpha_2)\\
        &= m(\psi(\alpha_1 + \alpha_2) - \psi(20 + \alpha_1 + \alpha_2) - \psi(\alpha_2)) + \sum_{i=1}^m \psi(n_1(x_i) + \alpha_1)\\
    \end{align*}
{{< /math >}}

We now find $\frac{\partial^2 \log p}{\partial \theta \partial \theta^T} = 
    \begin{bmatrix}
    \frac{\partial^2 \log p}{\partial \alpha_1 \partial \alpha_1} & \frac{\partial^2 \log p}{\partial \alpha_1 \partial \alpha_2}
    \frac{\partial^2 \log p}{\partial \alpha_2 \partial \alpha_1} & \frac{\partial^2 \log p}{\partial \alpha_2 \partial \alpha_2}
    \end{bmatrix} $ from the above first-order partial derivatives. 
    
{{< math >}}    
\begin{align*}
    \frac{\partial^2 \log p}{\partial \alpha_1 \partial \alpha_1} &= m(\psi'(\alpha_1 + \alpha_2) - \psi'(20 + \alpha_1 + \alpha_2) - \psi'(\alpha_1)) + \sum_{i=1}^m \psi'(n_1(x_i) + \alpha_1)\\
    \frac{\partial^2 \log p}{\partial \alpha_1 \partial \alpha_2} = \frac{\partial^2 \log p}{\partial \alpha_2 \partial \alpha_1} &= m(\psi'(\alpha_1 + \alpha_2) - \psi'(20 + \alpha_1 + \alpha_2))\\
    \frac{\partial^2 \log p}{\partial \alpha_2 \partial \alpha_2} &= m(\psi'(\alpha_1 + \alpha_2) - \psi'(20 + \alpha_1 + \alpha_2) - \psi'(\alpha_2)) + \sum_{i=1}^m \psi'(n_2(x_i) + \alpha_2)\\
\end{align*}
{{< /math >}}

We now find $FIM = -E \biggl[ \frac{\partial^2 \log p}{\partial \theta \partial \theta^T}\biggl]$ and $CRLB = FIM^{-1}$ from the above second-order partial derivatives. 

For simplicity and spacing, let $v = m(\psi'(\alpha_1 + \alpha_2) - \psi'(20 + \alpha_1 + \alpha_2))$ since it occurs in each partial derivative.
{{< math >}}
  \begin{align*}
      FIM &= -E \begin{bmatrix}
  \frac{\partial^2 \log p}{\partial \alpha_1 \partial \alpha_1} & \frac{\partial^2 \log p}{\partial \alpha_1 \partial \alpha_2}
  \frac{\partial^2 \log p}{\partial \alpha_2 \partial \alpha_1} & \frac{\partial^2 \log p}{\partial \alpha_2 \partial \alpha_2}
  \end{bmatrix} \\
  &= -E \begin{bmatrix}
  v - m\psi'(\alpha_1) + \sum_{i=1}^m \psi'(n_1(x_i) + \alpha_1) & v \\
  v & v - m\psi'(\alpha_2) + \sum_{i=1}^m \psi'(n_1(x_i) + \alpha_2)
  \end{bmatrix} \\
  &= \begin{bmatrix}
  -E[v - m\psi'(\alpha_1) + \sum_{i=1}^m \psi'(n_1(x_i) + \alpha_1)] & -E[v] \\
  -E[v] & -E[v - m\psi'(\alpha_2) + \sum_{i=1}^m \psi'(n_2(x_i) + \alpha_2)]
  \end{bmatrix}\\
  CRLB &= FIM^{-1}\\
  &= \begin{bmatrix}
  -E[v - m\psi'(\alpha_1) + \sum_{i=1}^m \psi'(n_1(x_i) + \alpha_1)] & -E[v] \\
  -E[v] & -E[v - m\psi'(\alpha_2) + \sum_{i=1}^m \psi'(n_2(x_i) + \alpha_2)]
  \end{bmatrix}^{-1}
  \end{align*}
{{< /math >}}

## Maximum Likelihood Estimation
Recall from the previous section that we derived the partial derivatives of the log likelihood function as follows:
{{< math >}}
    \begin{align*}
        \frac{\partial \log p}{\partial \alpha_1} &= m(\psi(\alpha_1 + \alpha_2) - \psi(20 + \alpha_1 + \alpha_2)) + \sum_{i=1}^m \psi(n_1(x_i) + \alpha_1) - \psi(\alpha_1)\\
        &= m(\psi(\alpha_1 + \alpha_2) - \psi(20 + \alpha_1 + \alpha_2) - \psi(\alpha_1)) + \sum_{i=1}^m \psi(n_1(x_i) + \alpha_1)\\
        \frac{\partial \log p}{\partial \alpha_2} &= m(\psi(\alpha_1 + \alpha_2) - \psi(20 + \alpha_1 + \alpha_2)) + \sum_{i=1}^m \psi(n_1(x_i) + \alpha_1) - \psi(\alpha_2)\\
        &= m(\psi(\alpha_1 + \alpha_2) - \psi(20 + \alpha_1 + \alpha_2) - \psi(\alpha_2)) + \sum_{i=1}^m \psi(n_1(x_i) + \alpha_1)\\
    \end{align*}
    We can then set each of the derivatives equal to 0 and solve for our $\alpha_k$ parameter in order to find the Maximum Likelihood Estimator for each.
    \begin{align*}
        \frac{\partial \log p}{\partial \alpha_1} &= m(\psi(\alpha_1 + \alpha_2) - \psi(20 + \alpha_1 + \alpha_2) - \psi(\alpha_1)) + \sum_{i=1}^m \psi(n_1(x_i) + \alpha_1) = 0\\
        \frac{\partial \log p}{\partial \alpha_2} &= m(\psi(\alpha_1 + \alpha_2) - \psi(20 + \alpha_1 + \alpha_2) - \psi(\alpha_2)) + \sum_{i=1}^m \psi(n_1(x_i) + \alpha_1) = 0\\
    \end{align*}
{{< /math >}}

However, no closed form solution exists for this equation. As proposed in the project guidelines, we used the method of fixed point iteration proposed by Minka et al. in order to update each of the $\alpha_k$ values:
$$ \alpha_k^{(t)} = \alpha_k^{(t-1)} \frac{\sum_i \psi(n_{ik} + \alpha_k^{(t-1)}) - \psi(\alpha_k^{(t-1)})}{\sum_i \psi(n_i + \sum_k \alpha_k^{(t-1)} ) - \psi(\sum_k \alpha_k^{(t-1)})}$$ 
    
Specifically, for our problem of $k \in \{1, 2\}$, we implement:
{{< math >}}
  \begin{align*}
  \alpha_1^{(t)} &= \alpha_1^{(t-1)} \frac{\sum_i \psi(n_{i1} + \alpha_1^{(t-1)}) - \psi(\alpha_1^{(t-1)})}{\sum_i \psi(n_i + \alpha_1^{(t-1)} + \alpha_2^{(t-1)}) - \psi(\alpha_1^{(t-1)} + \alpha_2^{(t-1)})} \\
  \alpha_2^{(t)} &= \alpha_2^{(t-1)} \frac{\sum_i \psi(n_{i2} + \alpha_2^{(t-1)}) - \psi(\alpha_2^{(t-1)})}{\sum_i \psi(n_i + \alpha_1^{(t-1)} + \alpha_2^{(t-1)}) - \psi(\alpha_1^{(t-1)} + \alpha_2^{(t-1)})}
  \end{align*}
{{< /math >}}

# Method of Moments
For the beta-binomial distribution, via Wikipedia, the first moment ($\mu_1$) and second moment ($\mu_2$) based on $n_1$ are given as follows:
$$ \mu_1 = \frac{n_1\alpha_1}{\alpha_1 + \alpha_2}$$ 
$$\mu_2 = \frac{n_1 \alpha_1 (n_1 (1 + \alpha_1) + \alpha_2)}{(\alpha_1 + \alpha_2) (\alpha_1 + \alpha_2 + 1)} $$

For solving the first and second order moments, we set the following:
$$ \overline{x} = \frac{1}{N} \sum_{i=1}^N x_i $$
$$ \overline{x^2} = \frac{1}{N} \sum_{i=1}^N x^2_i $$

{{< math >}}
\begin{align*}
    \overline{x} &= E[x] = \mu_1 = \frac{n_1\alpha_1}{\alpha_1 + \alpha_2} \\
    \overline{x}(\alpha_1 + \alpha_2) &= n_1\alpha_1 \\
    \overline{x}\alpha_1 + \overline{x}\alpha_2 &= n_1\alpha_1 \\
    \overline{x}\alpha_2 &= n_1\alpha_1 - \overline{x}\alpha_1 \\
    \alpha_2 &= \frac{n_1\alpha_1 - \overline{x}\alpha_1}{\overline{x}} = \alpha_1(\frac{n_1}{\overline{x}}-1)
\end{align*}
{{< /math >}}

Let $v = \frac{n_1}{\overline{x}}-1$, and $v + 1 = \frac{n_1}{\overline{x}}$, such that $\alpha_2 = \alpha_1 v$.

{{< math >}}
\begin{align*}
    \overline{x^2} &= E[x^2] = \mu_2 = \frac{n_1 \alpha_1 (n_1 (1 + \alpha_1) + \alpha_2)}{(\alpha_1 + \alpha_2) (\alpha_1 + \alpha_2 + 1)}\\
    &= \frac{n_1 \alpha_1 (n_1 (1 + \alpha_1) + \alpha_1 v)}{(\alpha_1 + \alpha_1 v) (\alpha_1 + \alpha_1 v + 1)} \\
    &= \frac{n_1 \alpha_1 (\alpha_1 (n_1 + v) + n_1}{\alpha_1(1 + v) (\alpha_1(1 + v) + 1)} \\
    &= \frac{n_1 (\alpha_1 (n_1 + v) + n_1}{\alpha_1(1 + v)^2 + (1 + v))} \\
    &= \frac{n_1 (\alpha_1 (n_1 + \frac{n_1}{\overline{x}}-1) + n_1)}{\alpha_1(\frac{n_1}{\overline{x}})^2 + (\frac{n_1}{\overline{x}})} \\
    &= \frac{n_1 (\alpha_1 (n_1 + \frac{n_1}{\overline{x}}-1) + n_1)}{n_1(\frac{\alpha_1 n_1}{\overline{x}^2}) + (\frac{1}{\overline{x}})} \\
    &= \frac{\alpha_1 (n_1\overline{x} + n_1-\overline{x}) + n_1\overline{x}}{\frac{\alpha_1 n_1}{\overline{x}} + 1} \\
    \overline{x^2}(\frac{\alpha_1 n_1}{\overline{x}} + 1) &= \alpha_1 (n_1\overline{x} + n_1-\overline{x}) + n_1\overline{x}\\
    \alpha_1\frac{ \overline{x^2}n_1}{\overline{x}} + \overline{x} &= \alpha_1 (n_1\overline{x} + n_1-\overline{x}) + n_1\overline{x}\\
    \alpha_1(\frac{\overline{x^2}n_1}{\overline{x}} -n_1 \overline{x} - n_1 + \overline{x}) &= n_1 \overline{x} - \overline{x^2}\\
    \hat{\alpha_1}_{mom} &= \frac{n_1 \overline{x} - \overline{x^2}}{n_1(\frac{\overline{x^2}}{\overline{x}} -\overline{x} - 1) + \overline{x}}\\
\end{align*}
{{< /math >}}

We can now plug this back into our first order moment to solve for $\beta$.
{{< math >}}
\begin{align*}
    \alpha_2 &= \alpha_1(\frac{n_1}{\overline{x}}-1)\\
    &= \Biggl( \frac{n_1 \overline{x} - \overline{x^2}}{n_1(\frac{\overline{x^2}}{\overline{x}} -\overline{x} - 1) + \overline{x}} \Biggl) (\frac{n_1}{\overline{x}}-1)\\
    &= \frac{n_1^2 - n_1\overline{x} - \frac{n_1\overline{x^2}}{\overline{x}} + \overline{x^2}}{n_1(\frac{\overline{x^2}}{\overline{x}} -\overline{x} - 1) + \overline{x}}\\
    \hat{\alpha_2}_{mom} &= \frac{(n_1 - \overline{x})(n_1 - \frac{\overline{x^2}}{\overline{x}})}{n_1(\frac{\overline{x^2}}{\overline{x}} -\overline{x} - 1) + \overline{x}}\\
\end{align*}
{{< /math >}}
