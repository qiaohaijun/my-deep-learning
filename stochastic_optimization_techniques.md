http://colinraffel.com/wiki/stochastic_optimization_techniques

---

{{tag>"Paper summaries" "Neural Networks" "Optimization"}}

====== Stochastic Optimization Techniques ======
 
Neural networks are often trained stochastically, i.e. using a method where the objective function changes at each iteration.  This stochastic variation is due to the model being trained on different data during each iteration.  This is motivated by (at least) two factors: First, the dataset used as training data is often too large to fit in memory and/or be optimized over efficiently.  Second, the objective function is typically nonconvex, so using different data at each iteration can help prevent the model from settling in a local minimum.  Furthermore, training neural networks is usually done using only the first-order gradient of the parameters with respect to the loss function.  This is due to the large number of parameters present in a neural network, which for practical purposes prevents the computation of the Hessian matrix.  Because vanilla gradient descent can diverge or converge incredibly slowly if its learning rate hyperparameter is set inappropriately, many alternative methods have been proposed which are intended to produce desirable convergence with less dependence on hyperparameter settings.  These methods often effectively compute and utilize a preconditioner on the gradient, adaptively change the learning rate over time or approximate the Hessian matrix.  This document summarizes some of the more popular methods proposed recently; for a similar overview see ((Ruder, An overview of gradient descent optimization algorithms http://sebastianruder.com/optimizing-gradient-descent/index.html#gradientdescentoptimizationalgorithms)) or the documentation of Climin ((https://climin.readthedocs.org/en/latest/#optimizer-overview)) or ((Schaul, Antonoglou, Silver, Unit Tests for Stochastic Optimization)) for a comparison on some simple tasks.

In the following, we will use $\theta_t$ to denote some generic parameter of the model at iteration $t$, to be optimized according to some loss function $\mathcal{L}$ which is to be minimized.

===== Stochastic Gradient Descent =====

Stochastic gradient descent (SGD) simply updates each parameter by subtracting the gradient of the loss with respect to the parameter, scaled by the learning rate $\eta$, a hyperparameter.  If $\eta$ is too large, SGD will diverge; if it's too small, it will converge slowly.  The update rule is simply
$$
\theta_{t + 1} = \theta_t - \eta \nabla \mathcal{L}(\theta_t)
$$

===== Momentum =====

In SGD, the gradient $\nabla \mathcal{L}(\theta_t)$ often changes rapidly at each iteration $t$ due to the fact that the loss is being computed over different data.  This is often partially mitigated by re-using the gradient value from the previous iteration, scaled by a momentum hyperparameter $\mu$, as follows:

\begin{align*}
v_{t + 1} &= \mu v_t - \eta \nabla \mathcal{L}(\theta_t) \\
\theta_{t + 1} &= \theta_t + v_{t+1}
\end{align*}

It has been argued that including the previous gradient step has the effect of approximating some second-order information about the gradient.

===== Nesterov's Accelerated Gradient =====

In Nesterov's Accelerated Gradient (NAG), the gradient of the loss at each step is computed at $\theta_t + \mu v_t$ instead of $\theta_t$.  In momentum, the parameter update could be written $\theta_{t + 1} = \theta_t + \mu v_t - \eta \nabla \mathcal{L}(\theta_t)$, so NAG effectively computes the gradient at the new parameter location but without considering the gradient term.  In practice, this causes NAG to behave more stably than regular momentum in many situations.  A more thorough analysis can be found in ((Sutskever, Martens, Dahl, and Hinton, "On the importance of initialization and momentum in deep learning" (ICML 2013) )).  The update rules are then as follows:

\begin{align*}
v_{t + 1} &= \mu v_t - \eta \nabla\mathcal{L}(\theta_t + \mu v_t) \\
\theta_{t + 1} &= \theta_t + v_{t+1}
\end{align*}

===== Adagrad =====

Adagrad effectively rescales the learning rate for each parameter according to the history of the gradients for that parameter.  This is done by dividing each term in $\nabla \mathcal{L}$ by the square root of the sum of squares of its historical gradient.  Rescaling in this way effectively lowers the learning rate for parameters which consistently have large gradient values.  It also effectively decreases the learning rate over time, because the sum of squares will continue to grow with the iteration.  After setting the rescaling term $g = 0$, the updates are as follows:
\begin{align*}
g_{t + 1} &= g_t + \nabla \mathcal{L}(\theta_t)^2 \\
\theta_{t + 1} &= \theta_t - \frac{\eta\nabla \mathcal{L}(\theta_t)}{\sqrt{g_{t + 1}} + \epsilon}
\end{align*}
where division is elementwise and $\epsilon$ is a small constant included for numerical stability.  It has nice theoretical guarantees and empirical results ((Dyer, "Notes on AdaGrad")) ((Duchi, Hazan, and Singer, "Adaptive Subgradient Methods for Online Learning and Stochastic Optimization" (COLT 2010) )).

===== RMSProp =====

In its originally proposed form ((Hinton, Srivastava, and Swersky, "rmsprop: Divide the gradient by a running average of its recent magnitude")), RMSProp is very similar to Adagrad.  The only difference is that the $g_t$ term is computed as a exponentially decaying average instead of an accumulated sum.  This makes $g_t$ an estimate of the second moment of $\nabla \mathcal{L}$ and avoids the fact that the learning rate effectively shrinks over time.  The name "RMSProp" comes from the fact that the update step is normalized by a decaying RMS of recent gradients.  The update is as follows:

\begin{align*}
g_{t + 1} &= \gamma g_t + (1 - \gamma) \nabla \mathcal{L}(\theta_t)^2 \\
\theta_{t + 1} &= \theta_t - \frac{\eta\nabla \mathcal{L}(\theta_t)}{\sqrt{g_{t + 1}} + \epsilon}
\end{align*}

In the original lecture slides where it was proposed, $\gamma$ is set to $.9$.  In ((Dauphin, Vries, Chung and Bengion, “RMSProp and equilibrated adaptive learning rates for non-convex optimization”)), it is shown that the $\sqrt{g_{t + 1}}$ term approximates (in expectation) the diagonal of the absolute value of the Hessian matrix (assuming the update steps are $\mathcal{N}(0, 1)$ distributed).  It is also argued that the absolute value of the Hessian is better to use for non-convex problems which may have many saddle points.

Alternatively, in ((Graves, "Generating Sequences with Recurrent Neural Networks")), a first-order moment approximator $m_t$ is added.  It is included in the denominator of the preconditioner so that the learning rate is effectively normalized by the standard deviation $\nabla \mathcal{L}$.  There is also a $v_t$ term included for momentum.  This gives 

\begin{align*}
m_{t + 1} &= \gamma m_t + (1 - \gamma) \nabla \mathcal{L}(\theta_t) \\
g_{t + 1} &= \gamma g_t + (1 - \gamma) \nabla \mathcal{L}(\theta_t)^2 \\
v_{t + 1} &= \mu v_t - \frac{\eta \nabla \mathcal{L}(\theta_t)}{\sqrt{g_{t+1} - m_{t+1}^2 + \epsilon}}  \\
\theta_{t + 1} &= \theta_t + v_{t + 1}
\end{align*}

===== Adadelta =====

Adadelta ((Zeiler, "Adadelta: An Adaptive Learning Rate Method")) uses the same exponentially decaying moving average estimate of the gradient second moment $g_t$ as RMSProp.  It also computes a moving average $x_t$ of the updates $v_t$ similar to momentum, but when updating this quantity it squares the current step, which I don't have any intuition for.

\begin{align*}
g_{t + 1} &= \gamma g_t + (1 - \gamma) \nabla \mathcal{L}(\theta_t)^2 \\
v_{t + 1} &= -\frac{\sqrt{x_t + \epsilon} \nabla \mathcal{L}(\theta_t)}{\sqrt{g_{t+1} + \epsilon}} \\
x_{t + 1} &= \gamma x_t + (1 - \gamma) v_{t + 1}^2 \\
\theta_{t + 1} &= \theta_t + v_{t + 1}
\end{align*}

===== Adam =====

Adam is somewhat similar to Adagrad/Adadelta/RMSProp in that it computes a decayed moving average of the gradient and squared gradient (first and second moment estimates) at each time step.  It differs mainly in two ways: First, the first order moment moving average coefficient is decayed over time.  Second, because the first and second order moment estimates are initialized to zero, some bias-correction is used to counteract the resulting bias towards zero.  The use of the first and second order moments, in most cases, ensure that typically the gradient descent step size is $\approx \pm \eta$ and that in magnitude it is less than $\eta$.  However, as $\theta_t$ approaches a true minimum, the uncertainty of the gradient will increase and the step size will decrease.  It is also invariant to the scale of the gradients.  Given hyperparameters $\gamma_1$, $\gamma_2$, $\lambda$, and $\eta$, and setting $m_0 = 0$ and $g_0 = 0$ (note that the paper denotes $\gamma_1$ as $\beta_1$, $\gamma_2$ as $\beta_2$, $\eta$ as $\alpha$ and $g_t$ as $v_t$), the update rule is as follows: ((Kingma and Ba, "Adam: A Method for Stochastic Optimization"))

\begin{align*}
m_{t + 1} &= \gamma_1 m_t + (1 - \gamma_1) \nabla \mathcal{L}(\theta_t) \\
g_{t + 1} &= \gamma_2 g_t + (1 - \gamma_2) \nabla \mathcal{L}(\theta_t)^2 \\
\hat{m}_{t + 1} &= \frac{m_{t + 1}}{1 - \gamma_1^{t + 1}} \\
\hat{g}_{t + 1} &= \frac{g_{t + 1}}{1 - \gamma_2^{t + 1}} \\
\theta_{t + 1} &= \theta_t - \frac{\eta \hat{m}_{t + 1}}{\sqrt{\hat{g}_{t + 1}} + \epsilon}
\end{align*}

===== ESGD =====

((Dauphin, Vries, Chung and Bengion, "RMSProp and equilibrated adaptive learning rates for non-convex optimization"))

===== Adasecant =====

((Gulcehre and Bengio, "Adasecant: Robust Adaptive Secant Method for Stochastic Gradient"))

===== vSGD =====

((Schaul, Zhang, LeCun, "No More Pesky Learning Rates"))

===== Rprop =====

((Riedmiller and Bruan, "A Direct Adaptive Method for Faster Backpropagation Learning: The RPROP Algorithm"))
