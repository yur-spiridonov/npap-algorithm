# An Alternative Algorithm for Summing Any Decimal Numbers in Binary Arithmetic

Any number **X** can be represented as an infinite integer **K** (0 ≤ K < ∞) of infinitesimal intervals **ɛ**.

The interval **ɛ** can be a decimal **ɛ₁₀** or binary **ɛ₂** number, or, in general, a number in any number system.

For a finite decimal **X**, the equality holds: **X = S·K₁₀·ɛ₁₀**, where **S = 1** or **−1**, **K₁₀** and **ɛ₁₀** — decimal numbers.

For a finite binary **Y**, the equality holds: **Y = S·K₂·ɛ₂**, where **S = 1** or **−1**, **K₂** and **ɛ₂** — binary numbers.

As **ɛ₁₀ → 0** and **ɛ₂ → 0**, **K₁₀ → ∞** and **K₂ → ∞**, and then **S·K₁₀·ɛ₁₀ = S·K₂·ɛ₂**. That is, for any infinitely precise decimal **X** there exists a binary **Y** that is infinitely precisely equal to **X**.

Any finite decimal or binary **K** (except **K = 0**) contains a certain number **j** of significant digits.

Significant digits are all non-zero digits in **X**, starting from the first non-zero one, and trailing zeros if the number is integer. For example, 0.00123 has three significant digits, 12300 has five significant digits.

### Representation of a number in normalized exponential form in the operational register

The form of recording any **X** in exponential form, in which the first non-zero significant digit follows the radix point, is called the normalized format.

For brevity, under the abbreviation "NF X" we will understand — the format of the number **X** in normalized form.

NF **X** is written as  
**X = S·0.K·β^e**  

where **0.K** — mantissa, **K** — integer consisting of **j** significant digits of the number written in natural form, **β** — number system (base), **e** — exponent determining the position of the radix point in **X** represented in natural form relative to the position of the radix point in **X** written in exponential form.

For example, for 0.001234, **j = 4**, then **K = 1234**, and NF **X** = 0.1234·10⁻².

For **X** in NF, where **K** is an integer with **j** significant digits, the inequality holds:  
**β^(j−1) ≤ K < β^j − 1**.

Since **K ≠ 0**, **K = 0** is a special case for such representation.

For **X** represented in NF, four areas are allocated in the operational register — **S**, **p**, **s**, **w**. One digit is allocated for the sign **S** of the number, **p** digits for **K̃** which is the closest to **K**, one digit **s** for the sign of the exponent **e**, and **w** digits for the exponent **e**.

The location of the areas in the register can be arbitrary and depend on the implementation.

The maximum integer that can be written into the **p**-digit area of the register is **K̃_max = β^p − 1**.

The maximum integer that can be written into the **w**-digit area of the register is **e_max = β^w − 1**.

If all **j** digits of **K** of NF **X** fit into the **p**-digit area of the register, and all digits of its exponent **e** fit into the **w**-digit area, then such **X** is called representable. For representable numbers — **K ≤ K̃_max** and **e ≤ e_max**.

If **K > K̃_max**, then **K** cannot be written into the **p**-digit register without losing least-significant digits and/or if **e > e_max**, then all digits of the exponent **e** of NF **X** cannot be written into the **w**-digit area. In this case, **X** is a non-representable number.

If **K > K̃_max**, **e < e_max**, then to write the value of **K** into the **p**-area of the register, it is necessary to reduce the number **j** of digits in **K** by rounding it to the closest **K̃** with **p** significant digits.

For example, if **p = 2**, then for **X = 0.1234·10⁻³**, where **K = 1234** has **j = 4** digits, after rounding to **p = 2** significant digits **K̃ = 12** and **X̃ = 0.12·10⁻³**.

If **e > e_max**, then **X̃** is sometimes considered infinitely large.

In the IEEE 754 standard, special cases such as NaN, ±0, and ±∞ are encoded with a special number with exponent **e_min = −e_max + 1**.

### NPAt format

Assume we have NF **X̃ = S·0.K̃·β^e**, in which **K̃** contains **p** digits.

As described above, this number is written into the operational register in four areas — (**S**, **p**, **s**, **w**).

It is assumed that the radix point in NF **X̃** is placed before the most significant non-zero digit **K̃**. In this case, the fractional **0.K̃** contains **p** digits.

If we assume that the radix point is placed after the least-significant digit of the integer **K̃**, then to keep the value of **X̃** unchanged, the exponent **e** must be decreased by **p**. **X** will now look like  
**X̂ = S·K̂·β^(e−p)**

Assume that the radix point in the **p**-area of the register is placed after the digit with number **t ≤ p**. Where **t** is counted from left to right from the first most significant digit of the **p**-area of the register. Then, to keep the value of **X** unchanged, the exponent **e** in the normalized **X̃** must be decreased by **t**. **X** will now be represented as  
**X = S·K′·β^(e−t)**

In this expression, **K′ < K** will contain an integer non-zero part with **f** digits, the number of which coincides with the number of digits in the **t**-area of the operational register. Round **K′** to the closest integer **K̂**. The result will be  
**X̂ = S·K̂·β^(e−t)**,  
which will be the closest to **X** with **f** significant digits.

Note that the radix point is defined not by the number of digits **f** in **K̂**, but by the number of digits **t** in the **t**-area of the operational register. This is explained by the fact that in arithmetic transformations there can be cases when **f < t**. For example, when subtracting numbers close in value.

The number written into the operational register **X̂** with integer **K̂** can be considered a number with the radix point after the **t**-th digit. **X̂** will be briefly called a number with point after **t** (NPAt).

Since the first **t** digits of the operational register form the **t**-area for writing **K̂**, the **p**-area of the register can guarantee representation of **K̂** with a maximum of **t** digits.

By setting **t < p**, we thereby reduce the number of digits by which **X** can be represented, while maintaining the maximum reliability of **f** digits in **K̂**.

This allows easy organization of processing numbers with different precision **t ≤ p** on the same operational register with **p**-area.

Denote **q = (e − t)**. Then **X̂** can be written as  
**X̂ = S·K̂·β^q**.

The coefficient **q** determines the maximum number of significant digits **t** that the integer **K̂** in the rounded **X̂** can contain. **q** will be called the rounding coefficient (RC).

Denote  
**μ = β^q**.

Then **μ** can be interpreted as the scaling factor, and NPAt can be written as  
**X̂ = S·K̂·μ**.

**Example.** Suppose we are performing calculations in a decimal processor in which the **p**-area of the operational register contains **p = 4** digits. Let us have NF  
**Z = 0.12346…·10⁻⁴** with **j > p** significant digits. Then the closest to **Z** number NF **X = 0.1235·10⁻⁴** with exponent **e = −4** can be written into the **p**-area. Thus, after rounding the mantissa of number **Z**, **K = 1235** will be written into the **p**-area of the register.

Suppose further that we want to perform calculations with precision up to **t = 2** significant digits. To do this, represent **X** in NPAt format. For **t = 2** we get **K′ = 12.35**, which after rounding gives **K̂ = 12**. Define RC **q = (e − t) = −6**, then **μ = 10⁻⁶**. The result is NPAt **X̂ = 12·10⁻⁶**, in which **f = 2** digits.

In the operational register for writing **X̂**, as well as for NF **X̃**, four areas are allocated — **S**, **p**, **s**, **w**. The sign of the number **X** is written into area **S**; **f** digits of the integer coefficient **K̂** or zero are written into the **t**-area of the operational register; the sign of RC **q** is written into area **s**; RC **q** is written into **w**.

These parameters determine the value of NPAt — **X̂ = S·K̂·μ**.

If there were no losses of non-zero significant digits when rounding **K′**, then NPAt is an exact number, which we will denote by the symbol **X̄**. Exact **X̄** are a subset of NPAt **X̂**.

In general, **K̂** during calculations can have **0 ≤ f ≤ t** digits and can take any integer value in the interval  
**0 ≤ K̂ < K̂_max**  
where **K̂_max = β^t − 1**.

**Examples:**
1. If in natural form **X = 0.0123**, then in normalized form **X = 0.123·10⁻¹**. For **t = 2** we will have RC **q = −1 − 2 = −3** and then **μ = 10⁻³**. The result is NPAt **X̂ = 12·10⁻³**.
2. For **X = 12000** in normalized form **X = 0.120·10⁵**. For **t = 2** we will have RC **q = 5 − 2 = 3** and then **μ = 10³**. The result is NPAt **X̄ = 12·10³**.
3. For **X = 2** in normalized form **X = 0.200·10¹**. For **t = 3** we will have RC **q = 1 − 3 = −2**, **μ = 10⁻²**. And since there was no rounding, NPAt will be exact **X̄ = 200·10⁻²** or **X̄ = 2**.

### NPAt vs normalized

The maximum positive **X** that can be represented in NF **X̃** equals  
**X̃_max = 0.K̃_max·β^(e_max)**  

For NF **X̃** in the operational register with **p**-area, the maximum that can be written is  
**0.K̃_max ≈ 1**  

Then the maximum positive **X** that can be represented in NF **X̃** equals  
**X̃_max ≈ β^(e_max)**  

According to the definition, the minimum **K̃_min** in NF **X̃** is  
**K̃_min = 1**  

Then the minimum positive **X** that can be represented in NF **X̃** equals  
**X̃_min = 0.1·β^(e_min) = β^(e_min−1)**  

Or, if **e_min = −e_max**, then  
**X̃_min = β^(e_min−1)**  

Thus, the range of coefficient values is approximately  
**X̃_max / X̃_min ≈ β^e_max / β^(-e_max−1) = β^(2e_max+1)**  

Now consider **X** in NPAt format. The maximum positive **X** that can be represented in NPAt **X̂** equals  
**X̂_max = K̂_max·β^(q_max)**  

If **K̂_max** contains **t = p** digits, then the maximum positive **X** that can be represented in NPAt **X̂** equals  
**K̂_max = β^t − 1 ≈ β^t**  

And the maximum positive **X** that can be represented in NPAt **X̂** equals  
**X̂_max = β^(q_max + t)**  

The minimum number that can be written into the **p**-area of the operational register is  
**K̂_min = 1 or 0.**  

And the minimum positive **X** that can be represented in NPAt **X̂** equals  
**X̂_min = β^q_min or 0.**  

If **q_min = −q_max**, then  
**X̂_min = β^(-q_max)**  

Then the range of values of **K̂** in NPAt **X̂** is approximately  
**X̂_max / X̂_min ≈ β^(q_max + t) / β^(-q_max) = β^(2q_max + t)**  

It is easy to see that the range of possible values of **X̂** in NPAt exceeds the range of NF **X̃** by β^(t−1) times.

To avoid a sharp "cut-off" of the range of representable values near zero when **e = e_min** and to ensure the so-called gradual underflow, the IEEE 754 standard introduces special subnormal numbers in which **K̃ ≠ 0** at **e = e_min** with leading zeros in the most significant digits. Special algorithms are developed for working with subnormal numbers.

In NPAt, such a problem does not exist, since **K̂** can take any value from 1 to β^t − 1 or zero for all **e** from **e_min** to **e_max**.

The maximum value that can be written into the **w**-digit area of the operational register is  
**W = β^w − 1.**

Since the maximum value that can be written into the **w**-digit area of the operational register for NF **X̃** is  
**e_max = W,**  

and, taking into account the sign **s**, the minimum exponent value will be  
**e_min = −e_max.**  

Then the exponent **e** can take any value in the interval  
**−W ≤ e < W.**

For NPAt **X̂**, the rounding coefficient **q** is written into the **w**-digit area of the operational register, which also takes any value in the interval  
**−W ≤ q < W.**

Its maximum value will be  
**q_max = W,**  

Taking into account the sign **s**, the minimum value of the rounding coefficient **q** will be  
**q_min = −W.**  

Since **q = e − t**, the rounding coefficient **q** can take any value from the interval  
**−(e_max + t) ≤ q ≤ e_max − t.**

Since when **K̂ = 0** in NPAt **X = 0**, taking into account that **−e_max = e_min**, in the case **e_min < t** and **K̂ ≠ 0**, **X** can be considered an infinitely small number.

If **X** is represented in NPAt format — **X̂ = S·K̂·μ**, then to represent **X** in NF **X̃**, the radix point in **K̂** must be shifted **t** digits to the left and the rounding coefficient **q** in **μ = β^q** must be increased by **t**.

For example, NPAt **X̂ = 123·10⁻²**, in which **t = 3**, **q = −2**, will be represented in NF **X̃** as **X̃ = 0.123·10¹**.

### Properties of the sum of signed NF numbers

For the sum of signed addends in NF **X̃**, the following statements hold.

If one of the addends, or the sum of addends, when summing NF **X̃**, is a number with exponent **e > e_max**, then this is a special case and such a number is called positive infinity (+∞).

If one of the signed addends, or the sum of addends, when summing NF **X̃**, is a number with exponent **e < e_min** and **K̃ ≠ 0**, then this is a special case and such a number is called negative infinity (−∞).

If the sum of addends in NF **X̃** is a number with exponent **e < e_min** and **K̃ < β/2**, then such a number is taken as zero. Since in NF it is impossible to distinguish ±0 from the exact zero value, 0 is a special case.

### Properties of the sum of NPAt numbers

For the sum of signed NPAt **X̂**, the following statements hold.

If one of the signed addends, or the sum of addends in NPAt format **X̂**, is a number with rounding coefficient **q > q_max**, then this is a special case and such a number for positive **K̂** is taken as positive infinity (+∞). For negative **K̂** it is taken as negative infinity (−∞).

If one of the signed addends, or the sum of addends in NPAt **X̂**, is a number with rounding coefficient **q < q_min** and **K̂ ≠ 0**, then this is a special case and such a number for positive **K̂** is called positive infinitely small (+), and for negative **K̂** — negative infinitely small (−).

The sum of exact addends in NPAt **X̄** equals the exact **X̄** if their sum was not rounded. In this case, the exact **X̄** is an exact zero.

### Comparison of summation results of NF numbers and numbers in NPAt format

For example, Table 1 presents sums **X = X₁ + X₂** of some decimal numbers **X₁** and **X₂**, which are represented in normalized form — **X̃₁**, **X̃₂** and in NPAt format — **X̂₁** and **X̂₂**.

**Table 1.** β = 10, t = 2, W = 3

<table>
  <tr>
    <td colspan="4" align="center"><b>Sum of normalized numbers</b></td>
    <td colspan="4" align="center"><b>Sum of numbers in NPAt format</b></td>
  </tr>
  <tr>
    <td><b>1</b></td>
    <td>X̃₁</td>
    <td>X̃₂</td>
    <td>X̃₁ + X̃₂</td>
    <td>q₁ = e₁−t</td>
    <td>X̂₁</td>
    <td>X̂₂</td>
    <td>X̂₁ + X̂₂</td>
  </tr>
  <tr>
    <td>2</td>
    <td>0.23·10⁻¹</td>
    <td>0.01·10⁻¹</td>
    <td>0.24·10⁻¹</td>
    <td>-3</td>
    <td>23·10⁻³</td>
    <td>1.2·10⁻³</td>
    <td>24·10⁻³</td>
  </tr>
  <tr>
    <td>3</td>
    <td>0.12·10³</td>
    <td>0.23·10³</td>
    <td>0.35·10³</td>
    <td>1</td>
    <td>12·10¹</td>
    <td>23·10¹</td>
    <td>35·10¹</td>
  </tr>
  <tr>
    <td>4</td>
    <td>0.12·10⁻¹</td>
    <td>−0.12·10⁻¹</td>
    <td>ОС(0)</td>
    <td>-3</td>
    <td>X̄₁ = 12·10⁻³</td>
    <td>X̄₂ = −12·10⁻³</td>
    <td>0</td>
  </tr>
  <tr>
    <td>5</td>
    <td>0.23·10²</td>
    <td>−0.22·10²</td>
    <td>0.10·10¹</td>
    <td>0</td>
    <td>23·10⁰</td>
    <td>−22·10⁰</td>
    <td>1·10⁰</td>
  </tr>
  <tr>
    <td>6</td>
    <td>0.12·10⁵</td>
    <td>ОС(+∞)</td>
    <td>0.23·10³</td>
    <td>ОС (+∞)</td>
    <td>3</td>
    <td>12·10³</td>
    <td>0.00·10³ | 12·10³</td>
  </tr>
  <tr>
    <td>7</td>
    <td>0.12·10⁻³</td>
    <td>0.23·10⁻³</td>
    <td>0.35·10⁻³</td>
    <td>-5 < −W</td>
    <td>ОС (inf small)</td>
    <td>ОС (inf small)</td>
    <td>ОС (inf small)</td>
  </tr>
  <tr>
    <td>8</td>
    <td>0.12·10⁶</td>
    <td>ОС(+∞)</td>
    <td>0.23·10³</td>
    <td>ОС (+∞)</td>
    <td>4 > W</td>
    <td>ОС (+∞)</td>
    <td>0.0023·10³ | ОС (+∞)</td>
  </tr>
</table>

### Summary

1. Any normalized floating-point number **X̃ = S·0.K̃·μ**, where **K̃** — integer, can be represented approximately as NPAt **X̂ = S·K̂·μ**, or exactly as **X̄ = S·K̄·μ**, where **X̄** — integer non-rounded number, which is a subset of NPAt **X̂**.
2. **K̂** and **K̄** — integers in the interval from 0 to **K̂_max**, which define a monotonically increasing function **X̂ = S·K̂·μ** from **X̂_min = β^(-q_max)** to **X̂_max = β^(q_max + t)**.
3. Since **X̄** is a subset of **X̂**, in NPAt format we have two data types — approximate numbers **X̂** with a subset of exact numbers **X̄** and non-numbers (NaN).
4. Parameters of signed NPAt — (**S**, **K**, **s**, **e**) are integers or zero, so all computations can be performed in two's complement code.
5. If in the exact number **X̄ = S·K̄·μ** **K = 0**, then **X̄ = 0** is not a special case. In NPAt zero is defined explicitly.
6. The presence of explicit zero in NPAt allows introducing the concepts of minus and plus infinitely small numbers, which is in demand in many mathematical proofs.
7. In NPAt computations, normalization is not required, as the fractional part formed by shifting **K** goes beyond the register grid and **K** is rounded to the nearest integer.
8. The computation precision **t** in NPAt is set by the user and does not affect the computation algorithm.
9. The NPAt format allows very simple implementation of computations on CPU, yielding results identical to computations on FPU.

Below is a demonstration algorithm showing how to implement summation of arbitrary input signed numbers **x₁** and **x₂** in NPAt format on an integer processor, with the same results as when summing the same numbers in float or double format.

The algorithm is implemented in C++ and available at — npap-algorithm

The program performs summation  
**R = ±x₁ ± x₂·(Z−1)**.

Where the number of summation cycles **Z**, input ±**x₁** and ±**x₂** can vary arbitrarily.

It can be seen that the summation results in NPAt, at **t = 24** or **53** and arbitrary **Z**, are bit-identical to the summation results for the same numbers in float or double formats.
