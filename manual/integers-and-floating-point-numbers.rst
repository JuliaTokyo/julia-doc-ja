.. _man-integers-and-floating-point-numbers:

.. currentmodule:: Base

*************************************
 整数と浮動小数点数
*************************************

Integers and floating-point values are the basic building blocks of
arithmetic and computation. Built-in representations of such values are
called numeric primitives, while representations of integers and
floating-point numbers as immediate values in code are known as numeric
literals. For example, ``1`` is an integer literal, while ``1.0`` is a
floating-point literal; their binary in-memory representations as
objects are numeric primitives.

整数と浮動小数点数は、計算処理の基本要素となるものです。
これらの値の組み込み表現は数値プリミティブと呼ばれます。
一方、整数と浮動小数点数がコード内で直接の値となっているものは、数値リテラルと呼ばれています。
例えば、 ``1`` は整数リテラルで、 ``1.0`` は浮動小数点リテラルです。
これらのオブジェクト（メモリ上のバイナリ表現）が数値プリミティブです。

Julia provides a broad range of primitive numeric types, and a full complement
of arithmetic and bitwise operators as well as standard mathematical functions
are defined over them. These map directly onto numeric types and operations
that are natively supported on modern computers, thus allowing Julia to take
full advantage of computational resources. Additionally, Julia provides
software support for :ref:`man-arbitrary-precision-arithmetic`, which can
handle operations on numeric values that cannot be represented effectively in
native hardware representations, but at the cost of relatively slower
performance.

Juliaは様々なプリミティブ数値型を用意しています。
これらの型には、完全な計算・ビット演算子、そして標準的な数学関数が定義されています。
これらの数値型と演算は、現代のコンピュータでネイティブにサポートされているものと直接対応しています。
これにより、Juliaは計算リソースを最大限に活用することができるのです。
加えて、Juliaは :ref:`man-arbitrary-precision-arithmetic` をソフトウェアとしてサポートしています。
これは、ハードウェアでのネイティブ表現では効率的に表せない数値の演算を可能にしますが、処理は相対的に遅くなります。

The following are Julia's primitive numeric types:

下に示したものが、Juliaのプリミティブ数値型です。

-  **Integer types:**

================  =======  ==============  ============== ==================
Type              Signed?  Number of bits  Smallest value Largest value
================  =======  ==============  ============== ==================
:class:`Int8`        ✓         8            -2^7             2^7 - 1
:class:`UInt8`                 8             0               2^8 - 1
:class:`Int16`       ✓         16           -2^15            2^15 - 1
:class:`UInt16`                16            0               2^16 - 1
:class:`Int32`       ✓         32           -2^31            2^31 - 1
:class:`UInt32`                32            0               2^32 - 1
:class:`Int64`       ✓         64           -2^63            2^63 - 1
:class:`UInt64`                64            0               2^64 - 1
:class:`Int128`      ✓         128           -2^127          2^127 - 1
:class:`UInt128`               128           0               2^128 - 1
:class:`Bool`       N/A        8           ``false`` (0)  ``true`` (1)
================  =======  ==============  ============== ==================

-  **Floating-point types:**

================ ========= ==============
Type             Precision Number of bits
================ ========= ==============
:class:`Float16` half_          16
:class:`Float32` single_        32
:class:`Float64` double_        64
================ ========= ==============

.. _half: https://en.wikipedia.org/wiki/Half-precision_floating-point_format
.. _single: https://en.wikipedia.org/wiki/Single_precision_floating-point_format
.. _double: https://en.wikipedia.org/wiki/Double_precision_floating-point_format


-  **整数型**

================  =======  ==============  ============== ==================
型                 符号      ビット数         最小値          最大値
================  =======  ==============  ============== ==================
:class:`Int8`        ✓         8            -2^7             2^7 - 1
:class:`UInt8`                 8             0               2^8 - 1
:class:`Int16`       ✓         16           -2^15            2^15 - 1
:class:`UInt16`                16            0               2^16 - 1
:class:`Int32`       ✓         32           -2^31            2^31 - 1
:class:`UInt32`                32            0               2^32 - 1
:class:`Int64`       ✓         64           -2^63            2^63 - 1
:class:`UInt64`                64            0               2^64 - 1
:class:`Int128`      ✓         128           -2^127          2^127 - 1
:class:`UInt128`               128           0               2^128 - 1
:class:`Bool`       N/A        8           ``false`` (0)  ``true`` (1)
================  =======  ==============  ============== ==================

-  **浮動小数点型**

================ ========= ==============
型　　             精度 　　　ビット数
================ ========= ==============
:class:`Float16` 半精度_        16
:class:`Float32` 単精度_        32
:class:`Float64` 倍精度_        64
================ ========= ==============

.. _半精度: https://ja.wikipedia.org/wiki/%E5%8D%8A%E7%B2%BE%E5%BA%A6%E6%B5%AE%E5%8B%95%E5%B0%8F%E6%95%B0%E7%82%B9%E6%95%B0
.. _単精度: https://ja.wikipedia.org/wiki/%E5%8D%98%E7%B2%BE%E5%BA%A6%E6%B5%AE%E5%8B%95%E5%B0%8F%E6%95%B0%E7%82%B9%E6%95%B0
.. _倍精度: https://ja.wikipedia.org/wiki/%E5%80%8D%E7%B2%BE%E5%BA%A6%E6%B5%AE%E5%8B%95%E5%B0%8F%E6%95%B0%E7%82%B9%E6%95%B0



Additionally, full support for :ref:`man-complex-and-rational-numbers` is built
on top of these primitive numeric types. All numeric types interoperate
naturally without explicit casting, thanks to a flexible, user-extensible
:ref:`type promotion system <man-conversion-and-promotion>`.

加えて、これらのプリミティブ数値型をもとに :ref:`man-complex-and-rational-numbers` も完全にサポートされています。
全ての数値型は明示的にキャストしなくても、自然にinterpolateします。これは、フレキシブルでユーザーが拡張可能な :ref:`type promotion system <man-conversion-and-promotion>` によって実現されています。

Integers
--------

整数
--------

Literal integers are represented in the standard manner:

リテラルな整数は、標準的な書き方で表されます。

.. doctest::

    julia> 1
    1

    julia> 1234
    1234

The default type for an integer literal depends on whether the target
system has a 32-bit architecture or a 64-bit architecture:

整数リテラルのデフォルトでの型は、そのシステムが32ビットのアーキテクチャか64ビットかで変わります。

.. doctest::

    # 32-bit system:
    julia> typeof(1)
    Int32

    # 64-bit system:
    julia> typeof(1)
    Int64

    # 32ビットのシステム
    julia> typeof(1)
    Int32

    # 64ビットのシステム
    julia> typeof(1)
    Int64


The Julia internal variable :const:`WORD_SIZE` indicates whether the target system
is 32-bit or 64-bit.:

Juliaの内部的な変数 :const:`WORD_SIZE` は、そのシステムが32ビットか64ビットなのかを示します。

.. doctest::

    # 32-bit system:
    julia> WORD_SIZE
    32

    # 64-bit system:
    julia> WORD_SIZE
    64

    # 32ビットのシステム
    julia> WORD_SIZE
    32

    # 64ビットのシステム
    julia> WORD_SIZE
    64

Julia also defines the types :class:`Int` and :class:`UInt`, which are aliases for the
system's signed and unsigned native integer types respectively.:

また、Juliaでは :class:`Int` と :class:`UInt` という型が定義されています。
これらはそれぞれ、システムでの符号なし・符号付き整数型の別名となっています。

.. doctest::

    # 32-bit system:
    julia> Int
    Int32
    julia> UInt
    UInt32


    # 64-bit system:
    julia> Int
    Int64
    julia> UInt
    UInt64


    # 32ビットのシステム
    julia> Int
    Int32
    julia> UInt
    UInt32


    # 64ビットのシステム
    julia> Int
    Int64
    julia> UInt
    UInt64

Larger integer literals that cannot be represented using only 32 bits
but can be represented in 64 bits always create 64-bit integers,
regardless of the system type:

32ビットでは表現できないが64ビットであれば可能な整数リテラルは、どのようなシステムでも必ず64ビットの整数としてつくられます。


.. doctest::

    # 32-bit or 64-bit system:
    julia> typeof(3000000000)
    Int64

    # 32ビット、もしくは64ビットのシステム
    julia> typeof(3000000000)
    Int64

Unsigned integers are input and output using the ``0x`` prefix and hexadecimal
(base 16) digits ``0-9a-f`` (the capitalized digits ``A-F`` also work for input).
The size of the unsigned value is determined by the number of hex digits used:

符号なし整数の入出力は、 ``0x`` というプレフィックスがついた16進数 ``0-9a-f`` で表されます（入力の場合は大文字の ``A-F`` を使うこともできます）。
符号なしの値のサイズは、16進数の文字数により決まります。


.. doctest::

    julia> 0x1
    0x01

    julia> typeof(ans)
    UInt8

    julia> 0x123
    0x0123

    julia> typeof(ans)
    UInt16

    julia> 0x1234567
    0x01234567

    julia> typeof(ans)
    UInt32

    julia> 0x123456789abcdef
    0x0123456789abcdef

    julia> typeof(ans)
    UInt64

This behavior is based on the observation that when one uses unsigned
hex literals for integer values, one typically is using them to
represent a fixed numeric byte sequence, rather than just an integer
value.

このふるまいは、符号なしの16進数リテラルを整数値として使うとき、整数値ではなく固定長の数値バイト列を表すのに使うことが多いことからきています。

Recall that the variable :data:`ans` is set to the value of the last expression
evaluated in an interactive session. This does not occur when Julia code is
run in other ways.

以前に述べたように、 :data:`ans` という変数はインタラクティブ・セッションで最後に評価された値となっています。
これは他の方法でJuliaを実行した際には起こりません。


Binary and octal literals are also supported:

バイナリと八進数のリテラルも用意されています。

.. doctest::

    julia> 0b10
    0x02

    julia> typeof(ans)
    UInt8

    julia> 0o10
    0x08

    julia> typeof(ans)
    UInt8

The minimum and maximum representable values of primitive numeric types
such as integers are given by the :func:`typemin` and :func:`typemax` functions:

整数のようなプリミティブ数値型が表現できる最小と最大の値はそれぞれ :func:`typemin` と :func:`typemax` という関数で取得できます。

.. doctest::

    julia> (typemin(Int32), typemax(Int32))
    (-2147483648,2147483647)

    julia> for T in [Int8,Int16,Int32,Int64,Int128,UInt8,UInt16,UInt32,UInt64,UInt128]
             println("$(lpad(T,7)): [$(typemin(T)),$(typemax(T))]")
           end
       Int8: [-128,127]
      Int16: [-32768,32767]
      Int32: [-2147483648,2147483647]
      Int64: [-9223372036854775808,9223372036854775807]
     Int128: [-170141183460469231731687303715884105728,170141183460469231731687303715884105727]
      UInt8: [0,255]
     UInt16: [0,65535]
     UInt32: [0,4294967295]
     UInt64: [0,18446744073709551615]
    UInt128: [0,340282366920938463463374607431768211455]

The values returned by :func:`typemin` and :func:`typemax` are always of the
given argument type. (The above expression uses several features we have
yet to introduce, including :ref:`for loops <man-loops>`,
:ref:`man-strings`, and :ref:`man-string-interpolation`,
but should be easy enough to understand for users with some existing
programming experience.)

:func:`typemin` と :func:`typemax` は、必ず引数と同じ型の値を返します
（:ref:`for loops <man-loops>` や :ref:`man-strings`、:ref:`man-string-interpolation` といったまだ紹介していない機能が上のコードでは使われていますが、他のプログラミング言語の経験者が理解するのはそれほど難しくないでしょう）。



Overflow behavior
~~~~~~~~~~~~~~~~~

オーバーフローのふるまい
~~~~~~~~~~~~~~~~~~~~~~~~~~

In Julia, exceeding the maximum representable value of a given type results in
a wraparound behavior:

Juliaでは、型の表現可能な最大値を超えた場合、ラップアラウンドします。

.. doctest::

    julia> x = typemax(Int64)
    9223372036854775807

    julia> x + 1
    -9223372036854775808

    julia> x + 1 == typemin(Int64)
    true

Thus, arithmetic with Julia integers is actually a form of `modular arithmetic
<https://en.wikipedia.org/wiki/Modular_arithmetic>`_. This reflects the
characteristics of the underlying arithmetic of integers as implemented on
modern computers. In applications where overflow is possible, explicit checking
for wraparound produced by overflow is essential; otherwise, the ``BigInt`` type
in :ref:`man-arbitrary-precision-arithmetic` is recommended instead.

このことから分かるように、Juliaでの整数の計算は `合同算術（モジュラ計算） <https://ja.wikipedia.org/wiki/%E5%90%88%E5%90%8C%E7%AE%97%E8%A1%93>`_ となっています。
これは、Juliaを実行する下にある、現代のコンピュータで実装されている整数計算の特徴を反映しています。
オーバーフローするかもしれないアプリケーションでは、オーバーフローによるラップアラウンドの明示的な確認は必須です。
そうでなければ、 :ref:`man-arbitrary-precision-arithmetic` の ``BigInt`` を使うことが推奨されます。


Division errors
~~~~~~~~~~~~~~~

除算エラー
~~~~~~~~~~~~

Integer division (the ``div`` function) has two exceptional cases: dividing by
zero, and dividing the lowest negative number (:func:`typemin`) by -1. Both of
these cases throw a :exc:`DivideError`. The remainder and modulus functions
(``rem`` and ``mod``) throw a :exc:`DivideError` when their second argument is
zero.

整数の割り算（``div`` 関数）には2つの例外ケースがあります。ゼロ除算と、最も小さい負の値（:func:`typemin`）を-1で割る場合です。
どちらのケースも :exc:`DivideError` が投げられます。
除算の余りを求める関数とモジュロ関数（``rem`` と ``mod``） [#rem-and-mod]_ は、2つめの引数がゼロのとき :exc:`DivideError` を投げます。



Floating-Point Numbers
----------------------

浮動小数点数
--------------

Literal floating-point numbers are represented in the standard formats:

リテラルな浮動小数点数は、標準的な書き方で表されます。

.. doctest::

    julia> 1.0
    1.0

    julia> 1.
    1.0

    julia> 0.5
    0.5

    julia> .5
    0.5

    julia> -1.23
    -1.23

    julia> 1e10
    1.0e10

    julia> 2.5e-4
    0.00025

The above results are all ``Float64`` values. Literal ``Float32`` values can
be entered by writing an ``f`` in place of ``e``:

上の例は全て ``Float64`` の値です。
リテラルな ``Float32`` の値は、 ``e`` の代わりに ``f`` と書くことで入力できます。

.. doctest::

    julia> 0.5f0
    0.5f0

    julia> typeof(ans)
    Float32

    julia> 2.5f-4
    0.00025f0

Values can be converted to ``Float32`` easily:

値は簡単に ``Float32`` へ変換することができます。

.. doctest::

    julia> Float32(-1.5)
    -1.5f0

    julia> typeof(ans)
    Float32

Hexadecimal floating-point literals are also valid, but only as ``Float64`` values:

16進法の浮動小数点リテラルも有効ですが、これは ``Float64`` の値としてのみ可能です。

.. doctest::

    julia> 0x1p0
    1.0

    julia> 0x1.8p3
    12.0

    julia> 0x.4p-1
    0.125

    julia> typeof(ans)
    Float64

Half-precision floating-point numbers are also supported (``Float16``), but
only as a storage format. In calculations they'll be converted to ``Float32``:

半精度の浮動小数点数（``Float16``）も用意されていますが、これは保存の用途でのみ使うことができます。
計算の際には、これらは ``Float32`` に変換されます。

.. doctest::

    julia> sizeof(Float16(4.))
    2

    julia> 2*Float16(4.)
    8.0f0

The underscore ``_`` can be used as digit separator:

アンダースコア ``_`` は数字の区切りを表すのに使うことができます。

.. doctest::

    julia> 10_000, 0.000_000_005, 0xdead_beef, 0b1011_0010
    (10000,5.0e-9,0xdeadbeef,0xb2)

Floating-point zero
~~~~~~~~~~~~~~~~~~~

浮動小数点でのゼロ
~~~~~~~~~~~~~~~~~~

Floating-point numbers have `two zeros
<https://en.wikipedia.org/wiki/Signed_zero>`_, positive zero and negative zero.
They are equal to each other but have different binary representations, as can
be seen using the ``bits`` function: :

浮動小数点数には`正のゼロと負のゼロ <https://ja.wikipedia.org/wiki/%E2%88%920>`_があります。
これらは等価ですが、それぞれ異なるバイナリ表現となっています。
``bits`` 関数でそれぞれのバイナリ表現をみることができます。


.. doctest::

    julia> 0.0 == -0.0
    true

    julia> bits(0.0)
    "0000000000000000000000000000000000000000000000000000000000000000"

    julia> bits(-0.0)
    "1000000000000000000000000000000000000000000000000000000000000000"

.. _man-special-floats:

Special floating-point values
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

特別な浮動小数点の値
~~~~~~~~~~~~~~~~~~~~

There are three specified standard floating-point values that do not
correspond to any point on the real number line:

以下の明確な3つの標準的な浮動小数点の値は、実際の数直線上とは対応しないものです。

=========== =========== ===========  ================= =================================================================
Special value                        Name              Description
-----------------------------------  ----------------- -----------------------------------------------------------------
``Float16`` ``Float32`` ``Float64``
=========== =========== ===========  ================= =================================================================
``Inf16``   ``Inf32``    ``Inf``     positive infinity a value greater than all finite floating-point values
``-Inf16``  ``-Inf32``   ``-Inf``    negative infinity a value less than all finite floating-point values
``NaN16``   ``NaN32``    ``NaN``     not a number      a value not ``==`` to any floating-point value (including itself)
=========== =========== ===========  ================= =================================================================


=========== =========== ===========  ================= =================================================================
特別な値                               名前               説明
-----------------------------------  ----------------- -----------------------------------------------------------------
``Float16`` ``Float32`` ``Float64``
=========== =========== ===========  ================= =================================================================
``Inf16``   ``Inf32``    ``Inf``     positive infinity a value greater than all finite floating-point values
``-Inf16``  ``-Inf32``   ``-Inf``    negative infinity a value less than all finite floating-point values
``NaN16``   ``NaN32``    ``NaN``     not a number      a value not ``==`` to any floating-point value (including itself)
=========== =========== ===========  ================= =================================================================


For further discussion of how these non-finite floating-point values are
ordered with respect to each other and other floats, see
:ref:`man-numeric-comparisons`. By the
`IEEE 754 standard <https://en.wikipedia.org/wiki/IEEE_754-2008>`_, these
floating-point values are the results of certain arithmetic operations:

.. doctest::

    julia> 1/Inf
    0.0

    julia> 1/0
    Inf

    julia> -5/0
    -Inf

    julia> 0.000001/0
    Inf

    julia> 0/0
    NaN

    julia> 500 + Inf
    Inf

    julia> 500 - Inf
    -Inf

    julia> Inf + Inf
    Inf

    julia> Inf - Inf
    NaN

    julia> Inf * Inf
    Inf

    julia> Inf / Inf
    NaN

    julia> 0 * Inf
    NaN

The :func:`typemin` and :func:`typemax` functions also apply to floating-point
types:

.. doctest::

    julia> (typemin(Float16),typemax(Float16))
    (-Inf16,Inf16)

    julia> (typemin(Float32),typemax(Float32))
    (-Inf32,Inf32)

    julia> (typemin(Float64),typemax(Float64))
    (-Inf,Inf)


Machine epsilon
~~~~~~~~~~~~~~~

計算機イプシロン
~~~~~~~~~~~~~~~

Most real numbers cannot be represented exactly with floating-point numbers,
and so for many purposes it is important to know the distance between two
adjacent representable floating-point numbers, which is often known as
`machine epsilon <https://en.wikipedia.org/wiki/Machine_epsilon>`_.

Julia provides :func:`eps`, which gives the distance between ``1.0``
and the next larger representable floating-point value:

.. doctest::

    julia> eps(Float32)
    1.1920929f-7

    julia> eps(Float64)
    2.220446049250313e-16

    julia> eps() # same as eps(Float64)
    2.220446049250313e-16

These values are ``2.0^-23`` and ``2.0^-52`` as ``Float32`` and ``Float64``
values, respectively. The :func:`eps` function can also take a
floating-point value as an argument, and gives the absolute difference
between that value and the next representable floating point value. That
is, ``eps(x)`` yields a value of the same type as ``x`` such that
``x + eps(x)`` is the next representable floating-point value larger
than ``x``:

.. doctest::

    julia> eps(1.0)
    2.220446049250313e-16

    julia> eps(1000.)
    1.1368683772161603e-13

    julia> eps(1e-27)
    1.793662034335766e-43

    julia> eps(0.0)
    5.0e-324

The distance between two adjacent representable floating-point numbers is not
constant, but is smaller for smaller values and larger for larger values. In
other words, the representable floating-point numbers are densest in the real
number line near zero, and grow sparser exponentially as one moves farther away
from zero. By definition, ``eps(1.0)`` is the same as ``eps(Float64)`` since
``1.0`` is a 64-bit floating-point value.

Julia also provides the :func:`nextfloat` and :func:`prevfloat` functions which return
the next largest or smallest representable floating-point number to the
argument respectively: :

.. doctest::

    julia> x = 1.25f0
    1.25f0

    julia> nextfloat(x)
    1.2500001f0

    julia> prevfloat(x)
    1.2499999f0

    julia> bits(prevfloat(x))
    "00111111100111111111111111111111"

    julia> bits(x)
    "00111111101000000000000000000000"

    julia> bits(nextfloat(x))
    "00111111101000000000000000000001"

This example highlights the general principle that the adjacent representable
floating-point numbers also have adjacent binary integer representations.

Rounding modes
~~~~~~~~~~~~~~

端数処理
~~~~~~~~~~

If a number doesn't have an exact floating-point representation, it must be
rounded to an appropriate representable value, however, if wanted, the manner
in which this rounding is done can be changed according to the rounding modes
presented in the `IEEE 754 standard <https://en.wikipedia.org/wiki/IEEE_754-2008>`_::


    julia> 1.1 + 0.1
    1.2000000000000002

    julia> with_rounding(Float64,RoundDown) do
           1.1 + 0.1
           end
    1.2

The default mode used is always :const:`RoundNearest`, which rounds to the nearest
representable value, with ties rounded towards the nearest value with an even
least significant bit.

.. warning:: Rounding is generally only correct for basic arithmetic functions
	     (:func:`+`, :func:`-`, :func:`*`, :func:`/` and :func:`sqrt`) and
	     type conversion operations. Many other functions assume the
	     default :const:`RoundNearest` mode is set, and can give erroneous
	     results when operating under other rounding modes.

Background and References
~~~~~~~~~~~~~~~~~~~~~~~~~

背景と参考文献
~~~~~~~~~~~~~~~~

Floating-point arithmetic entails many subtleties which can be surprising to
users who are unfamiliar with the low-level implementation details. However,
these subtleties are described in detail in most books on scientific
computation, and also in the following references:

- The definitive guide to floating point arithmetic is the `IEEE 754-2008
  Standard <http://standards.ieee.org/findstds/standard/754-2008.html>`_;
  however, it is not available for free online.
- For a brief but lucid presentation of how floating-point numbers are
  represented, see John D. Cook's `article
  <http://www.johndcook.com/blog/2009/04/06/anatomy-of-a-floating-point-number/>`_
  on the subject as well as his `introduction
  <http://www.johndcook.com/blog/2009/04/06/numbers-are-a-leaky-abstraction/>`_
  to some of the issues arising from how this representation differs in
  behavior from the idealized abstraction of real numbers.
- Also recommended is Bruce Dawson's `series of blog posts on floating-point
  numbers <https://randomascii.wordpress.com/2012/05/20/thats-not-normalthe-performance-of-odd-floats/>`_.
- For an excellent, in-depth discussion of floating-point numbers and issues of
  numerical accuracy encountered when computing with them, see David Goldberg's
  paper `What Every Computer Scientist Should Know About Floating-Point
  Arithmetic
  <http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.22.6768&rep=rep1&type=pdf>`_.
- For even more extensive documentation of the history of, rationale for,
  and issues with floating-point numbers, as well as discussion of many other
  topics in numerical computing, see the `collected writings
  <http://www.cs.berkeley.edu/~wkahan/>`_ of `William Kahan
  <https://en.wikipedia.org/wiki/William_Kahan>`_, commonly known as the "Father
  of Floating-Point". Of particular interest may be `An Interview with the Old
  Man of Floating-Point
  <http://www.cs.berkeley.edu/~wkahan/ieee754status/754story.html>`_.

.. _man-arbitrary-precision-arithmetic:

Arbitrary Precision Arithmetic
------------------------------

任意精度演算
----------------

To allow computations with arbitrary-precision integers and floating point numbers,
Julia wraps the `GNU Multiple Precision Arithmetic Library (GMP) <https://gmplib.org>`_ and the `GNU MPFR Library <http://www.mpfr.org>`_, respectively.
The :class:`BigInt` and :class:`BigFloat` types are available in Julia for arbitrary precision
integer and floating point numbers respectively.

Constructors exist to create these types from primitive numerical types, and
:func:`parse` can be use to construct them from :class:`AbstractString`\ s.  Once
created, they participate in arithmetic with all other numeric types thanks to
Julia's
:ref:`type promotion and conversion mechanism <man-conversion-and-promotion>`:

.. doctest::

    julia> BigInt(typemax(Int64)) + 1
    9223372036854775808

    julia> parse(BigInt, "123456789012345678901234567890") + 1
    123456789012345678901234567891

    julia> parse(BigFloat, "1.23456789012345678901")
    1.234567890123456789010000000000000000000000000000000000000000000000000000000004

    julia> BigFloat(2.0^66) / 3
    2.459565876494606882133333333333333333333333333333333333333333333333333333333344e+19

    julia> factorial(BigInt(40))
    815915283247897734345611269596115894272000000000

However, type promotion between the primitive types above and
:class:`BigInt`/:class:`BigFloat` is not automatic and must be explicitly stated.

.. doctest::

    julia> x = typemin(Int64)
    -9223372036854775808

    julia> x = x - 1
    9223372036854775807

    julia> typeof(x)
    Int64

    julia> y = BigInt(typemin(Int64))
    -9223372036854775808

    julia> y = y - 1
    -9223372036854775809

    julia> typeof(y)
    BigInt

The default precision (in number of bits of the significand) and
rounding mode of :class:`BigFloat` operations can be changed globally
by calling :func:`set_bigfloat_precision` and
:func:`set_rounding`, and all further calculations will take
these changes in account.  Alternatively, the precision or the
rounding can be changed only within the execution of a particular
block of code by :func:`with_bigfloat_precision` or
:func:`with_rounding`:

.. doctest::

    julia> with_rounding(BigFloat,RoundUp) do
           BigFloat(1) + parse(BigFloat, "0.1")
           end
    1.100000000000000000000000000000000000000000000000000000000000000000000000000003

    julia> with_rounding(BigFloat,RoundDown) do
           BigFloat(1) + parse(BigFloat, "0.1")
           end
    1.099999999999999999999999999999999999999999999999999999999999999999999999999986

    julia> with_bigfloat_precision(40) do
           BigFloat(1) + parse(BigFloat, "0.1")
           end
    1.1000000000004


.. _man-numeric-literal-coefficients:

Numeric Literal Coefficients
----------------------------

数のリテラルな係数
-------------------

To make common numeric formulas and expressions clearer, Julia allows
variables to be immediately preceded by a numeric literal, implying
multiplication. This makes writing polynomial expressions much cleaner:

.. doctest::

    julia> x = 3
    3

    julia> 2x^2 - 3x + 1
    10

    julia> 1.5x^2 - .5x + 1
    13.0

It also makes writing exponential functions more elegant:

.. doctest::

    julia> 2^2x
    64

The precedence of numeric literal coefficients is the same as that of unary
operators such as negation. So ``2^3x`` is parsed as ``2^(3x)``, and
``2x^3`` is parsed as ``2*(x^3)``.

Numeric literals also work as coefficients to parenthesized
expressions:

.. doctest::

    julia> 2(x-1)^2 - 3(x-1) + 1
    3

Additionally, parenthesized expressions can be used as coefficients to
variables, implying multiplication of the expression by the variable:

.. doctest::

    julia> (x-1)x
    6

Neither juxtaposition of two parenthesized expressions, nor placing a
variable before a parenthesized expression, however, can be used to
imply multiplication:

.. doctest::

    julia> (x-1)(x+1)
    ERROR: MethodError: `call` has no method matching call(::Int64, ::Int64)
    Closest candidates are:
      BoundsError()
      BoundsError(!Matched::Any...)
      DivideError()
      ...

    julia> x(x+1)
    ERROR: MethodError: `call` has no method matching call(::Int64, ::Int64)
    Closest candidates are:
      BoundsError()
      BoundsError(!Matched::Any...)
      DivideError()
      ...

Both expressions are interpreted as function application: any
expression that is not a numeric literal, when immediately followed by a
parenthetical, is interpreted as a function applied to the values in
parentheses (see :ref:`man-functions` for more about functions).
Thus, in both of these cases, an error occurs since the left-hand value
is not a function.

The above syntactic enhancements significantly reduce the visual noise
incurred when writing common mathematical formulae. Note that no
whitespace may come between a numeric literal coefficient and the
identifier or parenthesized expression which it multiplies.

Syntax Conflicts
~~~~~~~~~~~~~~~~

シンタックスの衝突
~~~~~~~~~~~~~~~~~

Juxtaposed literal coefficient syntax may conflict with two numeric literal
syntaxes: hexadecimal integer literals and engineering notation for
floating-point literals. Here are some situations where syntactic
conflicts arise:

-  The hexadecimal integer literal expression ``0xff`` could be
   interpreted as the numeric literal ``0`` multiplied by the variable
   ``xff``.
-  The floating-point literal expression ``1e10`` could be interpreted
   as the numeric literal ``1`` multiplied by the variable ``e10``, and
   similarly with the equivalent ``E`` form.

In both cases, we resolve the ambiguity in favor of interpretation as a
numeric literals:

-  Expressions starting with ``0x`` are always hexadecimal literals.
-  Expressions starting with a numeric literal followed by ``e`` or
   ``E`` are always floating-point literals.

Literal zero and one
--------------------

リテラルなゼロと1
----------------

Julia provides functions which return literal 0 and 1 corresponding to a
specified type or the type of a given variable.

====================== =====================================================
Function               Description
====================== =====================================================
:func:`zero(x) <zero>` Literal zero of type ``x`` or type of variable ``x``
:func:`one(x) <one>`   Literal one of type ``x`` or type of variable ``x``
====================== =====================================================

These functions are useful in :ref:`man-numeric-comparisons` to avoid overhead
from unnecessary :ref:`type conversion <man-conversion-and-promotion>`.

Examples:

.. doctest::

    julia> zero(Float32)
    0.0f0

    julia> zero(1.0)
    0.0

    julia> one(Int32)
    1

    julia> one(BigFloat)
    1.000000000000000000000000000000000000000000000000000000000000000000000000000000



.. [#rem-and-mod] 訳注: ``rem`` と ``mod`` 関数は、引数が負のときに違った動作をする。

    .. doctest::

        rem(5, 2) => 1, mod(5, 2) => 1
        rem(-5, 2) => -1, mod(-5, 2) => 1
        rem(5, -2) => 1, mod(5, -2) => -1

    参考 - `除算後の剰余 - MATLAB rem - MathWorks 日本 <http://jp.mathworks.com/help/matlab/ref/rem.html?requestedDomain=jp.mathworks.com#btv1i9_-6>`_
