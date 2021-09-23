---
layout: post2
title: "Solving implicit equation through solution space regression"
permalink: /:day/:month/:year/:title
published: true+
tags: "writting"
img: "images/pro_transfer_learning.jpg"
description: "It was needed to solve an equation to get a useful metric, but it was also necessary that the solution be expressed in a very simple way. It turns out that to solve it, you need to use the LambertW function. Since we needed a simple formula as a solution, we came up with an indirect way to solve it."

---
<style>
    .center {
  display: block;
  margin-left: auto;
  margin-right: auto;
}

pre code {
  background-color: #eee;
  border: 1px solid #999;
  display: block;
  padding: 20px;
}

</style>

How would you solve the following equation? <br>

<img src="../../../images/equation_1.png" class="center"> <br> 

Ok, we can solve it by approximation using numerical methods (Newton could be a possibility, for example). We put the equation as "something" equals zero, and using optimization techniques or numerical methods we can minimize it, setting zero as target value. That could be a possibility but it's not what we are looking for. What we would like to have is simple formula that returns X given k and t. I mean X = F(k,t)

So I come up with the following. If we call: <br>

<img src="../../../images/equation_2.png" class="center"> <br> 

Then we can get:

<img src="../../../images/equation_3.png" class="center"> <br> 

But it has no solution either. One way I thought is to derive both sides of the equation, but since the orginal equation domain is different from its derivative, the solution for the derivative is not a solution for its primitive. So, what can we do now? 

There is a solution space out there for that equation and to know how it looks we can optimize it for a given K value and get its corresponding Z value. If we do that for ~ 40,000 k values, we get 40,000 data points. A polinomyal regression for that group of data points can be understood as the solution space of the equation. Take a look at the python code:



<pre>
  <code>
    import numpy as np
    from numpy import arange
    from scipy.optimize import fsolve

    def f(z,k):

        'Function to minimize with solver.
        Args: 
            k: constant 
            z: starting value'

        return z - np.log(k*z - 1) 

    k_values = []
    z_values = []

    for k in arange(1,5,0.0001): # since K is always less than 5
        z = fsolve(f,100,k) # get z by minimizing f
        k_values.append(k)
        z_values.append(z[0])

    # creating a 4th degree polynomial logarithm regression model by minimizing the loss
    # function given the datapoints obtained before.

    log_reg = np.polyfit(np.log(k_values), z_values, 4) 

    import matplotlib.pyplot as plt

    # plot both actual data point and regression model

    k = np.linspace(1, 5, 100)
    y = log_reg[0]*np.log(k)**4 + log_reg[1]*np.log(k)**3 + log_reg[2]*np.log(k)**2 + log_reg[3]*np.log(k) + log_reg[4]
    fig = plt.figure()
    ax = fig.add_subplot(1,1,1)
    plt.plot(k, y, "r")
    plt.plot(k_values, z_values)
    plt.show()

  </code>
</pre>

