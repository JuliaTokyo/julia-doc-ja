.. _man-mathematical-operations:

.. currentmodule:: Base

**************************************************
 数学的演算と初歩的関数
**************************************************

Julia はその数値的プリミティブ型すべてについての基本的算術演算子とビット演算子の
完全なコレクションを備えており、加えて標準的数学関数の包括的なコレクションの
可搬的・効率的な実装を提供しています。

算術演算子
--------------------

以下の `算術演算子 (英語)
<https://en.wikipedia.org/wiki/Arithmetic#Arithmetic_operations>`_
はすべてのプリミティブ数値型でサポートされています:

==========  ============== ======================================
表現         名称            概要
==========  ============== ======================================
``+x``      単項プラス       恒等演算
``-x``      単項マイナス     値をその加法逆元に対応させる
``x + y``   二項プラス       加算する
``x - y``   二項マイナス     減算する
``x * y``   かける          乗算する
``x / y``   わる            除算する
``x \ y``   逆にわる         ``y / x`` と同値
``x ^ y``   累乗            ``x`` を ``y``\ 乗する
``x % y``   剰余            ``rem(x,y)`` と同値
==========  ============== ======================================

さらに ``Bool`` 型における否定もサポートされています:

==========  ============== ============================================
表現         名称            概要
==========  ============== ============================================
``!x``      否定            ``true`` を ``false`` にし、逆もまた同様
==========  ============== ============================================

Julia の昇格システムは引数の型が混合であっても算術演算子が自然かつ自動的に
「ちゃんと動く」ようになっています。 昇格システムの詳細については
:ref:`man-conversion-and-promotion` をご覧ください。

算術演算子を用いたいくつかの単純な例を示します:

.. doctest::

    julia> 1 + 2 + 3
    6

    julia> 1 - 2
    -1

    julia> 3*2/12
    0.5

(慣例として、我々は結びつきがあまりきつくない演算子はあまりきつくなく間を空けることが
多いですが、統語論的制約はありません。)

ビット演算子
-----------------

以下の `ビット演算子 (英語) <https://en.wikipedia.org/wiki/Bitwise_operation#Bitwise_operators>`_
はすべてのプリミティブ整数型でサポートされています:

===========  =========================================================================
表現   名称
===========  =========================================================================
``~x``       ビット単位 not
``x & y``    ビット単位 and
``x | y``    ビット単位 or
``x $ y``    ビット単位 xor (排他的 or)
``x >>> y``  `論理シフト (英語) <https://en.wikipedia.org/wiki/Logical_shift>`_ 右
``x >> y``   `算術シフト (英語) <https://en.wikipedia.org/wiki/Arithmetic_shift>`_ 右
``x << y``   論理/算術シフト 左
===========  =========================================================================

ビット演算子を用いたいくつかの例を示します:

.. doctest::

    julia> ~123
    -124

    julia> 123 & 234
    106

    julia> 123 | 234
    251

    julia> 123 $ 234
    145

    julia> ~UInt32(123)
    0xffffff84

    julia> ~UInt8(123)
    0x84

更新演算子
------------------
すべての二項算術演算子とビット演算子には、
演算の結果を左のオペランドに戻す更新版もあります。
二項演算子の更新版は、演算子の直後に ``=`` を置くことで作られます。
例えば、 ``x += 3`` と書くことは ``x = x + 3`` と書くことと同値です::

      julia> x = 1
      1

      julia> x += 3
      4

      julia> x
      4

すべての二項算術演算子とビット演算子の更新版は::

    +=  -=  *=  /=  \=  ÷=  %=  ^=  &=  |=  $=  >>>=  >>=  <<=


.. note::
   更新演算子は変数を左辺に再バインドします。
   結果として、変数の型は変わることがあります。

   .. doctest::

      julia> x = 0x01; typeof(x)
      UInt8

      julia> x *= 2 #Same as x = x * 2
      2

      julia> isa(x, Int)
      true

.. _man-numeric-comparisons:

数値比較
-------------------

標準比較演算子はすべてのプリミティブ数値型について定義されています:

=================== ========================
演算子               名称
=================== ========================
:obj:`==`           等しい
:obj:`\!=` :obj:`≠` 不しくない
:obj:`<`            より小さい
:obj:`<=` :obj:`≤`  より小さいまたは等しい
:obj:`>`            より大きい
:obj:`>=` :obj:`≥`  より大きいまたは等しい
=================== ========================

いくつかの簡単な例を示します:

.. doctest::

    julia> 1 == 1
    true

    julia> 1 == 2
    false

    julia> 1 != 2
    true

    julia> 1 == 1.0
    true

    julia> 1 < 2
    true

    julia> 1.0 > 3
    false

    julia> 1 >= 1.0
    true

    julia> -1 <= 1
    true

    julia> -1 <= -1
    true

    julia> -1 <= -2
    false

    julia> 3 < -0.5
    false

整数はビットの比較という標準的な方法で比較されます。
浮動小数点数は `IEEE 754 (英語) <https://en.wikipedia.org/wiki/IEEE_754-2008>`_ に従って比較されます:

-  有限な数は通常の方法で順序付けられます。
-  正のゼロは負のゼロと等しく、それより大きくはありません。
-  ``Inf`` はそれそのものと等しく、 ``NaN`` 以外のあらゆるものより大きいです。
-  ``-Inf`` はそれそのものと等しく、 ``NaN`` 以外のあらゆるものより小さいです。
-  ``NaN`` はそれそのものを含むあらゆるものに対して等しくなく、小さくもなく、大きくもありません。

最後の点は潜在的に驚くべきことであり、注目に値します:

.. doctest::

    julia> NaN == NaN
    false

    julia> NaN != NaN
    true

    julia> NaN < NaN
    false

    julia> NaN > NaN
    false

最後の点はさらに :ref:`Arrays <man-arrays>` において特有の頭痛の種となりえます:

.. doctest::

    julia> [1 NaN] == [1 NaN]
    false

Julia は特殊な値について数をテストする追加的な関数を提供しています。
これはハッシュキーの比較のような状況で役に立つことがあります:

=============================== ==================================
関数                             テストする内容
=============================== ==================================
:func:`isequal(x, y) <isequal>` ``x`` と ``y`` が等しいか
:func:`isfinite(x) <isfinite>`  ``x`` が有限な数か
:func:`isinf(x) <isinf>`        ``x`` が無限大か
:func:`isnan(x) <isnan>`        ``x`` が数ではないか
=============================== ==================================

:func:`isequal` は複数の ``NaN`` をお互いに等しいとみなします:

.. doctest::

    julia> isequal(NaN,NaN)
    true

    julia> isequal([1 NaN], [1 NaN])
    true

    julia> isequal(NaN,NaN32)
    true

:func:`isequal` は符号付きゼロを区別するためにも使えます:

.. doctest::

    julia> -0.0 == 0.0
    true

    julia> isequal(-0.0, 0.0)
    false

符号付き整数、符号無し整数、浮動小数点数間の混合型比較は時として難しいことです。
Julia がそれを確実に正しく行うよう多大な注意が払われてきました。

他の型については、 :func:`isequal` はデフォルトで :func:`==` を呼び出すので、 
独自の型について等価を定義したい場合は、 :func:`==` メソッドを追加するだけです。
独自の等価関数を定義するとき、多くの場合は対応する :func:`hash` メソッドを定義して
``isequal(x,y)`` が ``hash(x) == hash(y)`` を包含するようにすべきです。

連鎖比較
~~~~~~~~~~~~~~~~~~~~

`Python という顕著な例外 <https://en.wikipedia.org/wiki/Python_syntax_and_semantics#Comparison_operators>`_ を除くほとんどの言語とは異なり、
比較は任意に連鎖できます:

.. doctest::

    julia> 1 < 2 <= 2 < 3 == 3 > 2 >= 1 == 1 < 3 != 5
    true

連鎖比較は数値計算コードにおいてたびたび非常に役に立ちます。
連鎖比較ではスカラー比較に :obj:`&&` 演算子を、
要素比較に配列を扱える :obj:`&` 演算子を使います。
例えば、 ``0 .< A .< 1`` は ``A`` の対応する要素が 
0 から 1 のあいだであれば真であるという要素からなるブール配列を与えます。

演算子 :obj:`.<` は配列向けです; 演算 
``A .< B`` は ``A`` と ``B`` が同じ次元であるときだけ妥当です。 
この演算子はブール要素からなる
``A`` and ``B`` と同じ次元である配列を返します。このような演算子は *要素ごとの* 演算子と呼ばれます; Julia は
要素演算子を一揃い備えています: :obj:`.*`、 :obj:`.+`、 など。 要素演算子には
前の段落の例 ``0 .< A .< 1`` のように、スカラーオペランドを
とることができるものもあります。
この記法は、配列のそれぞれの要素に対して同じスカラーオペランドが
与えられることを意味しています。 

連鎖比較の評価のふるまいにご注意ください::

    v(x) = (println(x); x)

    julia> v(1) < v(2) <= v(3)
    2
    1
    3
    true

    julia> v(1) > v(2) <= v(3)
    2
    1
    false

真ん中の式がもし ``v(1) < v(2) && v(2) <= v(3)`` だったら
二度評価されたでしょうが、ここでは一度だけ評価されます。しかし、連鎖比較における
評価順序は未定義です。連鎖比較において副作用のある式 (print による出力など) を用いるのは
推奨されません。
副作用が避けられないときは、短絡回路 :obj:`&&` 演算子を
明示的に用いましょう (:ref:`man-short-circuit-evaluation` をお読みください。)。

Operator Precedence
~~~~~~~~~~~~~~~~~~~

Julia applies the following order of operations, from highest precedence
to lowest:

================= =============================================================================================
Category          Operators
================= =============================================================================================
Syntax            ``.`` followed by ``::``
Exponentiation    ``^`` and its elementwise equivalent ``.^``
Fractions         ``//`` and ``.//``
Multiplication    ``* / % & \`` and  ``.* ./ .% .\``
Bitshifts         ``<< >> >>>`` and ``.<< .>> .>>>``
Addition          ``+ - | $`` and ``.+ .-``
Syntax            ``: ..`` followed by ``|>``
Comparisons       ``> < >= <= == === != !== <:`` and ``.> .< .>= .<= .== .!=``
Control flow      ``&&`` followed by ``||`` followed by ``?``
Assignments       ``= += -= *= /= //= \= ^= ÷= %= |= &= $= <<= >>= >>>=`` and ``.+= .-= .*= ./= .//= .\= .^= .÷= .%=``
================= =============================================================================================

.. _man-numerical-conversions:

Numerical Conversions
---------------------

Julia supports three forms of numerical conversion, which differ in their
handling of inexact conversions.

- The notation ``T(x)`` or ``convert(T,x)`` converts ``x`` to a value of type ``T``.

  -  If ``T`` is a floating-point type, the result is the nearest representable
     value, which could be positive or negative infinity.

  -  If ``T`` is an integer type, an ``InexactError`` is raised if ``x``
     is not representable by ``T``.


- ``x % T`` converts an integer ``x`` to a value of integer type ``T``
  congruent to ``x`` modulo ``2^n``, where ``n`` is the number of bits in ``T``.
  In other words, the binary representation is truncated to fit.

- The :ref:`man-rounding-functions` take a type ``T`` as an optional argument.
  For example, ``round(Int,x)`` is a shorthand for ``Int(round(x))``.

The following examples show the different forms.

.. doctest::

    julia> Int8(127)
    127

    julia> Int8(128)
    ERROR: InexactError()
     in call at essentials.jl:56

    julia> Int8(127.0)
    127

    julia> Int8(3.14)
    ERROR: InexactError()
     in call at essentials.jl:56

    julia> Int8(128.0)
    ERROR: InexactError()
     in call at essentials.jl:56

    julia> 127 % Int8
    127

    julia> 128 % Int8
    -128

    julia> round(Int8,127.4)
    127

    julia> round(Int8,127.6)
    ERROR: InexactError()
     in trunc at float.jl:357
     in round at float.jl:177

See :ref:`man-conversion-and-promotion` for how to define your own
conversions and promotions.

.. _man-elementary-functions:

Elementary Functions
--------------------

Julia provides a comprehensive collection of mathematical functions and
operators. These mathematical operations are defined over as broad a
class of numerical values as permit sensible definitions, including
integers, floating-point numbers, rationals, and complexes, wherever
such definitions make sense.

.. _man-rounding-functions:

Rounding functions
~~~~~~~~~~~~~~~~~~

=========================== ================================== =============
Function                    Description                        Return type
=========================== ================================== =============
:func:`round(x) <round>`    round ``x`` to the nearest integer ``typeof(x)``
:func:`round(T, x) <round>` round ``x`` to the nearest integer ``T``
:func:`floor(x) <floor>`    round ``x`` towards ``-Inf``       ``typeof(x)``
:func:`floor(T, x) <floor>` round ``x`` towards ``-Inf``       ``T``
:func:`ceil(x) <ceil>`      round ``x`` towards ``+Inf``       ``typeof(x)``
:func:`ceil(T, x) <ceil>`   round ``x`` towards ``+Inf``       ``T``
:func:`trunc(x) <trunc>`    round ``x`` towards zero           ``typeof(x)``
:func:`trunc(T, x) <trunc>` round ``x`` towards zero           ``T``
=========================== ================================== =============

Division functions
~~~~~~~~~~~~~~~~~~

============================ =======================================================================
Function                     Description
============================ =======================================================================
:func:`div(x,y) <div>`       truncated division; quotient rounded towards zero
:func:`fld(x,y) <fld>`       floored division; quotient rounded towards ``-Inf``
:func:`cld(x,y) <cld>`       ceiling division; quotient rounded towards ``+Inf``
:func:`rem(x,y) <rem>`       remainder; satisfies ``x == div(x,y)*y + rem(x,y)``; sign matches ``x``
:func:`mod(x,y) <mod>`       modulus; satisfies ``x == fld(x,y)*y + mod(x,y)``; sign matches ``y``
:func:`mod2pi(x) <mod2pi>`   modulus with respect to 2pi;  ``0 <= mod2pi(x)  < 2pi``
:func:`divrem(x,y) <divrem>` returns ``(div(x,y),rem(x,y))``
:func:`fldmod(x,y) <fldmod>` returns ``(fld(x,y),mod(x,y))``
:func:`gcd(x,y...) <gcd>`    greatest common divisor of ``x``, ``y``,...; sign matches ``x``
:func:`lcm(x,y...) <lcm>`    least common multiple of ``x``, ``y``,...; sign matches ``x``
============================ =======================================================================

Sign and absolute value functions
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================================ ===========================================================
Function                         Description
================================ ===========================================================
:func:`abs(x) <abs>`             a positive value with the magnitude of ``x``
:func:`abs2(x) <abs2>`           the squared magnitude of ``x``
:func:`sign(x) <sign>`           indicates the sign of ``x``, returning -1, 0, or +1
:func:`signbit(x) <signbit>`     indicates whether the sign bit is on (true) or off (false)
:func:`copysign(x,y) <copysign>` a value with the magnitude of ``x`` and the sign of ``y``
:func:`flipsign(x,y) <flipsign>` a value with the magnitude of ``x`` and the sign of ``x*y``
================================ ===========================================================

Powers, logs and roots
~~~~~~~~~~~~~~~~~~~~~~

==================================== ==============================================================================
Function                             Description
==================================== ==============================================================================
:func:`sqrt(x) <sqrt>` ``√x``        square root of ``x``
:func:`cbrt(x) <cbrt>` ``∛x``        cube root of ``x``
:func:`hypot(x,y) <hypot>`           hypotenuse of right-angled triangle with other sides of length ``x`` and ``y``
:func:`exp(x) <exp>`                 natural exponential function at ``x``
:func:`expm1(x) <expm1>`             accurate ``exp(x)-1`` for ``x`` near zero
:func:`ldexp(x,n) <ldexp>`           ``x*2^n`` computed efficiently for integer values of ``n``
:func:`log(x) <log>`                 natural logarithm of ``x``
:func:`log(b,x) <log>`               base ``b`` logarithm of ``x``
:func:`log2(x) <log2>`               base 2 logarithm of ``x``
:func:`log10(x) <log10>`             base 10 logarithm of ``x``
:func:`log1p(x) <log1p>`             accurate ``log(1+x)`` for ``x`` near zero
:func:`exponent(x) <exponent>`       binary exponent of ``x``
:func:`significand(x) <significand>` binary significand (a.k.a. mantissa) of a floating-point number ``x``
==================================== ==============================================================================

For an overview of why functions like :func:`hypot`, :func:`expm1`, and :func:`log1p`
are necessary and useful, see John D. Cook's excellent pair
of blog posts on the subject: `expm1, log1p,
erfc <http://www.johndcook.com/blog/2010/06/07/math-library-functions-that-seem-unnecessary/>`_,
and
`hypot <http://www.johndcook.com/blog/2010/06/02/whats-so-hard-about-finding-a-hypotenuse/>`_.

Trigonometric and hyperbolic functions
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

All the standard trigonometric and hyperbolic functions are also defined::

    sin    cos    tan    cot    sec    csc
    sinh   cosh   tanh   coth   sech   csch
    asin   acos   atan   acot   asec   acsc
    asinh  acosh  atanh  acoth  asech  acsch
    sinc   cosc   atan2

These are all single-argument functions, with the exception of
`atan2 <https://en.wikipedia.org/wiki/Atan2>`_, which gives the angle
in `radians <https://en.wikipedia.org/wiki/Radian>`_ between the *x*-axis
and the point specified by its arguments, interpreted as *x* and *y*
coordinates.

Additionally, :func:`sinpi(x) <sinpi>` and :func:`cospi(x) <cospi>` are provided for more accurate computations
of :func:`sin(pi*x) <sin>` and :func:`cos(pi*x) <cos>` respectively.

In order to compute trigonometric functions with degrees
instead of radians, suffix the function with ``d``. For example, :func:`sind(x) <sind>`
computes the sine of ``x`` where ``x`` is specified in degrees.
The complete list of trigonometric functions with degree variants is::

    sind   cosd   tand   cotd   secd   cscd
    asind  acosd  atand  acotd  asecd  acscd

Special functions
~~~~~~~~~~~~~~~~~

=================================================== ==============================================================================
Function                                            Description
=================================================== ==============================================================================
:func:`erf(x) <erf>`                                `error function <https://en.wikipedia.org/wiki/Error_function>`_ at ``x``
:func:`erfc(x) <erfc>`                              complementary error function, i.e. the accurate version of ``1-erf(x)`` for large ``x``
:func:`erfinv(x) <erfinv>`                          inverse function to :func:`erf`
:func:`erfcinv(x) <erfinvc>`                        inverse function to :func:`erfc`
:func:`erfi(x) <erfi>`                              imaginary error function defined as ``-im * erf(x * im)``, where :const:`im` is the imaginary unit
:func:`erfcx(x) <erfcx>`                            scaled complementary error function, i.e. accurate ``exp(x^2) * erfc(x)`` for large ``x``
:func:`dawson(x) <dawson>`                          scaled imaginary error function, a.k.a. Dawson function, i.e. accurate ``exp(-x^2) * erfi(x) * sqrt(pi) / 2`` for large ``x``
:func:`gamma(x) <gamma>`                            `gamma function <https://en.wikipedia.org/wiki/Gamma_function>`_ at ``x``
:func:`lgamma(x) <lgamma>`                          accurate ``log(gamma(x))`` for large ``x``
:func:`lfact(x) <lfact>`                            accurate ``log(factorial(x))`` for large ``x``; same as ``lgamma(x+1)`` for ``x > 1``, zero otherwise
:func:`digamma(x) <digamma>`                        `digamma function <https://en.wikipedia.org/wiki/Digamma_function>`_ (i.e. the derivative of :func:`lgamma`) at ``x``
:func:`beta(x,y) <beta>`                            `beta function <https://en.wikipedia.org/wiki/Beta_function>`_ at ``x,y``
:func:`lbeta(x,y) <lbeta>`                          accurate ``log(beta(x,y))`` for large ``x`` or ``y``
:func:`eta(x) <eta>`                                `Dirichlet eta function <https://en.wikipedia.org/wiki/Dirichlet_eta_function>`_ at ``x``
:func:`zeta(x) <zeta>`                              `Riemann zeta function <https://en.wikipedia.org/wiki/Riemann_zeta_function>`_ at ``x``
|airylist|                                          `Airy Ai function <https://en.wikipedia.org/wiki/Airy_function>`_ at ``z``
|airyprimelist|                                     derivative of the Airy Ai function at ``z``
:func:`airybi(z) <airybi>`, ``airy(2,z)``           `Airy Bi function <https://en.wikipedia.org/wiki/Airy_function>`_ at ``z``
:func:`airybiprime(z) <airybiprime>`, ``airy(3,z)`` derivative of the Airy Bi function at ``z``
:func:`airyx(z) <airyx>`, ``airyx(k,z)``            scaled Airy AI function and ``k`` th derivatives at ``z``
:func:`besselj(nu,z) <besselj>`                     `Bessel function <https://en.wikipedia.org/wiki/Bessel_function>`_ of the first kind of order ``nu`` at ``z``
:func:`besselj0(z) <besselj0>`                      ``besselj(0,z)``
:func:`besselj1(z) <besselj1>`                      ``besselj(1,z)``
:func:`besseljx(nu,z) <besseljx>`                   scaled Bessel function of the first kind of order ``nu`` at ``z``
:func:`bessely(nu,z) <bessely>`                     `Bessel function <https://en.wikipedia.org/wiki/Bessel_function>`_ of the second kind of order ``nu`` at ``z``
:func:`bessely0(z) <bessely0>`                      ``bessely(0,z)``
:func:`bessely1(z) <bessely0>`                      ``bessely(1,z)``
:func:`besselyx(nu,z) <besselyx>`                   scaled Bessel function of the second kind of order ``nu`` at ``z``
:func:`besselh(nu,k,z) <besselh>`                   `Bessel function <https://en.wikipedia.org/wiki/Bessel_function>`_ of the third kind (a.k.a. Hankel function) of order ``nu`` at ``z``; ``k`` must be either ``1`` or ``2``
:func:`hankelh1(nu,z) <hankelh1>`                   ``besselh(nu, 1, z)``
:func:`hankelh1x(nu,z) <hankelh1x>`                 scaled ``besselh(nu, 1, z)``
:func:`hankelh2(nu,z) <hankelh2>`                   ``besselh(nu, 2, z)``
:func:`hankelh2x(nu,z) <hankelh2x>`                 scaled ``besselh(nu, 2, z)``
:func:`besseli(nu,z) <besseli>`                     modified `Bessel function <https://en.wikipedia.org/wiki/Bessel_function>`_ of the first kind of order ``nu`` at ``z``
:func:`besselix(nu,z) <besselix>`                   scaled modified Bessel function of the first kind of order ``nu`` at ``z``
:func:`besselk(nu,z) <besselk>`                     modified `Bessel function <https://en.wikipedia.org/wiki/Bessel_function>`_ of the second kind of order ``nu`` at ``z``
:func:`besselkx(nu,z) <besselkx>`                   scaled modified Bessel function of the second kind of order ``nu`` at ``z``
=================================================== ==============================================================================

.. |airylist| replace:: :func:`airy(z) <airy>`, :func:`airyai(z) <airyai>`, ``airy(0,z)``
.. |airyprimelist| replace:: :func:`airyprime(z) <airyprime>`, :func:`airyaiprime(z) <airyaiprime>`, ``airy(1,z)``


