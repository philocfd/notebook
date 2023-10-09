# 不同轴向诱导因子计算方法
代号和轴向诱导因子计算之间的关系
{"0": "Classic BEM",
"1": "Spera Correction",
"2": "Buhl Correction (Aerodyn)", 
"3": "Buhl Correction (QBlade)",
"4": "Wilson and Walker model",
"5": "Modified ABS model"}
# 原始方法
$\begin{align*}
a &= \frac{\sigma \cdot C_{\text{norm}}}{4F\sin^2(\phi) + \sigma \cdot C_{\text{norm}} \cdot \text{prop\_coeff}} \\
\\
a' &= \frac{\sigma \cdot C_{\text{tang}}}{4F\sin(\phi)\cos(\phi) - \sigma \cdot C_{\text{tang}} \cdot \text{prop\_coeff}}
\end{align*}$
# Spera Correction
$\begin{align*}
a &= \frac{\sigma \cdot C_{\text{norm}}}{4F\sin^2(\phi) + \sigma \cdot C_{\text{norm}}} \\
&\text{if } a \geq 0.2: \\
&\quad a_c = 0.2 \\
&\quad K = \frac{4F\sin^2(\phi)}{\sigma \cdot C_{\text{norm}}} \\
&\quad \text{to\_sqrt} = \left|K(1 - 2a_c) + 2\right|^2 + 4(Ka_c^2 - 1) \\
&\quad a = 1 + 0.5K(1 - 2a_c) - 0.5\sqrt{\text{to\_sqrt}} \\
\\
a' &= \frac{\sigma \cdot C_{\text{tang}}}{4F\sin(\phi)\cos(\phi) - \sigma \cdot C_{\text{tang}}}
\end{align*}$
# Buhl Correction (Aerodyn)
$\begin{align*}
\text{if } C_{t_r} > 0.96 \cdot F: \\
a &= \frac{18F - 20 - 3\sqrt{C_{t_r}(50 - 36F) + 12F(3F - 4)}}{36F - 50} \\
\text{else:} \\
a &= \left(1 + \frac{4F\sin^2(\phi)}{\sigma \cdot C_{\text{norm}}}\right)^{-1} \\
\\
a' &= \left(-1 + \frac{4F\sin(\phi)\cos(\phi)}{\sigma \cdot C_{\text{tang}}}\right)^{-1}
\end{align*}$

# Buhl Correction (QBlade)

$\begin{align*}
\text{if } C_{t_r} \leq 0.96 \cdot F: \\
a &= \frac{1}{\frac{4F\sin^2(\phi)}{\sigma \cdot C_{\text{norm}}} + 1} \\
\text{else:} \\
a &= \frac{18F - 20 - 3\sqrt{|\,C_{t_r}(50 - 36F) + 12F(3F - 4)\,|}}{36F - 50} \\
\\
a' &= \frac{1}{2} \left(\sqrt{|\,1 + \frac{4}{\lambda_r^2} a (1 - a)\,|} - 1\right)
\end{align*}$
# Wilson and Walker model
$\begin{align*}
a &= a_{\text{last}} \\
a_c &= 0.2 \\
\text{if } F &= 0: \\
&\quad F = 1 \times 10^{-6} \\
\text{if } C_{t_r} \leq 0.64 \cdot F: \\
&\quad a = \frac{1 - \sqrt{1 - \frac{C_{t_r}}{F}}}{2} \\
\text{else:} \\
&\quad a = \frac{C_{t_r} - 4F a_c^2}{4F(1 - 2a_c)} \\
\\
a' &= \left(\frac{4F\sin(\phi)\cos(\phi)}{\sigma \cdot C_{\text{tang}}}-1\right)^{-1}
\end{align*}$

# Modified ABS model
$\begin{align*}
a &= a_{\text{last}} \\
\text{if } C_{t_r} < 0.96 \cdot F: \\
&\quad a = \frac{1 - \sqrt{1 - \frac{C_{t_r}}{F}}}{2} \\
\text{else:} \\
&\quad a = 0.1432 + \sqrt{-0.55106 + 0.6427 \cdot \frac{C_{t_r}}{F}} \\
\\
a'' &= \frac{4aF(1-a)}{\lambda_r^2} \\
\text{if } 1 + a'' < 0: \\
&\quad a' = 0 \\
\text{else:} \\
&\quad a' = \frac{\sqrt{1 + a''} - 1}{2}
\end{align*}$


# 代码
## 计算推力和转矩

```python
   # thrust and torque - Wiley, WE 2nd, p.124

            dT_MT = F * 4 * pi * _r * rho * v ** 2 * a * (1 - a) * _dr

            dT_BET = 0.5 * rho * B * _c * Vrel_norm ** 2 * \

                C_norm * _dr

            dQ_MT = F * 4 * aprime * (1 - a) * rho * \

                v * pi * _r ** 3 * omega * _dr

            dQ_BET = B * 0.5 * rho * Vrel_norm ** 2 * \

                C_tang * _c * _dr * _r
```

## 翼型受力计算

```python
  # force calculation

            dFL = Cl * 0.5 * rho * Vrel_norm ** 2 * _c * _dr  # lift force

            dFD = Cd * 0.5 * rho * Vrel_norm ** 2 * _c * _dr  # drag force

            dFt = dFL * sin(phi) - dFD * cos(phi)  # tangential force

            dFn = dFL * cos(phi) + dFD * sin(phi)  # normal force
```



