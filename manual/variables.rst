
***********
 変数
***********

A variable, in Julia, is a name associated (or bound) to a value. It's useful when you want to store a value (that you obtained after some math, for example) for later use. For example:

Juliaでは、変数は値に結び付けられた（束縛された）名前です。これは、（例えば計算結果などの）値を後で使うためにしまっておくのに便利です。以下に例を示します。

.. doctest::

    # Assign the value 10 to the variable x
    julia> x = 10
    10

    # Doing math with x's value
    julia> x + 1
    11

    # Reassign x's value
    julia> x = 1 + 1
    2

    # You can assign values of other types, like strings of text
    julia> x = "Hello World!"
    "Hello World!"

.. doctest::

    # 10という値を変数xに割り当てる
    julia> x = 10
    10

    # xの値を使って計算する
    julia> x + 1
    11

    # xに値を再度割り当てる
    julia> x = 1 + 1
    2

    # 別の型の値、例えば文字列を割り当てることもできる
    julia> x = "Hello World!"
    "Hello World!"

Julia provides an extremely flexible system for naming variables.
Variable names are case-sensitive, and have no semantic meaning (that is,
the language will not treat variables differently based on their names).

Juliaでは、非常に柔軟に変数を命名することができます。
変数名の大文字と小文字は区別されますが、セマンティクスでの意味を持つわけではありません（言語は名前によって変数の扱いを変えない、ということです）。

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

Unicode names (in UTF-8 encoding) are allowed:

（UTF-8エンコーディングの）ユニコードでの名前を使うこともできます。

.. raw:: latex

    \begin{CJK*}{UTF8}{mj}

.. doctest::

    julia> δ = 0.00001
    1.0e-5

    julia> 안녕하세요 = "Hello"
    "Hello"

In the Julia REPL and several other Julia editing environments, you
can type many Unicode math symbols by typing the backslashed LaTeX symbol
name followed by tab.  For example, the variable name ``δ`` can be
entered by typing ``\delta``-*tab*, or even ``α̂₂`` by
``\alpha``-*tab*-``\hat``-*tab*-``\_2``-*tab*.

JuliaのREPLや、その他いくつかの編集環境では、バックスラッシュから始まるLaTeX記号名、そしてその後にタブを入力することで、様々なユニコード数学記号を入力することができます。 例えば、　変数名 ``δ`` は ``\delta``-*タブ* 、``α̂₂`` は ``\alpha``-*tab*-``\hat``-*tab*-``\_2``-*tab* とすることで入力することができます。

.. raw:: latex

    \end{CJK*}

Julia will even let you redefine built-in constants and functions if needed:

もし必要であれば、Juliaでは、ビルトインの定数や関数を再定義することすらできます。

.. doctest::

    julia> pi
    π = 3.1415926535897...

    julia> pi = 3
    Warning: imported binding for pi overwritten in module Main
    3

    julia> pi
    3

    julia> sqrt(100)
    10.0

    julia> sqrt = 4
    Warning: imported binding for sqrt overwritten in module Main
    4

However, this is obviously not recommended to avoid potential confusion.

とはいえ、潜在的な混乱を避けるため、これはもちろん推奨されません。

Allowed Variable Names
======================

許可される変数名
======================

Variable names must begin with a letter (A-Z or a-z), underscore, or a
subset of Unicode code points greater than 00A0; in particular, `Unicode character categories`_ Lu/Ll/Lt/Lm/Lo/Nl (letters), Sc/So (currency and
other symbols), and a few other letter-like characters (e.g. a subset
of the Sm math symbols) are allowed. Subsequent characters may also
include ! and digits (0-9 and other characters in categories Nd/No),
as well as other Unicode code points: diacritics and other modifying
marks (categories Mn/Mc/Me/Sk), some punctuation connectors (category
Pc), primes, and a few other characters.

変数名の最初は、アルファベット（A-Zもしくはa-z）、アンダースコア、そして00A0より大きい場所を指し示すユニコードから始まる必要があります。
ユニコードは特に、`Unicode character categories`_ Lu/Ll/Lt/Lm/Lo/Nl（文字）、Sc/So （通貨とその他の記号）、そしていくつかの文字（letter）のような字（character）（例：Sm数学記号の一部）を使うことができます。
最初の文字以降は、!や数字（0-9とNd/Noカテゴリにあるその他の文字）、そしてその他のユニコードも使うことができます。
使用可能なユニコードとしては、　
発音区別記号（訳注: ``´`` や `````、 ``^`` など）やその他の修飾記号（Mn/Mc/Me/Skカテゴリ）、いくつかの句読点（punctuation connectors）（Pcカテゴリ）、プライム記号（訳注: xxx）、そしてその他いくつかの文字があります。

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
- Names of ``Type``\ s and ``Modules``\ s begin with a capital letter and word separation is
  shown with upper camel case instead of underscores.
- Names of ``function``\ s and ``macro``\s are in lower case, without
  underscores.
- Functions that write to their arguments have names that end in ``!``.
  These are sometimes called "mutating" or "in-place" functions because
  they are intended to produce changes in their arguments after the
  function is called, not just return a value.
