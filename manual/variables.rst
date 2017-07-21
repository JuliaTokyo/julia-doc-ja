
***********
 変数
***********

Juliaにおいて、変数とは値に関連付けられた(若しくは縛り付けられた)名前を指します。変数はある値（例えば計算などで導出されたもの）を後に用いる為に保管する時に便利です。例:

.. doctest::

    # 変数 x に値 10 を代入
    julia> x = 10
    10

    # x の値を利用して計算を行う
    julia> x + 1
    11

    # 既に定義された変数 x に別の値を代入
    julia> x = 1 + 1
    2

    # 文字列などの別の種類の値を代入することも可能です
    julia> x = "Hello World!"
    "Hello World!"

Juliaは変数の命名について非常に柔軟なシステムを提供します。
変数名は大文字小文字を区別され、意味論的意味を持ちません。(つまり、
Juliaは変数名によって扱いを変えることはありません。)

.. raw:: latex

    \begin{CJK*}{UTF8}{gbsn}

.. doctest::

    julia> x = 1.0
    1.0

    julia> y = -3
    -3

    julia> Z = "My string"
    "My string"

    julia> customary_phrase = "Hello world!"
    "Hello world!"

    julia> UniversalDeclarationOfHumanRightsStart = "人人生而自由，在尊严和权力上一律平等。"
    "人人生而自由，在尊严和权力上一律平等。"

.. raw:: latex

    \end{CJK*}

(UTF-8でエンコードされる場合は)Unicodeを含んだ変数名も使用可能です:

.. raw:: latex

    \begin{CJK*}{UTF8}{mj}

.. doctest::

    julia> δ = 0.00001
    1.0e-5

    julia> 안녕하세요 = "Hello"
    "Hello"

Julia REPLや他の幾つかの編集環境では、多くのUnicode数学記号をLaTeX式で記述した後に
TABキーを打つことで入力することができます。例として、変数名``δ``は``\delta``-*[TABキー]*で、
変数名``α̂₂``は``\alpha``-*[TABキー]-*``\hat``*-[TABキー]-*``\_2``-*[TABキー]*で入力することができます。

.. raw:: latex

    \end{CJK*}

必要であればビルトインの定数や変数を再定義することができます:

.. doctest::

    julia> pi
    π = 3.1415926535897...

    julia> pi = 3
    WARNING: imported binding for pi overwritten in module Main
    3

    julia> pi
    3

    julia> sqrt(100)
    10.0

    julia> sqrt = 4
    WARNING: imported binding for sqrt overwritten in module Main
    4

しかしながら、これは明らかに混乱を招く恐れがある為、推奨されません。

使用可能な変数名
======================

Variable names must begin with a letter (A-Z or a-z), underscore, or a
subset of Unicode code points greater than 00A0; in particular, `Unicode character categories`_ Lu/Ll/Lt/Lm/Lo/Nl (letters), Sc/So (currency and
other symbols), and a few other letter-like characters (e.g. a subset
of the Sm math symbols) are allowed. Subsequent characters may also
include ! and digits (0-9 and other characters in categories Nd/No),
as well as other Unicode code points: diacritics and other modifying
marks (categories Mn/Mc/Me/Sk), some punctuation connectors (category
Pc), primes, and a few other characters.

.. _Unicode character categories: http://www.fileformat.info/info/unicode/category/index.htm

Operators like ``+`` are also valid identifiers, but are parsed specially. In
some contexts, operators can be used just like variables; for example
``(+)`` refers to the addition function, and ``(+) = f`` will reassign
it.  Most of the Unicode infix operators (in category Sm),
such as ``⊕``, are parsed as infix operators and are available for
user-defined methods (e.g. you can use ``const ⊗ = kron`` to define
``⊗`` as an infix Kronecker product).

The only explicitly disallowed names for variables are the names of built-in
statements:

.. doctest::

    julia> else = false
    ERROR: syntax: unexpected "else"

    julia> try = "No"
    ERROR: syntax: unexpected "="


Stylistic Conventions
=====================

While Julia imposes few restrictions on valid names, it has become useful to
adopt the following conventions:

- Names of variables are in lower case.
- Word separation can be indicated by underscores (``'_'``), but use of
  underscores is discouraged unless the name would be hard to read otherwise.
- Names of ``Type``\ s and ``Module``\ s begin with a capital letter and word separation is
  shown with upper camel case instead of underscores.
- Names of ``function``\ s and ``macro``\s are in lower case, without
  underscores.
- Functions that write to their arguments have names that end in ``!``.
  These are sometimes called "mutating" or "in-place" functions because
  they are intended to produce changes in their arguments after the
  function is called, not just return a value.
