.. _man-getting-started:

*****************
始めよう
*****************

Julia installation is straightforward, whether using precompiled
binaries or compiling from source. Download and install Julia by
following the instructions at
`http://julialang.org/downloads/ <http://julialang.org/downloads/>`_.

コンパイル済のバイナリを使うのであれ、ソースからコンパイルするのであれ、Juliaのインストールは簡単です。`http://julialang.org/downloads/ <http://julialang.org/downloads/>`_ にある手順に従い、Juliaをダウンロードしてインストールしてください。

The easiest way to learn and experiment with Julia is by starting an
interactive session (also known as a read-eval-print loop or "repl")
by double-clicking the Julia executable or running ``julia`` from the
command line::

Juliaを学んだり試したりする一番簡単な方法は、インタラクティブ・セッション（read-eval-print loop、もしくは"repl"とも呼ばれるものです）を使ったものです。セッションを起動するには、Juliaの実行ファイルをダブルクリックするか、コマンドラインから ``julia`` を実行します。

::

    $ julia
                   _
       _       _ _(_)_     |  A fresh approach to technical computing
      (_)     | (_) (_)    |  Documentation: http://docs.julialang.org
       _ _   _| |_  __ _   |  Type "?help" for help.
      | | | | | | |/ _` |  |
      | | |_| | | | (_| |  |  Version 0.4.1 (2015-11-08 10:33 UTC)
     _/ |\__'_|_|_|\__'_|  |  Official http://julialang.org/ release
    |__/                   |  x86_64-apple-darwin13.4.0

    julia> 1 + 2
    3

    julia> ans
    3



To exit the interactive session, type ``^D`` — the control key
together with the ``d`` key or type ``quit()``. When run in interactive
mode, ``julia`` displays a banner and prompts the user for input. Once
the user has entered a complete expression, such as ``1 + 2``, and
hits enter, the interactive session evaluates the expression and shows
its value. If an expression is entered into an interactive session
with a trailing semicolon, its value is not shown. The variable
``ans`` is bound to the value of the last evaluated expression whether
it is shown or not. The ``ans`` variable is only bound in interactive
sessions, not when Julia code is run in other ways.

インタラクティブ・セッションを終了するには、``^D`` — コントロールキーと ``d`` キーを同時に押す — もしくは、 ``quit()`` と入力してください。インタラクティブ・モードで起動すると、 ``julia`` のバナーが表示され、プロンプトがユーザーの入力を待ちます。ユーザーが完全な式、例えば ``1 + 2`` を入力し、エンターキーを押すと、その式が評価され、値が表示されます。式の後ろにセミコロンをつけて入力すると、値は表示されません。``ans`` という変数は、最後に評価された式の値（それが表示されたか否かに関わらず）に束縛されています。``ans`` はインタラクティブ・セッションでのみ利用可能で、他の方法でJuliaのコードが実行された際には使うことができません。

To evaluate expressions written in a source file ``file.jl``, write
``include("file.jl")``.

``file.jl`` というソースファイルに書かれた式を評価するには、 ``include("file.jl")`` と書きます。

To run code in a file non-interactively, you can give it as the first
argument to the julia command::

ファイルに書かれたコードをインタラクティブではない方法で実行するには、以下のようにファイル名をjuliaコマンドの第1引数とします::

    $ julia script.jl arg1 arg2...


As the example implies, the following command-line arguments to julia
are taken as command-line arguments to the program ``script.jl``, passed
in the global constant ``ARGS``. ``ARGS`` is also set when script code
is given using the ``-e`` option on the command line (see the ``julia``
help output below). For example, to just print the arguments given to a
script, you could do this::

上の例が示すように、後ろの引数は ``script.jl`` というプログラムのコマンドライン引数として取られ、 グローバル定数 ``ARGS`` に渡されます。コマンドラインで ``-e`` オプションが設定された際にも ``ARGS`` は設定されます（下の ``julia`` ヘルプ出力を参照してください）。例えば以下のようにすることで、スクリプトに渡された引数を単純に出力することができます::

    $ julia -e 'for x in ARGS; println(x); end' foo bar
    foo
    bar

Or you could put that code into a script and run it::

もしくは、上のコードをスクリプトに書いて実行することもできます::

    $ echo 'for x in ARGS; println(x); end' > script.jl
    $ julia script.jl foo bar
    foo
    bar

Julia can be started in parallel mode with either the ``-p`` or the
``--machinefile`` options. ``-p n`` will launch an additional ``n`` worker
processes, while ``--machinefile file`` will launch a worker for each line in
file ``file``. The machines defined in ``file`` must be accessible via a
passwordless ``ssh`` login, with Julia installed at the same location as the
current host. Each machine definition takes the form
``[count*][user@]host[:port] [bind_addr[:port]]`` . ``user`` defaults to current user,
``port`` to the standard ssh port. ``count`` is the number of workers to spawn
on the node, and defaults to 1. The optional ``bind-to bind_addr[:port]``
specifies the ip-address and port that other workers should use to
connect to this worker.

``-p`` もしくは ``--machinefile`` オプションを設定することで、Juliaを並列モードで開始することが出来ます。``-p n`` でn個のワーカーを追加で起動することができ、 ``--machinefile file`` では ``file`` ファイルの各行ごとにワーカーが起動されます。``file`` で定義されたマシンは、パスワード無しで ``ssh`` アクセスでき、現在のホストと同じ場所にJuliaがインストールされている必要があります。各マシンの定義は ``[count*][user@]host[:port] [bind_addr[:port]]`` という形式で書かれます。デフォルトでは、``user`` は現在のユーザー、 ``port`` は標準のsshポートになります。``count`` はノードで作成されるワーカーの数で、デフォルトは1です。``bind-to bind_addr[:port]`` オプションを指定すると、他のワーカーがそのワーカーと接続する際に使うIPアドレスとポートを設定することもできます。

If you have code that you want executed whenever julia is run, you can
put it in ``~/.juliarc.jl``:

Juliaを起動する際に必ず実行されるコードは ``~/.juliarc.jl`` に書きます:

.. raw:: latex

    \begin{CJK*}{UTF8}{mj}

::

    $ echo 'println("Greetings! 你好! 안녕하세요?")' > ~/.juliarc.jl
    $ julia
    Greetings! 你好! 안녕하세요?

    ...

.. raw:: latex

    \end{CJK*}

There are various ways to run Julia code and provide options, similar to
those available for the ``perl`` and ``ruby`` programs::

他の言語（``perl`` や ``ruby`` など）のように、Juliaを実行するには様々な方法やオプションがあります::



    julia [options] [program] [args...]
     -v, --version             Display version information
     -h, --help                Print this message
     -q, --quiet               Quiet startup without banner
     -H, --home <dir>          Set location of julia executable

     -e, --eval <expr>         Evaluate <expr>
     -E, --print <expr>        Evaluate and show <expr>
     -P, --post-boot <expr>    Evaluate <expr>, but don't disable interactive mode
     -L, --load <file>         Load <file> immediately on all processors
     -J, --sysimage <file>     Start up with the given system image file
     -C, --cpu-target <target> Limit usage of cpu features up to <target>

     -p, --procs {N|auto}      Integer value N launches N additional local worker processes
                               'auto' launches as many workers as the number of local cores
     --machinefile <file>      Run processes on hosts listed in <file>

     -i                        Force isinteractive() to be true
     --color={yes|no}          Enable or disable color text

     --history-file={yes|no}   Load or save history
     --no-history-file         Don't load history file (deprecated, use --history-file=no)
     --startup-file={yes|no}   Load ~/.juliarc.jl
     -f, --no-startup          Don't load ~/.juliarc   (deprecated, use --startup-file=no)
     -F                        Load ~/.juliarc         (deprecated, use --startup-file=yes)

     --compile={yes|no|all}    Enable or disable compiler, or request exhaustive compilation

     --code-coverage={none|user|all}, --code-coverage
                              Count executions of source lines (omitting setting is equivalent to 'user')

    --track-allocation={none|user|all}, --track-allocation
                              Count bytes allocated by each source line

    -O, --optimize
                              Run time-intensive code optimizations
    --check-bounds={yes|no}   Emit bounds checks always or never (ignoring declarations)
    --dump-bitcode={yes|no}   Dump bitcode for the system image (used with --build)
    --depwarn={yes|no}        Enable or disable syntax and method deprecation warnings
    --inline={yes|no}         Control whether inlining is permitted (overrides functions declared as @inline)
    --math-mode={ieee|user}   Always use IEEE semantics for math (ignoring declarations),
                              or adhere to declarations in source code

Resources
---------

関連資料
------

In addition to this manual, there are various other resources that may
help new users get started with Julia:

新しいユーザーがJuliaを始めるにあたり、このマニュアルの他にも以下のリソースが参考になるでしょう。

英語
^^^^

 - `Julia and IJulia cheatsheet <http://math.mit.edu/~stevenj/Julia-cheatsheet.pdf>`_
 - `Learn Julia in a few minutes <http://learnxinyminutes.com/docs/julia/>`_
 - `Tutorial for Homer Reid's numerical analysis class <http://homerreid.dyndns.org/teaching/18.330/JuliaProgramming.shtml>`_
 - `An introductory presentation <https://raw.githubusercontent.com/ViralBShah/julia-presentations/master/Fifth-Elephant-2013/Fifth-Elephant-2013.pdf>`_
 - `Videos from the Julia tutorial at MIT <http://julialang.org/blog/2013/03/julia-tutorial-MIT/>`_
 - `Forio Julia Tutorials <http://forio.com/labs/julia-studio/tutorials/>`_


日本語
^^^^^

  - `Julia高速チュートリアル - bicycle1885 <https://github.com/bicycle1885/Julia-Tutorial>`_
  - `Julia Advent Calendar 2014 - Qiita <http://qiita.com/advent-calendar/2014/julialang>`_
  - `Julia Advent Calendar 2015 - Qiita <http://qiita.com/advent-calendar/2015/julialang>`_
  - `JuliaTokyo 発表資料 <http://juliatokyo.connpass.com/>`_
  - `今Juliaを入門するのに良いチュートリアルは？ · Issue #3 · JuliaTokyo/julia-wakalang <https://github.com/JuliaTokyo/julia-wakalang/issues/3>`_
  - `JuliaTokyo/julia-wakalang <https://github.com/JuliaTokyo/julia-wakalang>`_ [#julia-wakalang]_


.. [#julia-wakalang] Juliaに関する質問を日本語で出来る場所。`チャットルーム（Slack） <https://julia-tokyo-inviter.herokuapp.com/>`_ もあります。
