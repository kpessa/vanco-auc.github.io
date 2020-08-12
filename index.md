## Vancomycin pharmacokinetic review <font size = "2" >*by Kurt Pessa, MS, PharmD*</font>
<table>
    <tr>
        <th width="70%">
            <p>
                <img src=Images/python-jupyter.png width="100%" align="left">
            </p>
            <div align="left" style="font-size:16px;font-weight:normal" font-family:courier>
                <ul>
                    <li>in Python using a jupyter notebook and matplotlib </li>
                </ul>
            </div>
        </th>
        <th> 
            <img src=Images/background.jpg width="100%" align="right">
        </th>
    </tr>
</table>

![](Images/onedose.png)

**External Links**
1. ASHP, IDSA, PIDSA 2020 Joint guidelines <https://academic.oup.com/ajhp/article/77/11/835/5810200"> 
2. RxKinetics: <http://www.rxkinetics.com/vanmodel.html>
3. Vancopk: <https://www.vancopk.com/vanco-kinetics-review/>
4. Clinical Pharmacokinetics, 6th Edition <https://store.ashp.org/Default.aspx?TabID=251&productId=514496146">


<details><summarY>Setting up python</summarY><blockquote>

<details><summary>Python Dependencies</summary>
<blockquote>

```python
import math
import numpy as np
import matplotlib.pyplot as plt
```

</blockquote>

</details>

<details><summary>Declaring Random PK Parameters</summary>
<blockquote>

```python
Dose = 1000 # mgs
t_inf = 1 # hrs
K_e = 0.100  ~ t1_2 = 6.9 hrs 
# Ke of 0.100 ~ 105 mL/min CrCl using 1985 Matze eqn
V_d = 45.5 # L ~ 70 kg * 0.65 L/kg
tau = 12
```

</blockquote>

</details>
    
</blockquote></details>

## I. General Equation 

<blockquote>
<img src="Images/conc_eqn.png"></blockquote>

1. `c_inf(t)` &rArr;  If infusing, use infusion equation
2. `c_elim(t)` &rArr; Else, use first-order elimination equation

    
**In Python:**
    
```python
# General concentration equation
def concentration(t):
    if t_inf_start < t <= t_inf_end:
        return c_inf(t)
    else:
        return c_elim(t)
```


<details><summary>Latex Syntax</summary>
<blockquote>
    
```latex
concentration(t) \Rightarrow \begin{bmatrix}
t_{inf,start} < t <= t_{inf,end} 
& c_{inf}(t)\\ 
else 
& c_{elim}(t) 
\end{bmatrix} 
\:\:\:\:\:\:\:
[1] 
```
</blockquote></details>
                
### a.) Infusion Equation

First, we need to get the equation for concentration while infusing

- The concentration while infusing at time t, `c_inf(t)` is: 

![](Images/cinf_eqn.png)

**In Python:**
     
```python
# Infusion Equation
def c_inf(t):
    return R_0 / CL_vanco * (1 - math.exp(-K_e * t))
```

<details><summary>Latex Syntax</summary>
> <code>\\[c_{inf}(t) \Rightarrow \frac{R_0}{CL_{vanco}}(1 - e^{-K_e \times t}) \:\;\;\;\;\; [2] \\]</code>
</details>

-----

#### 1. Rate of Infusion (R_0)
* Where <code>R_0</code> (The <strong>rate</strong> of infusion) is:     

<img src=Images/rate.png>

**In Python:**
```python 
#Rate of Infusion
R_0 = Dose / t_inf
```

> <details><summary>Latex Syntax</summary> 
> <code>\\[R_0 = \frac{Dose}{T_{inf}} \\]</code></details>

-----

#### 2. Vancomycin Clearance (CL_vanco)
* and <code>CL_vanco</code> (Vancomycin <strong>clearance</strong>) is: 

<img src=Images/clvanco.png>
 
**In Python:**
```python 
#Vancomycin Clearance
CL_vanco = K_e * V_d
```
 
> <details><summary>Latex Syntax</summary>
> <code>\\[CL_{vanco} = K_e \times V_d\\]</code></details>

-----

        
![](Images/cont_infusion.png)

- as this demonstration shows, if we continued infusing a 1,000 mg dose over 12 hours instead of 1 hour, we could get a level up in the 100's!!

### b.) Elimination Equation

- After infusing, now, we need to start using an elimination equation.  Here's the 1st-order elimination equation.


> ![](Images/celim_eqn.png)
> - where <code>c_eoi</code> is the <strong><em>concentration at the end of infusion</strong></em> <code>Tinf = t</code>


**In Python:**

```python

def c_elim(t): 
    return c_eoi * math.exp(-K_e * (t - t_inf))
```

> <details><summary>Latex Syntax</summary>
> <code> \\[c_{elim}(t) \Rightarrow c_{eoi} \times e^{-K_e \times (t-t_{inf})} \:\:\:\:\:\:\: [3]\\]</code></details>



```python
X = np.linspace(t_inf,tau,100) 
Y = [c_post(t) for t in X]
plt.plot(X,Y)
plt.title(f"First-order elimination starting at Cmax={cmax:.1f}, 1hr post-infusion")
```
![](Images/elmination.png)

## Now, all we gotta to is plot !

Since plot does not take function,
- sample 100 points along `concentration(t)` function over 12 hrs

```python
X = np.linspace(0,12,100) 
Y = [concentration(t) for t in X]

plt.plot(X,Y)
``` 
![](Images/onedose.png) 
