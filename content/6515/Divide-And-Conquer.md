## DC1: Fast Integer Multiplication

- DC = Divide and Conquer (recursive)
- Fast Multiplications (n-bit integers, with n~1000)
    - Input: n-bit integers x and y, assume n is a power of 2
    - Goal: compute z=xy
    - Motivation: setting where multiplication is O(n^2) expensive, addition/subtraction is O(n) cheap
    - D&C idea: break input into 2 halves (1st n/2 bits, and 2nd n/2 bits)
        - Can improve to O(n^(log(3))) by using Gauss transformation w/ imaginary numbers, outlined in lecture
- FFT - Fast Fourier Transform Overview
- Complex Numbers


## DC2 - Linear-Time Median

- Problem: given an unsorted list A of n numbers, find the median of A (i.e. `n/2`th number). For an odd number `2l+1`, median is `l+1`th number
    - Trivial algorithm: sort list, then pick index. `O(nlogn)` time
- Improved D&C algorithm: `O(n)` time, basic approach "QuickSort" style
	```text
    FastSelect to find Median
    
    input: array A of size n, int k where 1 <= k <= n
    output: kth smallest element of A
    
    1. Linear pass, pick a good pivot p
        - Break A into floor(n/5) groups of 5 elements each
        - For each group G_i, sort G_i (constant time since size 5) and get median set S
        - Set pivot p to median of S (by recursively calling FastSelect with S, n/10)
    2. Linear pass, partition array into <p, =p, >p
    3. Recursively search in <p OR \>p buckets depending on median index k = n/2 or (n-1)/2 for odd n.
    ```
	If k<= len(<p array) then search <p for kth element, else if k > (len(<p array) + len(=p array)) then search >p for (k - len(<p array) - len(=p array))th element.
        Else it's in =p array, so return p
    
    T(n) = T(3/4 n) + T(n/5) + O(n)
    => O(n) time
    ```
- Picking a good pivot (in linear time): keep recurrence `O(n)` based on Master's Theorem, thus pivot that consistently partitions into at worst 3/4th of array (technially 99/100 would still work, though aim for better)
- Interesting concept: considering computational "slack" when considering runtime (e.g. here, picked n/5 since have 0.24 of "slack" available)

## DC3: Solving Recurrences

- Solving recurrences approach:
    1.  Bind solution w/ constants (remove O notation, turn into math problem)
        - e.g. For some constant c>0, T(n) <= 4T(n/2) + cn, T(1) <= c
    2.  Expand out terms, then abstract with math patterns
    3.  Find substitution to reduce T term to T(1) so it can be removed
    4.  Reduce back to O notation (look for dominant terms)
- Divide and conquer examples:
	```text
    T(n) = 2T(n/2) + O(n) = O(nlog(n))
    T(n) = 4T(n/2) + O(n) = O(n^2) // Runtime for SlowMultiply
    T(n) = 3T(n/2) + O(n) = O(n^log(3)) // Runtime for FastMultiply
    T(n) = T(3/4n) + O(n) = O(n)
    ```
- Use math techniques (outlined in lecture) to change problem into form that can be solved using methods outlined
- General Recurrence (Master's Theorem)
	```text
    Given constants a>0, b>1
        and T(n) = aT(n/b) + O(n)
    
    if a>b, O(n^log_b(a))
    if a=b, O(nlog_b(n))
    if a<b, O(n)
    ```

## DC4: Polynomial Multiplication (FFT Pt 1)

- Given two polynomials `A(x)` and `B(x)`, want `C(x)=A(x)B(x)`
    - Let `A(x) = a0 + a1 x + a2 x^2 + ... + a_(n-1) x^(n-1)`, same for `B(x)`
    - Let `C(x) = c0 + c1 x + c2 x^2 + ... + c_(2n-2) x^(2n-2)`
        - Here, `C(x)` is convolution of `A(x)` and `B(x)` (remember: calculate by "sliding")
- Problem reframed generally: Given `a = (a0, a1, ..., a_(n-1)` and `b = (b0, b1, ..., b_(n-1)`, compute `c = a * b = (c0, c1, ..., c_(2n-2))` where `*` is the convolution
    - Naive: O(k) => O(n^2) time
    - Improved: O(n logn) time using FFT
- A Polynomial has two equivalent representations:
	1.  coefficients (e.g. `a = (a0, a1, ..., a_(n-1))`)
	2.  values (e.g. `A(x1), A(x2), ..., A(x_n)`, where `A(x)` is a point)
- FFT converts coefficient values for well chosen values (i.e. choice of points)
- Key idea: multiplying polynomials is easy when polynomial is represented as values (O(n) total time given `A(x1),...,A(x_2n)` and `B(x1),...,B(x_2n)`).
	- Thus, use FFT to get values. Then multiply in linear time. Then use FFT to convert back to coeff
- FFT: if we pick points that satisfy +/- property (outlined in lecture), can restructure problem as divide and conquer algorithm
- Complex Numbers
    - `z = a+bi` in complex plane (a,b) can be represented as polar coordinates (r,θ), where `(a,b) = (rcosθ, rsinθ)`
	- Euler's formula: `z = r(cosθ + isinθ) = re^(iθ)`
    - Polar coordinates are convenient for multiplication, since `z1 * z2 = (r1, θ1) * (r2, θ2) = (r1*r2, θ1 + θ2)`
        - Thus, opposite is just the reflection `z1 * -1 = (r1, θ1) * (1, π) = (r1, θ1 + π)`
- Complex Roots: nth complex roots of unity are set of `z` where `z^n = 1`
	- E.g. for n=2, nth roots of unity are 1, -1. For n=4, its 1, -1, i, -i
	- Solving using polar representation: `z^n = 1 = (1, 2πj)` for integer `j`, which is the unit circle! `r=1` and `θ = 2π/n * j` for `j = 0,1,...,n-1`
	- Can think of every `θ = 2π/n` step as root of unity. Let `ω_n = (1, 2π/n) = e^(2πi/n)` which are polar coordinates, then applying Euler's. For every `θ = 2π/n` step, `ω_n -> (ω_n)^2 -> ... -> (ω_n)^(n-1)` because polar math.
		- Thus `(ω_n)^j = e^(2πij/n)`
	- Note: `(ω_n)^n = (ω_n)^0 = 1 + 0i = 1`
- Key Properties of nth roots of unity
	- `(nth roots)^2 = (n/2)nd roots`. E.g. for n=16, `(ω_16)^4 = (ω_8)^2 = (ω_4)^1`
		- For n as a power of 2, original input can be split to satisfy +/- property (n maps to n/2)
	- \+/\- property: `(ω_n)^j = -(ω_n)^(j + n/2)`
		- For even n, original input can be split to satisfy +/- property (1 maps to n+1)
- When evaluating FFT, choose nth roots of unity to exploit these properties

## DC5 - FFT Definition (FFT Part 2)

- Goal: Evaluate polynimal `A(x)` of deg &lt;= n-1 at n points (i.e. convert coefficients -&gt; values), with n points being the nth roots of unity.
- Process (watch lectures for details):
	```text
	input: polynomials A, B as coefficients
	output: polynomial C as coefficients, which is A * B

	1. Calculate A, B at nth roots of unity (coeff -> values)
	2. Get values of C at nth roots of unity (which is A(w) * B(w) for all w)
	3. Calculate coeff of C from values at nth roots of unity (values -> coeff)

	Total: O(nlog(n)) time!
	```
- Based on interesting math, to get "reverse" of FFT, you run FFT at a different point of unity (same root). Neat!
