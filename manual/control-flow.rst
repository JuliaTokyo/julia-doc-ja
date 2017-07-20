.. _man-control-flow:

.. currentmodule:: Base

**************
制御フロー
 Control Flow
**************

Julia には、様々な制御フロー構文があります。
Julia provides a variety of control flow constructs:

-  :ref:`man-compound-expressions 複合式`: ``begin`` and ``(;)``.
-  :ref:`man-conditional-evaluation 条件の評価`:
   ``if``-``elseif``-``else`` and ``?:`` (ternary operator 3項演算子).
-  :ref:`man-short-circuit-evaluation 短絡評価`:
   ``&&``, ``||`` and chained comparisons 連鎖比較.
-  :ref:`man-loops 繰り返し`: ``while`` and ``for``.
-  :ref:`man-exception-handling 例外処理`:
   ``try``-``catch``, :func:`error` and :func:`throw`.
-  :ref:`man-tasks タスク`: :func:`yieldto`.

高水準プログラミング言語には、最初の⒌つからなる制御フローメカニズムを標準的に備えています。
Taskはあまり標準的ではありませんが、一時中断して評価の切り替えを可能とする、非ローカルな制御フローを用意します。
これは強力な構造で、例外処理や協調マルチタスクが、Julia には実装されています。
日々のプログラミングにはタスクの直接的な使用を必要としませんが、ある特定の課題はタスクを使用することで容易に解決できます。
The first five control flow mechanisms are standard to high-level
programming languages. :class:`Task`\ s are not so standard: they provide non-local
control flow, making it possible to switch between temporarily-suspended
computations. This is a powerful construct: both exception handling and
cooperative multitasking are implemented in Julia using tasks. Everyday
programming requires no direct usage of tasks, but certain problems can
be solved much more easily by using tasks.

.. _man-compound-expressions:

複合式
Compound Expressions
--------------------

時には、最後の部分式の値を返すために、いくつかの部分式を順に評価する単一式は便利です。
Juliaには、beginブロックと(;)チェーンという２つの構成物があります
複合式の構成物の値は、最後の部分式の値に等しいです。
以下にbeginブロックの例があります。
Sometimes it is convenient to have a single expression which evaluates
several subexpressions in order, returning the value of the last
subexpression as its value. There are two Julia constructs that
accomplish this: ``begin`` blocks and ``(;)`` chains. The value of both
compound expression constructs is that of the last subexpression. Here's
an example of a ``begin`` block:

.. doctest::

    julia> z = begin
             x = 1
             y = 2
             x + y
           end
    3

``(;)``チェーンは、かなり小さく、単純な式であるため、単一ラインで簡単に
式を分けることができ、便利です。
Since these are fairly small, simple expressions, they could easily be
placed onto a single line, which is where the ``(;)`` chain syntax comes
in handy:

.. doctest::

    julia> z = (x = 1; y = 2; x + y)
    3

この構文は、Functionsで紹介した簡潔な単一関数定義で特に有用です。
典型的な構文ですが、``begin``ブロックを複数にしたり、``(;)``チェーンを単一行にしたり
する必要がありません。
This syntax is particularly useful with the terse single-line function
definition form introduced in :ref:`man-functions`. Although it
is typical, there is no requirement that ``begin`` blocks be multiline
or that ``(;)`` chains be single-line:

.. doctest::

    julia> begin x = 1; y = 2; x + y end
    3

    julia> (x = 1;
            y = 2;
            x + y)
    3

.. _man-conditional-evaluation:

条件式
Conditional Evaluation
----------------------

条件式は、コードの一部が評価され、boolean式の値に応じて、評価される、あるいは評価されないことになります。
以下に、``if``-``elseif``-``else``条件付き構文の例があります。
Conditional evaluation allows portions of code to be evaluated or not
evaluated depending on the value of a boolean expression. Here is the
anatomy of the ``if``-``elseif``-``else`` conditional syntax::

    if x < y
      println("x is less than y")
    elseif x > y
      println("x is greater than y")
    else
      println("x is equal to y")
    end

もし、条件式``x < y``が真であれば、対応するブロックが評価されます。
逆に、条件式``x > y``が真であれば、対応するブロックが評価されます。
どちらの式も真ではなければ、``else``ブロックが評価されます。
以下に実行結果があります。
If the condition expression ``x < y`` is ``true``, then the corresponding block
is evaluated; otherwise the condition expression ``x > y`` is evaluated, and if
it is ``true``, the corresponding block is evaluated; if neither expression is
true, the ``else`` block is evaluated. Here it is in action:

.. doctest::

    julia> function test(x, y)
             if x < y
               println("x is less than y")
             elseif x > y
               println("x is greater than y")
             else
               println("x is equal to y")
             end
           end
    test (generic function with 1 method)

    julia> test(1, 2)
    x is less than y

    julia> test(2, 1)
    x is greater than y

    julia> test(1, 1)
    x is equal to y

``elseif``ブロック、``else``ブロックは、オプションです。
``elseif``ブロックは、要望に応じて利用されます。
``if``-``elseif``-``else``構造は、最初に真と評価されるまで評価します。
真であるブロックが評価された後は、条件式・ブロックとも評価されません。
The ``elseif`` and ``else`` blocks are optional, and as many ``elseif``
blocks as desired can be used. The condition expressions in the
``if``-``elseif``-``else`` construct are evaluated until the first one
evaluates to ``true``, after which the associated block is evaluated,
and no further condition expressions or blocks are evaluated.

``if``ブロックには、漏出性があります。すなわち、ローカルスコープを導入しません。
たとえ、以前に新しい変数が定義されていなくても、``ìf``句の中で新しい変数が定義されたら、
``ìf``ブロックの後も使うことができます。
そのため、上記のような``test``機能を定義します。
``if`` blocks are "leaky", i.e. they do not introduce a local scope.
This means that new variables defined inside the ``ìf`` clauses can
be used after the ``if`` block, even if they weren't defined before.
So, we could have defined the ``test`` function above as

.. doctest::

    julia> function test(x,y)
             if x < y
               relation = "less than"
             elseif x == y
               relation = "equal to"
             else
               relation = "greater than"
             end
             println("x is ", relation, " y.")
           end
    test (generic function with 1 method)

The variable ``relation`` is declared inside the ``if`` block, but used
outside. However, when depending on this behavior, make sure all possible
code paths define a value for the variable. The following change to
the above function results in a runtime error

.. doctest::

    julia> function test(x,y)
             if x < y
               relation = "less than"
             elseif x == y
               relation = "equal to"
             end
             println("x is ", relation, " y.")
           end
    test (generic function with 1 method)

    julia> test(1,2)
    x is less than y.

    julia> test(2,1)
    ERROR: UndefVarError: relation not defined
     in test at none:7

``if``ブロックは、値を返します。これは多くの他の言語に由来するため、ユーザにとって直感的に思えます。
この値は、ただブランチの最後に実行された文の返り値であるため、
``if`` blocks also return a value, which may seem unintuitive to users
coming from many other languages. This value is simply the return value
of the last executed statement in the branch that was chosen, so

.. doctest::

    julia> x = 3
    3

    julia> if x > 0
               "positive!"
           else
               "negative..."
           end
    "positive!"

次のセクションで説明している通り、非常に短い条件文(ワンライナー)は、Juliaでは、
頻繁にショートカット評価として使用されていることに注意してください。
Note that very short conditional statements (one-liners) are frequently expressed using
Short-Circuit Evaluation in Julia, as outlined in the next section.

C, MATLAB, Perl, Python, およびRuby,と違って、しかしJavaのようないくつかの厳格な型付き言語は、
条件式の値が``true``でも``false``でもない場合は、エラーになります。
Unlike C, MATLAB, Perl, Python, and Ruby — but like Java, and a few
other stricter, typed languages — it is an error if the value of a
conditional expression is anything but ``true`` or ``false``:

.. doctest::

    julia> if 1
             println("true")
           end
    ERROR: TypeError: non-boolean (Int64) used in boolean context

このエラーは、条件付きの型が間違っていることを示しています。
``Int64`` よりも``Bool``で判定されることを必要としています。
This error indicates that the conditional was of the wrong type:
:obj:`Int64`` rather than the required :obj:`Bool`.

いわゆる、三項演算子の?は、``if``-``elseif``-``else``構文と密接な関係がありますが、
長いコードブロックの条件表現とは対照的に、単一式の値の間で、
条件選択を表現するものとして使用されています。
3つのオペランドを取るほとんどの言語で、唯一の演算子があることから、
三項演算子という名称が使用されています。
The so-called "ternary operator", ``?:``, is closely related to the
``if``-``elseif``-``else`` syntax, but is used where a conditional
choice between single expression values is required, as opposed to
conditional execution of longer blocks of code. It gets its name from
being the only operator in most languages taking three operands::

    a ? b : c

``?``の前の式``a``が条件式となり、三項演算子は、条件``a``が``true``であれば
``:``の前の式``b``を評価し、``false``であれば、``:``の後の式``c``を評価します。
The expression ``a``, before the ``?``, is a condition expression, and
the ternary operation evaluates the expression ``b``, before the ``:``,
if the condition ``a`` is ``true`` or the expression ``c``, after the
``:``, if it is ``false``.

この動作を理解する最も簡単な方法は、例を見ることです。
前の例では、``println`` コールは、3つのブロックの中にあります。
文字列リテラルを出力するのに、唯一の現実的な選択です。
これは、三項演算子を使用して、より簡潔に書くことができます。
明確にするために、相互の例を試してみましょう。
The easiest way to understand this behavior is to see an example. In the
previous example, the ``println`` call is shared by all three branches:
the only real choice is which literal string to print. This could be
written more concisely using the ternary operator. For the sake of
clarity, let's try a two-way version first:

.. doctest::

    julia> x = 1; y = 2;

    julia> println(x < y ? "less than" : "not less than")
    less than

    julia> x = 1; y = 0;

    julia> println(x < y ? "less than" : "not less than")
    not less than

条件式``x < y``が真ならば、三項演算子の中で、文字列``"less than"``が評価されます。
逆の場合、文字列``"not less than"``が評価されます。
オリジナルの3つの例では、コロンでつなぐ複数の使用法を三項演算子に要求しています。
If the expression ``x < y`` is true, the entire ternary operator
expression evaluates to the string ``"less than"`` and otherwise it
evaluates to the string ``"not less than"``. The original three-way
example requires chaining multiple uses of the ternary operator
together:

.. doctest::

    julia> test(x, y) = println(x < y ? "x is less than y"    :
                                x > y ? "x is greater than y" : "x is equal to y")
    test (generic function with 1 method)

    julia> test(1, 2)
    x is less than y

    julia> test(2, 1)
    x is greater than y

    julia> test(1, 1)
    x is equal to y

つなぐことを容易にするために、オペレータは右から左へと結合させます。
それは、`if``-``elseif``-``else``のように重要であり、
条件式が``true`` または ``false``それぞれを評価したら、
``:``の前と後ろの式のどちらかが評価されます。
To facilitate chaining, the operator associates from right to left.
It is significant that like ``if``-``elseif``-``else``, the expressions
before and after the ``:`` are only evaluated if the condition
expression evaluates to ``true`` or ``false``, respectively:

.. doctest::

    julia> v(x) = (println(x); x)
    v (generic function with 1 method)


    julia> 1 < 2 ? v("yes") : v("no")
    yes
    "yes"

    julia> 1 > 2 ? v("yes") : v("no")
    no
    "no"

.. _man-short-circuit-evaluation:

短絡評価
Short-Circuit Evaluation
------------------------

短絡評価は、条件評価と、とても似ています。
その振る舞いは、論理演算子``&&`` と ``||``を持つ、
多くの必須プログラミング言語に見受けられます。
これらの演算子で接続された一連の論理式では、
チェーン全体の最終的な真偽値を決定するために、
ただ１つの式の値がチェーン全体を評価されます。
明示的に以下を意味します。

Short-circuit evaluation is quite similar to conditional evaluation. The
behavior is found in most imperative programming languages having the
``&&`` and ``||`` boolean operators: in a series of boolean expressions
connected by these operators, only the minimum number of expressions are
evaluated as are necessary to determine the final boolean value of the
entire chain. Explicitly, this means that:

``a && b``式では、``a`` が真である場合、部分式``b``が評価されます。
``a || b``式では、``a`` が偽である場合、部分式``b``が評価されます。

-  In the expression ``a && b``, the subexpression ``b`` is only
   evaluated if ``a`` evaluates to ``true``.

-  In the expression ``a || b``, the subexpression ``b`` is only
   evaluated if ``a`` evaluates to ``false``.

もし、``a``が``false``であれば、``b``の値に関わらず、
``a && b``は、``false``になります。
同じように、もし、``a``が``true``であれば、``b``の値に関わらず、
``a && b``は、``true``になります。
``&&`` と ``||`` とも、右側に関連がありますが、``&&`` は、``||``より
優先度が高いです。この動作を試すことは簡単です。

The reasoning is that ``a && b`` must be ``false`` if ``a`` is
``false``, regardless of the value of ``b``, and likewise, the value of
``a || b`` must be true if ``a`` is ``true``, regardless of the value of
``b``. Both ``&&`` and ``||`` associate to the right, but ``&&`` has
higher precedence than ``||`` does. It's easy to experiment with
this behavior:

.. doctest::

    julia> t(x) = (println(x); true)
    t (generic function with 1 method)

    julia> f(x) = (println(x); false)
    f (generic function with 1 method)

    julia> t(1) && t(2)
    1
    2
    true

    julia> t(1) && f(2)
    1
    2
    false

    julia> f(1) && t(2)
    1
    false

    julia> f(1) && f(2)
    1
    false

    julia> t(1) || t(2)
    1
    true

    julia> t(1) || f(2)
    1
    true

    julia> f(1) || t(2)
    1
    2
    true

    julia> f(1) || f(2)
    1
    2
    false

``&&``演算子 と ``||``演算子の様々な組み合わせで、同じ方法で、
容易に結合性と優位性の実験をすることができます。
You can easily experiment in the same way with the associativity and
precedence of various combinations of ``&&`` and ``||`` operators.

次の振る舞いは、とても短い``if``文として、Juliaでは代替物としてよく使われます。
``if <cond> <statement> end``の代わりに、``<cond> && <statement>``を
書くことができます。(<cond> *and then* <statement>と読み変えることができます。)
``if ! <cond> <statement> end``の代わりに、``<cond> && <statement>``を
書くことができます。(<cond> *or else* <statement>と読み変えることができます。)
This behavior is frequently used in Julia to form an alternative to very short
``if`` statements. Instead of ``if <cond> <statement> end``, one can write
``<cond> && <statement>`` (which could be read as: <cond> *and then* <statement>).
Similarly, instead of ``if ! <cond> <statement> end``, one can write
``<cond> || <statement>`` (which could be read as: <cond> *or else* <statement>).

例えば、再帰的な階乗ルーチンは次のように定義することができます。
For example, a recursive factorial routine could be defined like this:

.. doctest::

    julia> function factorial(n::Int)
               n >= 0 || error("n must be non-negative")
               n == 0 && return 1
               n * factorial(n-1)
           end
    factorial (generic function with 1 method)

    julia> factorial(5)
    120

    julia> factorial(0)
    1

    julia> factorial(-1)
    ERROR: n must be non-negative
     in factorial at none:2

短絡評価なしの論理演算は、"数学演算と初等関数"で紹介されたビット単位の論理演算子、
``&`` と ``|``で式を終わらせることができます。
これらは、中置演算子の構文をサポートしていますが、
常に引数を評価する正常な機能を持っています。
Boolean operations *without* short-circuit evaluation can be done with the
bitwise boolean operators introduced in :ref:`man-mathematical-operations`:
``&`` and ``|``. These are normal functions, which happen to support
infix operator syntax, but always evaluate their arguments:

.. doctest::

    julia> f(1) & t(2)
    1
    2
    false

    julia> t(1) | t(2)
    1
    2
    true

``if``, ``elseif``または三項演算子で使われる条件式のように、
``&&`` または ``||``のオペランドは、ブール値、つまり``true`` または ``false``に
しなければならないです。
条件付きチェーンでの最後の登場を除いて、
非ブール値を使用するとエラーになります。
Just like condition expressions used in ``if``, ``elseif`` or the
ternary operator, the operands of ``&&`` or ``||`` must be boolean
values (``true`` or ``false``). Using a non-boolean value anywhere
except for the last entry in a conditional chain is an error:

.. doctest::

    julia> 1 && true
    ERROR: TypeError: non-boolean (Int64) used in boolean context

他方、条件つきのチェーンの終わりでは、どんな種類の式でも使用されるでしょう。
それは、先行する条件式に応じて、評価されたり、リターンされたりするでしょう。

On the other hand, any type of expression can be used at the end of a conditional chain.
It will be evaluated and returned depending on the preceding conditionals:

.. testsetup::

    srand(123);

.. doctest::

    julia> true && (x = rand(2,2))
    2x2 Array{Float64,2}:
     0.768448  0.673959
     0.940515  0.395453

    julia> false && (x = rand(2,2))
    false

.. _man-loops:

繰り返し評価：ループ
Repeated Evaluation: Loops
--------------------------

繰り返しを評価する式の構文は２つあります。``while``文と``for``文が該当します。
以下に、 ``while``の例があります。
There are two constructs for repeated evaluation of expressions: the
``while`` loop and the ``for`` loop. Here is an example of a ``while``
loop:

.. doctest::

    julia> i = 1;

    julia> while i <= 5
             println(i)
             i += 1
           end
    1
    2
    3
    4
    5

``while``文は、条件式を評価します。(上記例ですと、``i <= 5``になります。)
条件式が、``true``であり続ける限り、プログラム本文を評価し続けます。
条件式が``false``になったら、``while``文に到達しても、
プログラム本文は決して評価されません。
The ``while`` loop evaluates the condition expression (``i <= 5`` in this
case), and as long it remains ``true``, keeps also evaluating the body
of the ``while`` loop. If the condition expression is ``false`` when the
``while`` loop is first reached, the body is never evaluated.

``for``文は、共通の繰り返し評価イディオムを書きやすくします。 
数をカウントアップ・カウントダウンするのに、
上記のようなwhile文を使用することは一般的ではありません。
``for``文で簡潔に表現することができます。：
The ``for`` loop makes common repeated evaluation idioms easier to
write. Since counting up and down like the above ``while`` loop does is
so common, it can be expressed more concisely with a ``for`` loop:

.. doctest::

    julia> for i = 1:5
             println(i)
           end
    1
    2
    3
    4
    5

ここにある``1:5`` は、``Range``オブジェクトを表し、1, 2, 3, 4, 5という数字の続きを
表します。
``for``ループは、それぞれの数字を変数``i``に順番に割り当てることで、
値を繰り返します。
先の``while`` ループと``for``ループは変数のスコープで区別できます。
繰り返し文であっても、``for`` ループ内で使用できた変数が、ループ外では
使用できません。
これをテストするためには、新しいインタラクティブセッション・インスタンス
または別の変数名のどちらかが必要になります。

Here the ``1:5`` is a ``Range`` object, representing the sequence of
numbers 1, 2, 3, 4, 5. The ``for`` loop iterates through these values,
assigning each one in turn to the variable ``i``. One rather important
distinction between the previous ``while`` loop form and the ``for``
loop form is the scope during which the variable is visible. If the
variable ``i`` has not been introduced in an other scope, in the ``for``
loop form, it is visible only inside of the ``for`` loop, and not
afterwards. You'll either need a new interactive session instance or a
different variable name to test this:

.. doctest::

    julia> for j = 1:5
             println(j)
           end
    1
    2
    3
    4
    5

    julia> j
    ERROR: UndefVarError: j not defined

変数スコープの詳細な説明とJuliaでの動作については、
`man-variables-and-scoping`をご覧ください。
See :ref:`man-variables-and-scoping` for a detailed
explanation of variable scope and how it works in Julia.

一般的に、``for``ループ構文は、配列コンテナを繰り返します。
これらの場合、コードをより読みやすくするため、 ``in``キーワード が、``=``の代わりに、
同等のものとして、使われています。
In general, the ``for`` loop construct can iterate over any container.
In these cases, the alternative (but fully equivalent) keyword ``in`` is
typically used instead of ``=``, since it makes the code read more
clearly:

.. doctest::

    julia> for i in [1,4,0]
             println(i)
           end
    1
    4
    0

    julia> for s in ["foo","bar","baz"]
             println(s)
           end
    foo
    bar
    baz

繰り返し構文の様々なタイプが紹介されました。後半のmanulセクション、
`man-arrays`で説明します。
Various types of iterable containers will be introduced and discussed in
later sections of the manual (see, e.g., :ref:`man-arrays`).

テスト条件を偽造するる前に``while``繰り返しを止めたり、
繰り返し文の最後に到達する前に、``for``繰り返しを止めたりする時にしばしば便利です。
これは、``break`` で実現できます。
It is sometimes convenient to terminate the repetition of a ``while``
before the test condition is falsified or stop iterating in a ``for``
loop before the end of the iterable object is reached. This can be
accomplished with the ``break`` keyword:

.. doctest::

    julia> i = 1;

    julia> while true
             println(i)
             if i >= 5
               break
             end
             i += 1
           end
    1
    2
    3
    4
    5

    julia> for i = 1:1000
             println(i)
             if i >= 5
               break
             end
           end
    1
    2
    3
    4
    5

上部の``while`` ループは、自分自身で決して停止しません。
``for`` ループは、1000回繰り返すまで停止しません。
これらのループを早期に停止するため、``break``を使用します。
The above ``while`` loop would never terminate on its own, and the
``for`` loop would iterate up to 1000. These loops are both exited early
by using the ``break`` keyword.

他のケースでは、繰り返しを停止するのに便利な、``continue``を使って、
直ちに次の回数に移動することができます。
In other circumstances, it is handy to be able to stop an iteration and
move on to the next one immediately. The ``continue`` keyword
accomplishes this:

.. doctest::

    julia> for i = 1:10
             if i % 3 != 0
               continue
             end
             println(i)
           end
    3
    6
    9

これは、条件を否定し、``if``ブロックの中に``println``コールを置くことによって、
同じ振る舞いを明らかにしているため、やや不自然な例である。
現実的な使用法では、``continue``の後に、多くのコードを評価し、
``continue``を呼ぶ複数のポイントがあります。
This is a somewhat contrived example since we could produce the same
behavior more clearly by negating the condition and placing the
``println`` call inside the ``if`` block. In realistic usage there is
more code to be evaluated after the ``continue``, and often there are
multiple points from which one calls ``continue``.


ネストされた多重``for`` ループは、デカルト積を形成し、単一の外部ループに
組み合わせることができます。
Multiple nested ``for`` loops can be combined into a single outer loop,
forming the cartesian product of its iterables:


.. doctest::

    julia> for i = 1:2, j = 3:4
             println((i, j))
           end
    (1,3)
    (1,4)
    (2,3)
    (2,4)


ループの中での``break``文は、内部のネストではなく、ループ全体のネストを抜けます。
A ``break`` statement inside such a loop exits the entire nest of loops,
not just the inner one.

.. _man-exception-handling:

例外処理
Exception Handling
------------------
予期しない状況が発生した場合、関数は、呼び出し元に適切な値を返すことができない場合があります。
そのような場合は、例外処理が最も適切です。
診断的なエラーメッセージを出力してプログラムを停止するか、
プログラマがコードに例外的な状況を作って、適切なアクションを取るかです。
When an unexpected condition occurs, a function may be unable to return
a reasonable value to its caller. In such cases, it may be best for the
exceptional condition to either terminate the program, printing a
diagnostic error message, or if the programmer has provided code to
handle such exceptional circumstances, allow that code to take the
appropriate action.

Built-in :exc:`Exception`\ s
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

予期しない条件が発生したとき、`Exception`がスローされます。
下記一覧の`Exception`は通常の制御の流れを遮断します。
:exc:`Exception`\ s are thrown when an unexpected condition has occurred. The
built-in :exc:`Exception`\ s listed below all interrupt the normal flow of control.

+------------------------------+
| :exc:`Exception`             |
+==============================+
| :exc:`ArgumentError`         |
+------------------------------+
| :exc:`BoundsError`           |
+------------------------------+
| :exc:`CompositeException`    |
+------------------------------+
| :exc:`DivideError`           |
+------------------------------+
| :exc:`DomainError`           |
+------------------------------+
| :exc:`EOFError`              |
+------------------------------+
| :exc:`ErrorException`        |
+------------------------------+
| :exc:`InexactError`          |
+------------------------------+
| :exc:`InitError`             |
+------------------------------+
| :exc:`InterruptException`    |
+------------------------------+
| :exc:`InvalidStateException` |
+------------------------------+
| :exc:`KeyError`              |
+------------------------------+
| :exc:`LoadError`             |
+------------------------------+
| :exc:`OutOfMemoryError`      |
+------------------------------+
| :exc:`ReadOnlyMemoryError`   |
+------------------------------+
| :exc:`RemoteException`       |
+------------------------------+
| :exc:`MethodError`           |
+------------------------------+
| :exc:`OverflowError`         |
+------------------------------+
| :exc:`ParseError`            |
+------------------------------+
| :exc:`SystemError`           |
+------------------------------+
| :exc:`TypeError`             |
+------------------------------+
| :exc:`UndefRefError`         |
+------------------------------+
| :exc:`UndefVarError`         |
+------------------------------+
| :exc:`UnicodeError`          |
+------------------------------+


例えば、`sqrt`関数は、負の値を適用すると、`DomainError`がスローされます。
For example, the :func:`sqrt` function throws a :exc:`DomainError` if applied to a
negative real value:

.. doctest::

    julia> sqrt(-1)
    ERROR: DomainError:
    sqrt will only return a complex result if called with a complex argument. Try sqrt(complex(x)).
     in sqrt at math.jl:146

次のように独自の例外を定義することができます。
You may define your own exceptions in the following way:

.. doctest::

    julia> type MyCustomException <: Exception end

`throw`関数
The :func:`throw` function
~~~~~~~~~~~~~~~~~~~~~~~~~~
`throw`を使って、例外を明示的に作成することができます。
例えば、負ではない数を定義された関数が、引数が負の場合、`throw`の引数である`DomainError`を
呼び出します。
Exceptions can be created explicitly with :func:`throw`. For example, a function
defined only for nonnegative numbers could be written to :func:`throw` a :exc:`DomainError`
if the argument is negative:

.. doctest::

    julia> f(x) = x>=0 ? exp(-x) : throw(DomainError())
    f (generic function with 1 method)

    julia> f(1)
    0.36787944117144233

    julia> f(-1)
    ERROR: DomainError:
     in f at none:1

括弧なしの`DomainError`は例外ではありませんが、例外の一種であることに注意してください。
これは、`Exception`オブジェクトを取得するために呼び出す必要があります。
Note that :exc:`DomainError` without parentheses is not an exception, but a type of
exception. It needs to be called to obtain an :exc:`Exception` object:

.. doctest::

    julia> typeof(DomainError()) <: Exception
    true

    julia> typeof(DomainError) <: Exception
    false

さらに、いくつかの例外では、エラー報告のために使用される、1つ以上の引数を取ります。
Additionally, some exception types take one or more arguments that are used for
error reporting:

.. doctest::

    julia> throw(UndefVarError(:x))
    ERROR: UndefVarError: x not defined

このメカニズムは、次の`UndefVarError`で書かれた方法のように、
カスタムされた例外によって、容易に実現できます。
This mechanism can be implemented easily by custom exception types following
the way :exc:`UndefVarError` is written:

.. doctest::

    julia> type MyUndefVarError <: Exception
               var::Symbol
           end
    julia> Base.showerror(io::IO, e::MyUndefVarError) = print(io, e.var, " not defined");

エラー
Errors
~~~~~~

`error`関数は、正常なフローの制御を中断する、`ErrorException`を引き起こすために使われます。
The :func:`error` function is used to produce an :exc:`ErrorException` that
interrupts the normal flow of control.

負の数の平方根を取った場合、直ちに実行を停止するとします。
これを行うためには、`sqrt`関数の引数が負の場合、エラーを引き起こす、
``fussy``バージョンの`sqrt`関数を定義することができます。
Suppose we want to stop execution immediately if the square root of a
negative number is taken. To do this, we can define a fussy version of
the :func:`sqrt` function that raises an error if its argument is negative:

.. doctest::

    julia> fussy_sqrt(x) = x >= 0 ? sqrt(x) : error("negative x not allowed")
    fussy_sqrt (generic function with 1 method)

    julia> fussy_sqrt(2)
    1.4142135623730951

    julia> fussy_sqrt(-1)
    ERROR: negative x not allowed
     in fussy_sqrt at none:1

``fussy_sqrt``関数は、関数呼び出しの実行を続けようとする代わりに、
他の関数から負の数を呼び出したら、直ちに返し、
対話セッションでエラーメッセージを表示します。
If ``fussy_sqrt`` is called with a negative value from another function,
instead of trying to continue execution of the calling function, it
returns immediately, displaying the error message in the interactive
session:

.. doctest::

    julia> function verbose_fussy_sqrt(x)
             println("before fussy_sqrt")
             r = fussy_sqrt(x)
             println("after fussy_sqrt")
             return r
           end
    verbose_fussy_sqrt (generic function with 1 method)

    julia> verbose_fussy_sqrt(2)
    before fussy_sqrt
    after fussy_sqrt
    1.4142135623730951

    julia> verbose_fussy_sqrt(-1)
    before fussy_sqrt
    ERROR: negative x not allowed
     in verbose_fussy_sqrt at none:3

警告や情報メッセージ
Warnings and informational messages
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
(***36)
Julia also provides other functions that write messages to the standard error
I/O, but do not throw any :exc:`Exception`\ s and hence do not interrupt
execution.:

.. doctest::

    julia> info("Hi"); 1+1
    INFO: Hi
    2

    julia> warn("Hi"); 1+1
    WARNING: Hi
    2

    julia> error("Hi"); 1+1
    ERROR: Hi
     in error at ./error.jl:21


`try/catch`文
The ``try/catch`` statement
~~~~~~~~~~~~~~~~~~~~~~~~~~~
(***37)
The ``try/catch`` statement allows for :exc:`Exception`\ s to be tested for. For
example, a customized square root function can be written to automatically
call either the real or complex square root method on demand using
:exc:`Exception`\ s :

.. doctest::

    julia> f(x) = try
             sqrt(x)
           catch
             sqrt(complex(x, 0))
           end
    f (generic function with 1 method)

    julia> f(1)
    1.0

    julia> f(-1)
    0.0 + 1.0im

(***38)
It is important to note that in real code computing this function, one would
compare ``x`` to zero instead of catching an exception. The exception is much
slower than simply comparing and branching.

(***39)
``try/catch`` statements also allow the :exc:`Exception` to be saved in a
variable. In this contrived example, the following example calculates the
square root of the second element of ``x`` if ``x`` is indexable, otherwise
assumes ``x`` is a real number and returns its square root:

.. doctest::

    julia> sqrt_second(x) = try
             sqrt(x[2])
           catch y
             if isa(y, DomainError)
               sqrt(complex(x[2], 0))
             elseif isa(y, BoundsError)
               sqrt(x)
             end
           end
    sqrt_second (generic function with 1 method)

    julia> sqrt_second([1 4])
    2.0

    julia> sqrt_second([1 -4])
    0.0 + 2.0im

    julia> sqrt_second(9)
    3.0

    julia> sqrt_second(-9)
    ERROR: DomainError:
     in sqrt_second at none:7

(***40)
Note that the symbol following ``catch`` will always be interpreted as a
name for the exception, so care is needed when writing ``try/catch`` expressions
on a single line. The following code will *not* work to return the value of ``x``
in case of an error::

    try bad() catch x end

(***41)
Instead, use a semicolon or insert a line break after ``catch``::

    try bad() catch; x end

    try bad()
    catch
      x
    end

(***42)
The ``catch`` clause is not strictly necessary; when omitted, the default
return value is ``nothing``.

.. doctest::

    julia> try error() end #Returns nothing

(***43, 44)
The power of the ``try/catch`` construct lies in the ability to unwind a deeply
nested computation immediately to a much higher level in the stack of calling
functions. There are situations where no error has occurred, but the ability to
unwind the stack and pass a value to a higher level is desirable. Julia
provides the :func:`rethrow`, :func:`backtrace` and :func:`catch_backtrace` functions for
more advanced error handling.

finally Clauses
~~~~~~~~~~~~~~~

(***45, 46)
In code that performs state changes or uses resources like files, there is
typically clean-up work (such as closing files) that needs to be done when the
code is finished. Exceptions potentially complicate this task, since they can
cause a block of code to exit before reaching its normal end. The ``finally``
keyword provides a way to run some code when a given block of code exits,
regardless of how it exits.

For example, here is how we can guarantee that an opened file is closed::

    f = open("file")
    try
        # operate on file f
    finally
        close(f)
    end

(***47)
When control leaves the ``try`` block (for example due to a ``return``, or
just finishing normally), ``close(f)`` will be executed. If
the ``try`` block exits due to an exception, the exception will continue
propagating. A ``catch`` block may be combined with ``try`` and ``finally``
as well. In this case the ``finally`` block will run after ``catch`` has
handled the error.

.. _man-tasks:

Tasks (aka Coroutines)
----------------------
(***48)
Tasks are a control flow feature that allows computations to be
suspended and resumed in a flexible manner. This feature is sometimes
called by other names, such as symmetric coroutines, lightweight
threads, cooperative multitasking, or one-shot continuations.

(***49, 50)
When a piece of computing work (in practice, executing a particular
function) is designated as a :class:`Task`, it becomes possible to interrupt
it by switching to another :class:`Task`. The original :class:`Task` can later be
resumed, at which point it will pick up right where it left off. At
first, this may seem similar to a function call. However there are two
key differences. First, switching tasks does not use any space, so any
number of task switches can occur without consuming the call stack.
Second, switching among tasks can occur in any order, unlike function calls,
where the called function must finish executing before control returns
to the calling function.

(***51, 52)
This kind of control flow can make it much easier to solve certain
problems. In some problems, the various pieces of required work are not
naturally related by function calls; there is no obvious "caller" or
"callee" among the jobs that need to be done. An example is the
producer-consumer problem, where one complex procedure is generating
values and another complex procedure is consuming them. The consumer
cannot simply call a producer function to get a value, because the
producer may have more values to generate and so might not yet be ready
to return. With tasks, the producer and consumer can both run as long as
they need to, passing values back and forth as necessary.

(***53)
Julia provides the functions :func:`produce` and :func:`consume` for solving
this problem. A producer is a function that calls :func:`produce` on each
value it needs to produce:

.. doctest::

    julia> function producer()
             produce("start")
             for n=1:4
               produce(2n)
             end
             produce("stop")
           end;
(***54)
To consume values, first the producer is wrapped in a :class:`Task`,
then :func:`consume` is called repeatedly on that object:

.. doctest::

    julia> p = Task(producer);

    julia> consume(p)
    "start"

    julia> consume(p)
    2

    julia> consume(p)
    4

    julia> consume(p)
    6

    julia> consume(p)
    8

    julia> consume(p)
    "stop"

(***55)
One way to think of this behavior is that ``producer`` was able to
return multiple times. Between calls to :func:`produce`, the producer's
execution is suspended and the consumer has control.

(***56)
A Task can be used as an iterable object in a ``for`` loop, in which
case the loop variable takes on all the produced values:

.. doctest::

    julia> for x in Task(producer)
             println(x)
           end
    start
    2
    4
    6
    8
    stop

(***57)
Note that the :func:`Task` constructor expects a 0-argument function. A
common pattern is for the producer to be parameterized, in which case a
partial function application is needed to create a 0-argument :ref:`anonymous
function <man-anonymous-functions>`. This can be done either
directly or by use of a convenience macro::

    function mytask(myarg)
        ...
    end

    taskHdl = Task(() -> mytask(7))
    # or, equivalently
    taskHdl = @task mytask(7)

(***58)
:func:`produce` and :func:`consume` do not launch threads that can run on separate CPUs.
True kernel threads are discussed under the topic of :ref:`man-parallel-computing`.

Core task operations
~~~~~~~~~~~~~~~~~~~~

(***59, 60)
While :func:`produce` and :func:`consume` illustrate the essential nature of tasks, they
are actually implemented as library functions using a more primitive function,
:func:`yieldto`. ``yieldto(task,value)`` suspends the current task, switches
to the specified ``task``, and causes that task's last :func:`yieldto` call to return
the specified ``value``. Notice that :func:`yieldto` is the only operation required
to use task-style control flow; instead of calling and returning we are always
just switching to a different task. This is why this feature is also called
"symmetric coroutines"; each task is switched to and from using the same mechanism.

(***61, 62)
:func:`yieldto` is powerful, but most uses of tasks do not invoke it directly.
Consider why this might be. If you switch away from the current task, you will
probably want to switch back to it at some point, but knowing when to switch
back, and knowing which task has the responsibility of switching back, can
require considerable coordination. For example, :func:`produce` needs to maintain
some state to remember who the consumer is. Not needing to manually keep track
of the consuming task is what makes :func:`produce` easier to use than :func:`yieldto`.

(***63)
In addition to :func:`yieldto`, a few other basic functions are needed to use tasks
effectively.

- :func:`current_task` gets a reference to the currently-running task.
- :func:`istaskdone` queries whether a task has exited.
- :func:`istaskstarted` queries whether a task has run yet.
- :func:`task_local_storage` manipulates a key-value store specific to the current task.

Tasks and events
~~~~~~~~~~~~~~~~
(***64)
Most task switches occur as a result of waiting for events such as I/O
requests, and are performed by a scheduler included in the standard library.
The scheduler maintains a queue of runnable tasks, and executes an event loop
that restarts tasks based on external events such as message arrival.

(***65)
The basic function for waiting for an event is :func:`wait`. Several objects
implement :func:`wait`; for example, given a :class:`Process` object, :func:`wait` will
wait for it to exit. :func:`wait` is often implicit; for example, a :func:`wait`
can happen inside a call to :func:`read` to wait for data to be available.

(***66, 67)
In all of these cases, :func:`wait` ultimately operates on a :class:`Condition`
object, which is in charge of queueing and restarting tasks. When a task
calls :func:`wait` on a :class:`Condition`, the task is marked as non-runnable, added
to the condition's queue, and switches to the scheduler. The scheduler will
then pick another task to run, or block waiting for external events.
If all goes well, eventually an event handler will call :func:`notify` on the
condition, which causes tasks waiting for that condition to become runnable
again.

(***68, 69)
A task created explicitly by calling :class:`Task` is initially not known to the
scheduler. This allows you to manage tasks manually using :func:`yieldto` if
you wish. However, when such a task waits for an event, it still gets restarted
automatically when the event happens, as you would expect. It is also
possible to make the scheduler run a task whenever it can, without necessarily
waiting for any events. This is done by calling :func:`schedule`, or using
the :obj:`@schedule` or :obj:`@async` macros (see :ref:`man-parallel-computing` for
more details).

Task states
~~~~~~~~~~~

(***70)
Tasks have a ``state`` field that describes their execution status. A task
state is one of the following symbols:

=============  ==================================================
Symbol         Meaning
=============  ==================================================
``:runnable``  Currently running, or available to be switched to
``:waiting``   Blocked waiting for a specific event
``:queued``    In the scheduler's run queue about to be restarted
``:done``      Successfully finished executing
``:failed``    Finished with an uncaught exception
=============  ==================================================
