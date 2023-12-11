# Braids from Lorenz orbits

The Lorenz system is a system of differential equations given by
```math
\begin{align}
& \frac{\mathrm{d} x}{\mathrm{~d} t}=\sigma(y-x) \\
& \frac{\mathrm{d} y}{\mathrm{~d} t}=x(\rho-z)-y \\
& \frac{\mathrm{d} z}{\mathrm{~d} t}=x y-\beta z .
\end{align}
```

The original parameters studied by Lorenz were $$\rho=28, \sigma=10, \beta=8/3$$. For those parameters, the system exhibits a well-known "Butterfly-like attractor" with two lobes:

![Lorenz Attractor](/images/Lorenz_Attractor.png)
<html>
<body>
<p><img src="/images/Lorenz_Attractor.png" alt="Lorenz attractor" width="50%" ></p>
</body>
</html>
<html>
<body>

Among all orbits of the system, some of them form closed loops. They can be visually described by recording the sequence of "lobes" visited (R for right, L for left). This is best visualized through a "template":

![Lorenz Template](/images/Lorenz_LR.png)
