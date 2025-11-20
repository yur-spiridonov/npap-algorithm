# An Alternative Algorithm for Summing Any Decimal Numbers in Binary Arithmetic
**NPAp — Number with Point After p: Full IEEE 754 Compatibility via Integer Arithmetic**

Any number **X** can be represented with infinite precision as an infinite number of integer **K** (0 ≤ K < ∞) infinitesimal intervals **ɛ**. The interval **ɛ** can be expressed as decimal **ɛ₁₀**, binary **ɛ₂**, or, in general, **ɛ** in any number system. For decimal **ɛ₁₀** → 0 and binary **ɛ₂** → 0 **K₁₀** → ∞, **K₂** → ∞ and then decimal **±K₁₀ɛ₁₀** with infinite precision will be equal to binary **±K₂ɛ₂**. In other words, any infinitely precise decimal **X₁₀** with infinite precision equals its binary equivalent **X₂**.

In numerical arithmetic, **X** is a number with finite **K** and **ɛ**. For incommensurable decimal and binary number systems — **X₁₀ ≠ X₂**.

In any number system, **K** is defined as:
K = Rnd(X / ɛ)
textwhere **Rnd** means rounding to the nearest.

As **ɛ**, we take the minimum possible value that is guaranteed to be less than or equal to the minimum expected result **Xmin**. For example, if **β = 10** **Xmin = 0.00000123**, then for simplicity of calculations, it is most convenient to take **ɛ = 0.000001 = 10⁻⁶**. If **β = 10** **Xmin = 123**, then it is most convenient to take **ɛ = 100 = 10²**.

Thus:
ɛ = βᵉᵐⁱⁿ
textwhere **β** — number system (base), **emin** — minimum possible exponent.

All digits in **X**, starting from the first non-zero, including sometimes trailing zeros, are called **significant digits**.

Any **X** can be represented in exponential form:
X = ±0.Kβᵉ
textwhere **±0.K** — mantissa of number **X**, **e** — exponent, determining the position of the **radix point** in **X** written in natural form, relative to the position of the **radix point** in **X** written in exponential form.

If the first most significant digit in **K** is non-zero, then **X** is called a **normalized number**.

As a rule, arithmetic calculations are performed with accuracy to a given number of significant digits **p**. Calculations will be correct if these **p** digits are reliable.

An approximate **X̃**, which differs from the true value of **X** by no more than half the least significant digit of **K**, is called **reliable**.

**Representation of normalized X in the processor's operational register**

In computer calculations, operational registers with a finite number of digits are used.

To perform arithmetic operations on normalized numbers, **X = ±0.Kμ** must be written to the processor's operational register. For this, four areas are allocated in the operational register — **S**, **m**, **s**, **w**. Where one bit is allocated for the sign **S** of the number, **m** bits are allocated for the integer **K** of the fractional mantissa **0.K**, one bit **s** is allocated for the sign of the exponent **e**, and **w** bits are allocated for **e**.

The location of the areas in the register can be arbitrary and depend on the implementation.

In the **m**-bit area of the register, the maximum integer can be written — **Kₘₐₓ = βᵐ -1**.

In the **w**-bit area of the register, the maximum integer can be written — **eₘₐₓ = βʷ -1**.

If all **p** significant digits of the mantissa **0.K** can be written to the **m**-bit area of the register, and all digits of **e** can be written to the **w**-bit area, then **X** is called **representable**. For representable numbers — **K ≤ Kₘₐₓ** and **e ≤ eₘₐₓ**.

If **p** digits of **K** cannot be written to the **m**-bit register without losing the least significant digits and/or all digits of the exponent **e** of number **X** cannot be written to the **w**-bit area, then such **X** is called **non-representable**.

**X** is a non-representable number if **K > Kₘₐₓ** and/or **e > eₘₐₓ**.

Digits of the mantissa **0.K** that go beyond the **w** digits of the operational register are discarded, and **0.K** is rounded to the nearest **0.K′** with **w** significant digits. As a result, the register records:
X̃ = ±0.K′μ
textIf **|X̃| > Kₘₐₓβᵉᵐᵃˣ**, then **X̃** is sometimes called infinitely large.

If for **X̃ = ±0.K′βᵉᵐⁱⁿ** the first digit in **K′** is zero, then **X̃** in the classical representation is considered a **subnormal number**.

Sometimes, for example in the IEEE754 standard, to obtain the ability to write additional information to computer memory, such as — **NAN**, **0**, **∞** and other auxiliary information, **emin = (emax -1)** is accepted.

**Format of a number with a radix point after the p-th significant digit in X**

It is easiest to count significant digits in **X** in a normalized number, in which the first non-zero digit is located immediately after the **radix point**.

If the **radix point** in the normalized **X** is shifted **p** digits to the right and the exponent is adjusted, then we get:
X = ±K′βᵉ⁻ᵖ
textwhere **K′** can be a fractional number with an integer part consisting of **p** digits.

Rounding **K′** to an integer **K̂** with **p** significant digits, we get:
X̂ = ±K̂μ
text**X̂**, written in this way, is an approximate number with a scale factor:
μ = βᵛ
textwhere **v = e – p** is the exponent determining the position of the **radix point** in **X** written in natural form, relative to the position of the **radix point** in **X**, offset from the **radix point** of the normalized number by **p** digits to the right.

During transformations, the scale factor **μ** can change when the parameter **v** changes. For example, when aligning exponents during addition.

If after shifting the **radix point** in the normalized **X** **p** digits to the right, it turns out that **K > Kₘₐₓ**, then the least significant digits of **K** that go beyond the **m**-th least significant digit of the register must be discarded by rounding to the nearest integer **K̂**. As a result, we will have:
X̂ = ±K̂βᵉ
text**X̂** means that in the process of transforming **X**, non-zero significant digits in **K** were lost and **X̂** is an approximate number with an integer mantissa **±K̂**, in which there are **p** significant digits.

In the case where **X** contains **p ≤ m** significant digits, then the normalized **X** will be an exact number. Shifting the **radix point** in such **X** **p** digits to the right does not lead to the loss of non-zero digits, and such a number remains exact.

Exact numbers will be denoted by the symbol **X̄**. The exact **X̄** is a special case of **X̂**.

**X̂** and **X̄** with a **radix point** after the **p**-th significant digit in the normalized number will be briefly called a **number with point after p (NPAp)** and denoted — **X̂** or **X̄**.

**Example.** If **X = 0.001234**, then the normalized **X = 0.1234·10⁻²**, in which **e = -2**. If **p = 3**, then **v = -2 - 3 = -5**. Then **X = 123.4·10⁻⁵** and **X̂ = 123·10⁻⁵**, **μ = 10⁻⁵**. If **X = 0.00123**, then the normalized **X = 0.123·10⁻²**, in which **e = -2**. If **p = 3**, then **v = -2 – 3 = -5**. Then **X = 123·10⁻⁵** and **X̄ = 123·10⁻⁵**, **μ = 10⁻⁵**.

**Representation of NPAp in the operational register**

In the operational register for writing **NPAp**, as well as for normalized numbers, four areas are allocated — **S**, **m**, **s**, **w**. In **NPAp**, these register areas are interpreted as: **S** — sign of the number, **m**-area of the operational register, into which **K̂** or **K̄** is written; **s** — sign of the parameter **v**;

The values **p** and **β** in the operational register are not stored, but are constants that are set based on the task conditions. These constants do not participate in calculations, but are used only for converting a normalized number to **NPAp**, as well as for outputting the calculation result to the terminal in an acceptable form.

**Properties of NPAp and normalized floating-point numbers**

The interval that covers positive floating-point numbers of the form **X = 0.Kβᵉ** is divided into two areas. The area of normalized numbers:
βᵉᵐⁱⁿ ≤ X < Kₘₐₓβᵉᵐᵃˣ
textAnd the area of denormalized (subnormal) numbers:
βᵉᵐⁱⁿ⁻ᵖ ≤ X < Kₘₐₓβᵉᵐⁱⁿ – ᵖ
textIn this representation, zero is an exceptional case; **emin = - emax**.

The interval of positive numbers that **NPAp** covers is:
βᵛᵐⁱⁿ ≤ X̂ < Kₘₐₓβᵛᵐᵃˣ
textwhere **vmax = emax - p**. When **K = 0**, **X̂ = 0**. Zero is represented here in explicit form.

The maximum number of digits of the mantissa **K** of **NPAp** that can be written to the **p**-bit area of the operational register is determined by the maximum number of **p** bits of this register area.

The maximum **Kₘₐₓ** that can be written to the **p**-bit area is:
Kₘₐₓ = βᵖ – 1
textTo write to the operational register **X** with **K > Kₘₐₓ**, you need to reduce **K** and increase the scale **μ**.

The scale **μ** for normalized **X** can change in the range:
βᵉᵐⁱⁿ ≤ μ ≤ βᵉᵐᵃˣ
textThe scale **μ** for **NPAp** can change depending on the implementation (set **p**) in the range:
βᵛᵐⁱⁿ ≤ μ ≤ βᵛᵐᵃˣ
textwhere **vmax = emax + p**.

The maximum number **p** of possible significant digits of **NPAp X̂** is set by the user in accordance with the task conditions and the number of bits of the operational register under **K**. The value **p** in calculations with **NPAp** is not explicitly present.

**Example of choosing parameters for NPAp**

Let's represent **NPAp** with accuracy to 2 significant digits for decimal numbers (**β = 10**) that lie in the interval **0.00123…156**. In normalized form, they will be represented as — **0.123·10⁻²…156·10³**, where **emin = -2**, **emax = 3**. To ensure accuracy to 2 significant digits, at least two bits must be allocated in the operational register for writing **K** (**p = 2**). In a two-bit register, all integers with two significant digits from one to **Kₘₐₓ = 10² – 1 = 99** can be guaranteed to be written. For the left and right boundaries of **NPAp**, we find — **vmin = emin - p = -2-2 = -4**, **vmax = emax - p = 3-2 = 1**. In **NPAp** format, the interval for the numbers under consideration will look like — **12·10⁻⁴…16·10¹**. It follows that to represent decimal **NPAp** that lie in the interval **0.00123…156** with accuracy to 2 significant digits, the operational register must provide one bit for writing the sign **S** of the number **X**, **p = 2** bits for writing the mantissa **K̂**, one bit for the sign **s** of the parameter **v**, as well as **w** bits for **v**. The parameter **v** is determined by choosing the maximum value among **|vmin|** and **|emax|**. In our case, the maximum value is **|emin| = 4**. Since in one decimal bit (**w = 1**) the maximum **10¹-1= 9** can be written, then **|emin| = 4** can be guaranteed to be written to this bit. The interval of decimal numbers **0.00123…156** with accuracy to **p = 2** significant digits, as a result of conversion to **NPAp** format, will have the form — **12·10⁻⁴…16·10¹**. To have all **NPAp** from this interval represented in the operational register, the register must have a bit depth of at least — **S+p+s+w = 1+2+1+1 = 5**.

**Examples of summing NPAp**

The summation algorithm implemented on **NPAp** strictly obeys the arithmetic rules of addition of finite numbers, which in most cases give an approximate result.

Let's find the sum of normalized — **X = X₁ + X₂** and **NPAp** — **X̂ = X̂₁ + X̂₂** in a decimal adder (**β = 10**) with **p = 2**.

Further, the symbol **« ⇒ »** means conversion from the format of a normalized number to **NPAp**.

1. Let **X₁ = 0.0234**, **X₂ = 0.00123**.  
   **The correct sum with accuracy p = 2 will be** —  
   **X = 0.0234 + 0.00123 = 0.02463 ≈ 0.25·10⁻¹**.  
   **The sum of normalized numbers rounded to p = 2 will be** —  
   **X₁ = 0.234·10⁻¹**, **X₂ = 0.123·10⁻²**, where **e₁ = -1**, **e₂ = -2**.  
   **X̃₁ = 0.23·10⁻¹**, **X̃₂ = 0.12·10⁻²**  
   **X̃₁ + X̃₂ = 0.23·10⁻¹ + 0.12·10⁻² = 0.23·10⁻¹ + 0.012·10⁻¹ = 0.242·10⁻¹ ≈ 0.24·10⁻¹**  
   **Sum of NPAp X̂ = X̂₁ + X̂₂ with accuracy p = 2**:  
   **X̃₁ = 0.23·10⁻¹ ⇒ X̂₁ = 23·10⁻¹⁻ᵖ**, **X̃₂ = 0.12·10⁻² ⇒ X̂₂ = 12·10⁻²⁻ᵖ**.  
   **X̂₁ + X̂₂ = 23·10⁻¹⁻ᵖ + 12·10⁻²⁻ᵖ = 23·10⁻¹⁻ᵖ + 1.2·10⁻¹⁻ᵖ ≈ 24·10⁻¹⁻ᵖ**, hence **X̂ = 24·10⁻³**

2. Let **X₁ = 1230**, **X₂ = 234**.  
   **The correct sum with accuracy p = 2 will be** —  
   **X = 1230 + 234 = 1464 ≈ 15·10²**  
   **The sum of normalized numbers rounded to p = 2 will be** —  
   **X₁ = 0.123·10⁴**, **X₂ = 0.234·10³**, where **e₁ = 4**, **e₂ = 3**.  
   **X̃₁ = 0.12·10⁴**, **X̃₂ = 0.23·10³**  
   **X̃₁ + X̃₂ = 0.12·10⁴ + 0.23·10³ = 0.12·10⁴ + 0.023·10⁴ = 0.143·10⁴ ≈ 0.14·10⁴ = 14·10²**  
   **Sum of NPAp X̂ = X̂₁ + X̂₂ with accuracy p = 2 will be** —  
   **X̃₁ = 0.12·10⁴ ⇒ X̂₁ = 12·10⁴⁻ᵖ**, **X̃₂ = 0.23·10³ ⇒ X̂₂ = 23·10³⁻ᵖ**.  
   **X̂₁ + X̂₂ = 12·10⁴⁻ᵖ + 23·10³⁻ᵖ = 12·10⁴⁻ᵖ + 2.3·10⁴⁻ᵖ ≈ 14·10⁴⁻ᵖ**, hence **X̂ = = 14·10²**

3. Let **X₁ = 0.00123**, **X₂ = -0.00122**.  
   **The correct difference with accuracy p = 2 will be** —  
   **X = 0.00123 - 0.00122 = 0.00001 = 10⁻⁵**  
   **The sum of normalized numbers rounded to p = 2 will be** —  
   **X₁ = 0.123·10⁻²**, **X₂ = 0.122·10⁻²**, where **e₁ = -2**, **e₂ = -2**.  
   **X̃₁ = 0.12·10⁻²**, **X̃₂ = 0.12·10⁻²**  
   **X̃₁ - X̃₂ = 0.00·10²** — exceptional case  
   **Sum of NPAp X̂ = X̂₁ + X̂₂ with accuracy p = 2 will be** —  
   **X̃₁ = 0.12·10⁻² ⇒ X̂₁ = 12·10⁻²⁻ᵖ**, **X̃₂ = 0.12·10⁻² ⇒ X̂₂ = 12·10⁻²⁻ᵖ**.  
   **X̂₁ + X̂₂ = 12·10⁻²⁻ᵖ - 12·10⁻²⁻ᵖ = 0·10⁻²⁻ᵖ**, hence **X̂ = 0·10⁻⁴ = 0** — explicit zero.  
   Subtraction (12-12) can be performed in two's complement code.

4. Let **X₁ = 23**, **X₂ = -22**.  
   **The correct difference** — **X = 23 - 22 = 1**  
   **The sum of normalized numbers rounded to p = 2 will be** —  
   **0.23·10² – 0.22·10² = 0.01·10² = 0.1·10¹** (after normalization)  
   **Sum of NPAp X̂ = X̂₁ + X̂₂ with accuracy p = 2 will be** —  
   Then **X₁ = 0.23·10² ⇒ X̂₁ = 23·10²⁻ᵖ**, **X₂ = 0.22·10² ⇒ X̂ = 22·10²⁻ᵖ**.  
   Find — **23·10²⁻ᵖ - 22·10²⁻ᵖ = 1·10²⁻ᵖ**, hence **X̂ = 1·10⁰**.  
   Subtraction (23-22) can be performed in two's complement code, since 23, 22 — integers.

## Summary

1. Any normalized floating-point number **X = ±0.Kμ**, where **K** is an integer, can be represented in **NPAp** format as **X̂ = ±K̂μ** or **X̄ = ±K̄μ**, where **μ** is an integer scale factor.

2. The range of representable numbers in **NPAp** lies in the interval **βᵛᵐⁱⁿ ≤ |X̂| < Kₘₐₓβᵛᵐᵃˣ** or equals zero, where **vₘₐₓ = eₘₐₓ + p**.

3. Integer numbers in **NPAp** have the form **X̄ = ±K̄β⁰** and are a special case of the format. Additionally:  
   - **K = 0** → explicit zero (independent of v and S)  
   - **v > vₘₐₓ**, **K ≠ 0** → **+∞**  
   - **v < vₘᵢₙ**, **K ≠ 0** → **−∞**  
   Thus, **NPAp** naturally supports **±∞** via exponent overflow/underflow — without special reserved bit patterns.

4. All **NPAp** parameters (**S**, **K**, **s**, **v**) are integers or zero → all operations use two’s-complement integer arithmetic.

5. For an exact number **X̄ = Sμ·K = 0**, the value is **explicit zero** — reliably detectable without normalization.

6. Normalization in **NPAp** is required **only on exponent overflow/underflow**. Leading zeros in **K** do not require renormalization (they do not change the value).

7. **NPAp** computations can be fully implemented on a CPU using **only integer instructions** — **no FPU required**.

## Test Program

See the full C++ implementation: [`npap_test.cpp`](npap_test.cpp)

The program demonstrates **bit-identical results** with IEEE 754 `double` on real-world data that cannot be exactly represented in binary.

## Publication

**Iouri Spiridonov**  
*Alternative Algorithm for Summing Decimal Numbers in Binary Arithmetic*  
**TechRxiv**, 17 November 2025  
DOI: [10.36227/techrxiv.175978514.43264211/v1](https://doi.org/10.36227/techrxiv.175978514.43264211/v1)

@preprint{Spiridonov2025,
  author    = {Spiridonov, Iouri},
  title     = {Alternative Algorithm for Summing Decimal Numbers in Binary Arithmetic},
  year      = {2025},
  month     = nov,
  publisher = {TechRxiv},
  doi       = {10.36227/techrxiv.175978514.43264211.v1},
  url       = {https://doi.org/10.36227/techrxiv.175978514.43264211.v1},
  note      = {Preprint}
}
