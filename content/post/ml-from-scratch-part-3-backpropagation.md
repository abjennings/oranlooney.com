---
title: 'ML From Scratch, Part 3: Backpropagation'
author: Oran Looney
date: 2018-12-26
tags:
  - Python
  - Statistics
  - From Scratch
  - Machine Learning
image: /post/ml-from-scratch-part-3-backpropagation_files/lead.jpg
draft: true
---

A typical neural network with one hidden layer would be 

$$\hat{y} =\sigma(W_2 \sigma(W_1 X + b_1) + b_2)$$

Where $\sigma()$ is a sigmoid function, $W_1$ and $W_2$ a weights for connections between layers, and $b_1$ and $b_2$ are bias vectors. $X$ is a matrix of data with one row per observation and one column per feature. The parameters of the model are $\Theta = (W_1, W_2, b_1, b_2)$. Let's also say that the loss function is $J(\Theta;X) = \frac{1}{2}||y - \hat{y}||^2$ for simplicity.

To fit the model to data, we find the parameters which minimize loss: $\hat{\Theta} = \text{argmin} \, J(\Theta;X)$. One condition which must be true at a local minima is that $\nabla_\Theta J = 0$. That gives us the equations:

$$ \frac{\partial J}{\partial W_1} = 0,  \frac{\partial J}{\partial W_2} = 0$$
$$\frac{\partial J}{\partial b_1} = 0,  \frac{\partial J}{\partial b_2} = 0$$

The notation used here is from matrix calculus, and we are taking partial derivatives with respect to a matrix (for W) or a vector (for b.) The [matrix cookbook][MC] may help you understand this notation. 

A full description of the forward pass of an $L$-layer neural net is given recursively as:

$$ a^{(0)} = X$$
$$a^{(i)} = \sigma(z^{(i)})$$
$$ z^{(i)} = W_i a^{(i-1)} + b_i$$
$$\hat{y} = a^{(L)} $$

For the backwards pass, therefore, we can use the chain rule. For example, let's do $W_1$:

$$ \frac{\partial J}{\partial W_1} = 
   \frac{\partial J}{\partial a^{(2)}} 
   \frac{\partial a^{(2)}}{\partial z^{(2)}} 
   \frac{\partial z^{(2)}}{\partial a^{(1)}} 
   \frac{\partial a^{(1)}}{\partial z^{(1)}} 
   \frac{\partial z^{(1)}}{\partial W_1}
$$

Given the forward pass equations given above, it turns out that each of these partial derivatives is straight-forward to calculate. You can verify for yourself that:

$$ \frac{\partial J}{\partial a^{(2)}} = \frac{\partial J}{\partial \hat{y}} = y - \hat{y}$$

$$ \frac{\partial a^{(i)}}{\partial z^{(i)}} = a^{(i)} \circ (1-a^{(i)}) $$

$$ \frac{\partial z^{(i)}}{\partial a^{(i-1)}} = W_i $$

$$ \frac{\partial z^{(i)}}{\partial W_i} = a^{(i-1)} $$

Which are all straight-forward, except perhaps the derivative of the sigmoid were we rely on the slightly non-obvious fact that $\sigma'(x) = \sigma(x) (1-\sigma(x))$. 

The justification for introducing the (technically extraneous) concept of $z$ is found in how absolutely obvious and clear it makes the separate steps of the forward and backwards pass. If we had to take $\partial a^{(i)} / \partial a^{(i-1)}$ directly without going through $z$ we would have to think about a non-linear function of a matrix all at once; with $z$ we're able to view it as the element-wise application of a non-linear function (which is Calculus 101) and the partial derivative of a linear expression with respect to a matrix (which is Matrix Calculus 101; see the [Cookbook][MC]). 

[MC]: https://www.math.uwaterloo.ca/~hwolkowi/matrixcookbook.pdf
