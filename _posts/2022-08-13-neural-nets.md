---
layout: post
title:  "Neural networks"
date:   2022-08-21 13:00:00 +0700
categories: artificial-intelligent machine-learning
tags: artificial-intelligent machine-learning neural-network
description: A note on neural networks
comments: true
eqn-number: true
---
> A note on Neural networks.
<!-- excerpt-end -->

- [Feed-forward network functions](#ff-func)
	- [Universal approximation property](#unv-approx)
	- [Weight-space symmetries](#w-s-sym)
- [Network training](#net-training)
	- [Network outputs probabilistic interpretation](#output-prob-itp)
		- [Univariate regression](#univ-output)
		- [Multivariate regression](#mult-output)
		- [Binary classification](#bi-clf)
		- [Multi-class classification](#mult-clf)
	- [Parameter optimization](#param-opt)
- [Backpropagation](#backprop)
- [Bayesian neural networks](#bayes-nn)
	- [Posterior parameter distribution](#posterior-param-dist)
- [Preferences](#preferences)
- [Footnotes](#footnotes)

## Feed-forward network functions
{: #ff-func}
Recall that the [linear models]({% post_url 2022-08-13-linear-models %}) used in regression and classification are based on linear combination of fixed nonlinear basis function $\phi_j(\mathbf{x})$ and take the form
\begin{equation}
y(\mathbf{x},\mathbf{w})=f\left(\sum_{j=1}^{M}w_j\phi_j(\mathbf{x})\right),\label{1}
\end{equation}
where in the case of regression, $f$ is the function $f(x)=x$, while in the classification case, $f$ takes the form of a nonlinear activation function (e.g., the [sigmoid function]({% post_url 2022-08-13-linear-models %}#logistic-sigmoid-func)).

**Neural networks** extend this model \eqref{1} by letting each basis functions $\phi_j(\mathbf{x})$ be a nonlinear function of a linear combination of the inputs, where the coefficients in the combination are the adaptive parameters.

More formally, neural networks is a series of layers, in which each layer represents a functional transformation. Let us consider the first layer by constructing $M$ linear combinations of the input variable $x_1,\ldots,x_D$ in the form
\begin{equation}
a_j=\sum_{i=1}^{D}w_{ji}^{(1)}x_i+w_{j0}^{(1)},\label{2}
\end{equation}
where
- $j=1,\ldots,M$;
- the superscript $^{(1)}$ indicates that we are working with parameters of the first layer;
- $w_{ji}^{(1)}$'s are called the **weights**;
- $w_{j0}^{(1)}$'s are known as the **biases**;
- $a_j$'s are referred as **activations**.

The activations $a_j$'s are then transformed using a differentiable, nonlinear **activation function** $h(\cdot)$, which correspond to $f(\cdot)$ in \eqref{1} to give
\begin{equation}
z_j=h(a_j),\label{3}
\end{equation}
where $z_j$ are called the **hidden units**. Repeating the same procedure as \eqref{2}, which was following \eqref{1}, $z_j$'s are taken as the inputs of the second layer to give $K$ outputs
\begin{equation}
a_k=\sum_{j=1}^{M}w_{kj}^{(2)}z_j+w_{k0}^{(2)},\label{4}
\end{equation}
where $k=1,\ldots,K$.

This process will be repeated in $L$ times with $L$ is the number of layers. At the last layer, for instance, the second layer of our example network, the outputs, also called **output unit activations**, $a_k$'s are transformed using an appropriate activation function to give a set of network output $y_k$. For example, in multiple binary classification problems, we can choose the logistic sigmoid as our activation function that
\begin{equation}
y_k=\sigma(a_k)\label{5}
\end{equation}
Combining all these steps \eqref{2}, \eqref{3}, \eqref{4} and \eqref{5} together, our neural network with sigmoidal output unit activation functions can be defined as
\begin{equation}
y_k(\mathbf{x},\mathbf{w})=\sigma\left(\sum_{j=1}^{M}w_{kj}^{(2)}h\left(\sum_{i=1}^{D}w_{ji}^{(1)}x_i+w_{j0}^{(1)}\right)+w_{k0}^{(2)}\right),\label{6}
\end{equation}
where all of the weights and biases are comprises together into a parameter vector $\mathbf{w}$. As suggested in [linear regression]({% post_url 2022-08-13-linear-models %}#dummy-coeff), we can also let the bias $w_{j0}^{(1)}$ be coefficient of a dummy input variable $x_0=1$ that makes \eqref{2} can be written as
\begin{equation}
a_j=\sum_{i=0}^{D}w_{ji}^{(1)}x_i
\end{equation}
This results that our subsequent layers are also able to be written in a more convenient form, which lets the entire network \eqref{6} take the form
\begin{equation}
y_k(\mathbf{x},\mathbf{w})=\sigma\left(\sum_{j=0}^{M}w_{kj}^{(2)}h\left(\sum_{i=0}^{D}w_{ji}^{(1)}x_i\right)\right)
\end{equation}
Our network is also an example of a **multilayer perception**, or **MLP**, which is a combination of [perceptron models]({% post_url 2022-08-13-linear-models %}#perceptron). The key difference is that while the neural network uses continuous sigmoidal nonlinearities in the hidden units, which is differentiable w.r.t the parameters, the perceptron algorithm uses step-function nonlinearities, which is in contrast non-differentiable.

The network network we have been considering so far is **feed-forward neural network**, whose outputs are deterministic functions of the inputs. Each (hidden or output) unit in such a network computes a function given by
\begin{equation}
z_k=h\left(\sum_{j}w_{kj}z_j\right),
\end{equation}
where the sum runs all over units sending connections to unit $k$ (bias included).

### Universal approximation property
{: #unv-approx}
Feed-forward networks with **hidden layers** (i.e., the layers in which the training data does not show the desired output, e.g., the first layer of our network, the second layer on the other hands is called the **output layer**) provide **universal approximation** property.

In concrete, the universal approximation theorem states that a feedforward network with a linear output layer and at least one hidden layer with any **squashing** activation function (e.g., the logistic sigmoid function) an approximate any continuous function on a compact subsets of $\mathbb{R}^n$.

### Weight-space symmetries
{: #w-s-sym}

## Network training
{: #net-training}

### Network outputs probabilistic interpretation
{: #output-prob-itp}

#### Univariate regression
{: #univ-output}
Consider the [regression problem]({% post_url 2022-08-13-linear-models %}#least-squares-reg) in which the target variable $t$ has Gaussian distribution with an $\mathbf{x}$ dependent mean
\begin{equation}
p(t\vert\mathbf{x},\mathbf{w})=\mathcal{N}(t\vert y(\mathbf{x},\mathbf{w}),\beta^{-1}),
\end{equation}
For the conditional distribution above, it is sufficient to take the output unit activation function to be the function $h(x)=x$, because such a network can approximate any continuous function from $\mathbf{x}$ to $y$.

Given the data set $(\mathbf{X},\mathbf{t})=\\{\mathbf{x}\_n,t_n\\}$, where $\mathbf{x}\_n$'s are i.i.d for $n=1,\ldots,N$, and where
\begin{align}
\mathbf{X}=\left[\begin{matrix}\vert&&\vert \\\\ \mathbf{x}\_1&\ldots&\mathbf{x}\_N \\\\ \vert&&\vert\end{matrix}\right],\hspace{1cm}\mathbf{t}=\left[\begin{matrix}t_1 \\\\ \vdots \\\\ t_N\end{matrix}\right]
\end{align}
The likelihood function therefore can be given by
\begin{align}
p(t\vert\mathbf{X},\mathbf{w},\beta)&=\prod_{n=1}^{N}p(t_n\vert\mathbf{x}\_n,\mathbf{w},\beta) \\\\ &=\prod_{n=1}^{N}\mathcal{N}(t_n\vert y(\mathbf{x}\_n,\mathbf{w}),\beta^{-1})
\end{align}
With a minor change as usual that taking negative natural logarithm of both sides gives us
\begin{align}
-\log p(\mathbf{t}\vert\mathbf{X},\mathbf{w},\beta)&=-\sum_{n=1}^{N}\log\mathcal{N}(t_n\vert y(\mathbf{x}\_n,\mathbf{w}),\beta^{-1}) \\\\ &=\frac{\beta}{2}\sum_{n=1}^{N}\big(y(\mathbf{x}\_n,\mathbf{w})-t_n\big)^2-\frac{N}{2}\log\beta+\frac{N}{2}\log 2\pi
\end{align}
Therefore, maximizing the likelihood function $p(\mathbf{t}\vert\mathbf{X},\mathbf{x},\beta)$ is equivalent to minimizing the sum-of-squares error function given as
\begin{equation}
E(\mathbf{w})=\frac{1}{2}\sum_{n=1}^{N}\big(y(\mathbf{x}\_n,\mathbf{w})-t_n\big)^2,
\end{equation}
This also means the value of $\mathbf{w}$ that minimizes $E(\mathbf{w})$ will be $\mathbf{w}\_\text{ML}$, which implies that the corresponding solution for $\beta$ will be given by
\begin{equation}
\frac{1}{\beta_\text{ML}}=\frac{1}{N}\sum_{n=1}^{N}\big(y(\mathbf{x}\_n,\mathbf{w}\_\text{ML})-t_n\big)^2
\end{equation}

#### Multivariate regression
{: #mult-output}
Similarly, we consider the multiple target variables case, in which the conditional distribution of the target therefore takes the form
\begin{equation}
p(\mathbf{t}\vert\mathbf{x},\mathbf{w},\beta)=\mathcal{N}(\mathbf{t}\vert\mathbf{y}(\mathbf{x},\mathbf{w}),\beta^{-1}\mathbf{I})
\end{equation}
Repeating the same procedure as the univariate case, maximizing likelihood function is also equivalent to minimizing the sum-of-squares error function given by
\begin{equation}
E(\mathbf{w})=\frac{1}{2}\sum_{n=1}^{N}\big\Vert\mathbf{y}(\mathbf{x}\_n,\mathbf{w})-\mathbf{t}\_n\big\Vert^2,
\end{equation}
which gives us the solution for the noise precision $\beta$ in the multivariate case as
\begin{equation}
\frac{1}{\beta_\text{ML}}=\frac{1}{NK}\sum_{n=1}^{N}\big\Vert\mathbf{y}(\mathbf{x}\_n,\mathbf{w}\_\text{ML})-\mathbf{t}\_n\big\Vert^2,
\end{equation}
where $K$ is the number of target variables.

#### Binary classification
{: #bi-clf}
Consider the problem of binary classification which outputs $t=1$ to denote class $\mathcal{C}\_1$ and otherwise to denote class $\mathcal{C}\_2$.

In particular, we consider a network having a single output whose activation function is a logistic sigmoid
\begin{equation}
y=\sigma(a)\doteq\frac{1}{1+\exp(-a)},
\end{equation}
which follows immediately that $0\leq y(\mathbf{x},\mathbf{w})\leq 1$.

This suggests us interpreting $y(\mathbf{x},\mathbf{w})$ as the conditional probability for class $\mathcal{C}\_1$, $p(\mathcal{C}\_1\vert\mathbf{x})$, and hence the corresponding conditional probability for class $\mathcal{C}\_2$ will be $p(\mathcal{C}\_2\vert\mathbf{x})=1-y(\mathbf{x},\mathbf{w})$. Or in other words, the conditional distribution $p(t\vert\mathbf{x},\mathbf{w})$ of targets $t$ given inputs $\mathbf{x}$ is then a Bernoulli distribution of the form
\begin{equation}
p(t\vert\mathbf{x},\mathbf{w})=y(\mathbf{x},\mathbf{w})^t\big(1-y(\mathbf{x},\mathbf{w})\big)^{1-t}
\end{equation}
If we consider a training set of $N$ independent observations as in the two regression tasks above, the likelihood function of our classification task will be given as
\begin{align}
p(\mathbf{t}\vert\mathbf{X},\mathbf{w})&=\prod_{n=1}^{N}p(t_n\vert\mathbf{x}\_n,\mathbf{w}) \\\\ &=\prod_{n=1}^{N}y(\mathbf{x}\_n,\mathbf{w})^{t_n}\big(1-y(\mathbf{x}\_n,\mathbf{w})\big)^{1-t_n}
\end{align}
Taking the negative natural logarithm of the likelihood as above gives us the cross-entropy error function
\begin{align}
E(\mathbf{w})=-\log p(\mathbf{t}\vert\mathbf{X},\mathbf{w})&=-\log\prod_{n=1}^{N}y(\mathbf{x}\_n,\mathbf{w})^{t_n}\big(1-y(\mathbf{x}\_n,\mathbf{w})\big)^{1-t_n} \\\\ &=-\sum_{n=1}^{N}t_n\log y_n+(1-t_n)\log(1-y_n),
\end{align}
where $y_n=y(\mathbf{x}\_n,\mathbf{w})$.

Moreover, consider the partial derivative of the above error function w.r.t the activation $a_n$, we have
\begin{align}
\frac{\partial E(\mathbf{w})}{\partial a_n}&=\frac{\partial-\sum_{n=1}^{N}t_n\log y_n+(1-t_n)\log(1-y_n)}{\partial a_n} \\\\ &=-\frac{t_n}{y_n}\frac{\partial y_n}{\partial a_n}-\frac{1-t_n}{1-y_n}\frac{\partial(1-y_n)}{\partial a_n} \\\\ &=\frac{\partial y_n}{\partial a_n}\left(\frac{1-t_n}{1-y_n}-\frac{t_n}{y_n}\right) \\\\ &=y_n(1-y_n)\left(\frac{1-t_n}{1-y_n}-\frac{t_n}{y_n}\right) \\\\ &=y_n-t_n,\label{eq:bin-drv-error-a}
\end{align}
where in the forth step, we have use the identity of the [derivative of sigmoid function]({% post_url 2022-08-13-linear-models %}#sigmoid-derivative) that
\begin{equation}
\frac{d\sigma}{d a}=\sigma(1-\sigma)
\end{equation}

#### Multi-class classification
{: #mult-clf}
For the multi-class classification that assigns input variables to $K$ separated classes, we can use the network with $K$ outputs each of which has a logistic sigmoid activation function. Each output $t_k\in\\{0,1\\}$ for $k=1,\ldots,K$ indicates whether the input will be assigned to class $\mathcal{C}\_k$

We first consider the case that the class labels are independent given the input vector, which means the conditional distributions for class $C_k$'s will be $K$ i.i.d Bernoulli distributions, in which the conditional probability for class $\mathcal{C}\_k$ will take the form
\begin{equation}
p(\mathcal{C}\_k\vert\mathbf{x},\mathbf{w})=y_k(\mathbf{x},\mathbf{w})^{t_k}\big(1-y_k(\mathbf{x},\mathbf{w})\big)^{1-t_k}
\end{equation}
Therefore, the joint distribution of them, the conditional distribution of the target variables will be given as
\begin{align}
p(\mathbf{t}\vert\mathbf{x},\mathbf{w})&=\prod_{k=1}^{K}p(\mathcal{C}\_k\vert\mathbf{x},\mathbf{w}) \\\\ &=\prod_{k=1}^{K}y_k(\mathbf{x},\mathbf{w})^{t_k}\big(1-y_k(\mathbf{x},\mathbf{w})\big)^{1-t_k}
\end{align}
Let $\mathbf{T}$ denote the combination of all the targets $\mathbf{t}\_n$, i.e.,
\begin{equation}
\mathbf{T}=\left[\begin{matrix}-\hspace{0.15cm}\mathbf{t}\_1^\text{T}\hspace{0.15cm}- \\\\ \vdots \\\\ -\hspace{0.15cm}\mathbf{t}\_N^\text{T}\hspace{0.15cm}-\end{matrix}\right],
\end{equation}
the likelihood function therefore takes the form of
\begin{align}
p(\mathbf{T}\vert\mathbf{X},\mathbf{w})&=\prod_{n=1}^{N}p(\mathbf{t}\_n\vert\mathbf{x}\_n,\mathbf{w}) \\\\ &=\prod_{n=1}^{N}\prod_{k=1}^{K}y_k(\mathbf{x}\_n,\mathbf{w})^{t_k}\big(1-y_k(\mathbf{x}\_n,\mathbf{w})\big)^{1-t_k}\label{eq:mult-clf-llh}
\end{align}
Analogy to the binary case, taking the negative natural logarithm of the likelihood \eqref{eq:mult-clf-llh} gives us the corresponding cross-entropy error function for the multi-class case, given as
\begin{align}
E(\mathbf{w})=-\log p(\mathbf{T}\vert\mathbf{X},\mathbf{w})&=-\log\prod_{n=1}^{N}\prod_{k=1}^{K}y_k(\mathbf{x}\_n,\mathbf{w})^{t_{nk}}\big(1-y_k(\mathbf{x}\_n,\mathbf{w})\big)^{1-t_{nk}} \\\\ &=-\sum_{n=1}^{N}\sum_{k=1}^{K}t_{nk}\log y_{nk}+(1-t_{nk})\log(1-y_{nk}),\label{eq:mult-clf-error}
\end{align}
where $y_{nk}$ is short for $y_k(\mathbf{x}\_n,\mathbf{w})$.

Similar to the binary case, consider the partial derivative of the error function \eqref{eq:mult-clf-error} w.r.t to the activation for a particular output unit $a_{nk}$ we have
\begin{align}
\frac{\partial E(\mathbf{w})}{\partial a_{nk}}&=\frac{\partial-\sum_{n=1}^{N}\sum_{k=1}^{K}t_{nk}\log y_{nk}+(1-t_{nk})\log(1-y_{nk})}{\partial a_{nk}} \\\\ &=-\frac{t_{nk}}{y_{nk}}\frac{\partial y_{nk}}{\partial a_{nk}}+\frac{1-t_{nk}}{1-y_{nk}}\frac{\partial y_{nk}}{\partial a_{nk}} \\\\ &=y_{nk}(1-y_{nk})\left(\frac{1-t_{nk}}{1-y_{nk}}-\frac{t_{nk}}{y_{nk}}\right) \\\\ &=y_{nk}-t_{nk}\label{eq:mult-drv-error-a}
\end{align}
which takes the same form as \eqref{eq:bin-drv-error-a}

On the other hands, if each input is assigned only to one of $K$ classes (mutually exclusive), the conditional distributions for class $C_k$ will be instead given as
\begin{equation}
p(\mathcal{C}\_k\vert\mathbf{x})=p(t_k=1\vert\mathbf{x})=y_k(\mathbf{x},\mathbf{w}),
\end{equation}
and thus the conditional distribution of the targets is
\begin{equation}
p(\mathbf{t}\vert\mathbf{x},\mathbf{w})=\prod_{k=1}^{K}p(t_k=1\vert\mathbf{x})^{t_k}=\prod_{k=1}^{K}y_k(\mathbf{x},\mathbf{w})^{t_k}
\end{equation}
The likelihood is therefore given as
\begin{equation}
p(\mathbf{T}\vert\mathbf{X},\mathbf{w})=\prod_{n=1}^{N}p(\mathbf{t}\_n\vert\mathbf{x}\_n,\mathbf{w})=\prod_{n=1}^{N}\prod_{k=1}^{K}y_k(\mathbf{x}\_n,\mathbf{w})^{t_{nk}},
\end{equation}
which gives us the following cross-entropy error function by taking the negative natural logarithm
\begin{align}
E(\mathbf{w})=-\log p(\mathbf{T}\vert\mathbf{X},\mathbf{w})&=-\log\prod_{n=1}^{N}\prod_{k=1}^{K}y_k(\mathbf{x},\mathbf{w})^{t_{nk}} \\\\ &=-\sum_{n=1}^{N}\sum_{k=1}^{K}t_{nk}\log y_k(\mathbf{x}\_n,\mathbf{w})
\end{align}
Taking the derivative of this error function w.r.t to the activation for a particular output unit also lets us end up with the form as \eqref{eq:bin-drv-error-a} and \eqref{eq:mult-drv-error-a}.

### Parameter optimization
{: #param-opt}

## Backpropagation
{: #backprop}

## Bayesian neural networks
{: #bayes-nn}

### Posterior parameter distribution
{: #posterior-param-dist}

## Preferences
{: #preferences}
[1] Christopher M. Bishop. [Pattern Recognition and Machine Learning](https://link.springer.com/book/9780387310732). Springer New York, NY.

[2] Ian Goodfellow & Yoshua Bengio & Aaron Courville. [Deep Learning](https://www.deeplearningbook.org). MIT Press (2016).

## Footnotes
{: #footnotes}