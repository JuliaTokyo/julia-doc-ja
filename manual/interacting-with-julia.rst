.. _man-interacting-with-julia:

************************
 Interacting With Julia
************************

Julia comes with a full-featured interactive command-line REPL (read-eval-print loop) built into the ``julia`` executable.  In addition to allowing quick and easy evaluation of Julia statements, it has a searchable history, tab-completion, many helpful keybindings, and dedicated help and shell modes.  The REPL can be started by simply calling julia with no arguments or double-clicking on the executable

Juliaは、すべての機能が搭載された対話型のコマンドラインREPL (read-eval-print loop) が実行形式 ``julia`` に搭載されています。 実行形式のJuliaは、コマンドを素早くかつ簡単に評価するとともに、コマンドの履歴検索、タブ補完、多くのキーバインド、詳細なヘルプ、およびシェルモードを提供しています。REPLは、単にjuliaとコマンドを実行するか、ダブルクリックをすることによって起動されます。
::

    $ julia
                   _
       _       _ _(_)_     |  A fresh approach to technical computing
      (_)     | (_) (_)    |  Documentation: http://docs.julialang.org
       _ _   _| |_  __ _   |  Type "help()" to list help topics
      | | | | | | |/ _` |  |
      | | |_| | | | (_| |  |  Version 0.3.0-prerelease+2834 (2014-04-30 03:13 UTC)
     _/ |\__'_|_|_|\__'_|  |  Commit 64f437b (0 days old master)
    |__/                   |  x86_64-apple-darwin13.1.0

    julia>

To exit the interactive session, type ``^D`` — the control key together with the ``d`` key on a blank line — or type ``quit()`` followed by the return or enter key. The REPL greets you with a banner and a ``julia>`` prompt.

対話形式のセッションを終了したい場合は、``^D`` - CTRLキーと ``d`` を同時にタイプするか、 ``quit()`` をタイプしてください。REPLは、バナーが表示された後、 ``julia>`` プロンプトが表示されます。

The different prompt modes
--------------------------

異なるプロンプトモード
--------------------------

The Julian mode
~~~~~~~~~~~~~~~
Julianモード
~~~~~~~~~~~~~~~

The REPL has four main modes of operation.  The first and most common is the Julian prompt.  It is the default mode of operation; each new line initially starts with ``julia>``.  It is here that you can enter Julia expressions.  Hitting return or enter after a complete expression has been entered will evaluate the entry and show the result of the last expression.

REPLは、4つの主な動作モードがあります。1つ目は、最も一般的なJulianプロンプトモードです。これは、デフォルトモードです。各行は、``julia>`` で始まります。このプロンプトの後に、Julia式を入力します。式の入力後にEnterキーを押すと、入力された式の評価を行い、最後に入力・評価された式の結果を表示します。

.. doctest::

    julia> string(1 + 2)
    "3"

There are a number useful features unique to interactive work. In addition to showing the result, the REPL also binds the result to the variable ``ans``.  A trailing semicolon on the line can be used as a flag to suppress showing the result.

対話形式特有のいくつかの有用な機能を提供しています。結果を表示するとともに、その結果は変数 ``ans`` にセットされます。行をセミコロンで終了させると、結果を同時に表示させないようにすることができます。

.. doctest::

    julia> string(3 * 4);

    julia> ans
    "12"

Help mode
~~~~~~~~~

ヘルプモート
~~~~~~~~~~~~~~~~~~~

When the cursor is at the beginning of the line, the prompt can be changed to a help mode by typing ``?``.  Julia will attempt to print help or documentation for anything entered in help mode

カーソルが行頭にあるときに、 ``?`` をタイプするとプロンプトがヘルプモードに変わります。juliaはヘルプモードの時に入力されたものに関するヘルプやドキュメントを表示します。
::

    julia> ? # upon typing ?, the prompt changes (in place) to: help>

    help> string
    Base.string(xs...)

       Create a string from any values using the "print" function.

In addition to function names, complete function calls may be entered to see which method is called for the given argument(s).  Macros, types and variables can also be queried

関数の名前、引数を伴った関数の呼び出し方を表示します。マクロ、型、変数に関しても確認することができます。
::

    help> string(1)
    string(x::Union(Int16,Int128,Int8,Int32,Int64)) at string.jl:1553

    help> @printf
    Base.@printf([io::IOStream], "%Fmt", args...)

       Print arg(s) using C "printf()" style format specification
       string. Optionally, an IOStream may be passed as the first argument
       to redirect output.

    help> AbstractString
    DataType   : AbstractString
      supertype: Any
      subtypes : {DirectIndexString,GenericString,RepString,RevString{T<:AbstractString},RopeString,SubString{T<:AbstractString},UTF16String,UTF8String}

Help mode can be exited by pressing backspace at the beginning of the line.

ヘルプモードは、行頭で、バックスペースキーを押すことによって終了させることができます。

.. _man-shell-mode:

Shell mode
~~~~~~~~~~

シェルモード
~~~~~~~~~~~~~~~

Just as help mode is useful for quick access to documentation, another common task is to use the system shell to execute system commands.  Just as ``?`` entered help mode when at the beginning of the line, a semicolon (``;``) will enter the shell mode.  And it can be exited by pressing backspace at the beginning of the line.

ヘルプモードのようにドキュメントに簡単にアクセスする方法として、システムシェルを使う方法があります。システムシェルは、システムコマンドを実行するためのものです。ヘルプモードの時に行頭で ``?`` を入力したように、シェルモードでは、セミコロン (``;``) を入力します。シェルモードは、バックスペースキーを押すことによって終了させることができます。

::

    julia> ; # upon typing ;, the prompt changes (in place) to: shell>

    shell> echo hello
    hello

Search modes
~~~~~~~~~~~~

サーチモード
~~~~~~~~~~~~

In all of the above modes, the executed lines get saved to a history file, which can be searched.  To initiate an incremental search through the previous history, type ``^R`` — the control key together with the ``r`` key.  The prompt will change to ``(reverse-i-search)`':``, and as you type the search query will appear in the quotes.  The most recent result that matches the query will dynamically update to the right of the colon as more is typed.  To find an older result using the same query, simply type ``^R`` again.

今までお話した全てモードは、履歴ファイルに実行した行の情報が保存されます。それらの情報は検索が可能です。過去の履歴に対してインクリメンタル検索するためには、``^R`` - CTRLキーと ``r`` を同時にタイプします。プロンプトが、``(reverse-i-search)`':`` に変わります。検索文字を入力すると同時に、引用符の中にその文字が表示されます。文字入力を繰り返すと、入力された検索文字にマッチした最新の入力履歴結果が、コロンの右側に動的に更新されていきます。より古い入力履歴の結果を検索したい場合は、再度 ``^R`` をタイプしてください。

Just as ``^R`` is a reverse search, ``^S`` is a forward search, with the prompt ``(i-search)`':``.  The two may be used in conjunction with each other to move through the previous or next matching results, respectively.

``^R`` は、逆方向検索ですが、``^S`` は、順方向検索になります。プロンプトは、 ``(i-search)`':`` です。それぞれの検索方法は、マッチした履歴の結果の前後を行き来するときに使うとよいでしょう。


Key bindings
------------

キーバインド
------------

The Julia REPL makes great use of key bindings.  Several control-key bindings were already introduced above (``^D`` to exit, ``^R`` and ``^S`` for searching), but there are many more.  In addition to the control-key, there are also meta-key bindings.  These vary more by platform, but most terminals  default to using alt- or option- held down with a key to send the meta-key (or can be configured to do so).

JuliaのREPLは、キーバインドを利用することが可能です。CTRLキーバインディングに関しては、いくつか(終了用の ``^D`` ,検索用の ``^R`` と ``^S`` )紹介してきました。

+-----------------------------+--------------------------------------------------------------+
| **Program control**                                                                        |
|                                                                                            |
| **プログラム・コントロール**                                                               |
+-----------------------------+--------------------------------------------------------------+
| ``^D``                      | Exit (when buffer is empty)                                  |
|                             |                                                              |
|                             | 終了（バッファが空の場合）                                   |
+-----------------------------+--------------------------------------------------------------+
| ``^C``                      | Interrupt or cancel                                          |
|                             |                                                              |
|                             | 中断もしくはキャンセル                                       |
+-----------------------------+--------------------------------------------------------------+
| Return/Enter, ``^J``        | New line, executing if it is complete                        |
|                             |                                                              |
|                             | 改行、入力完了後の実行                                       |
+-----------------------------+--------------------------------------------------------------+
| meta-Return/Enter           | Insert new line without executing it                         |
|                             |                                                              |
|                             | 実行せず新規行追加                                           |
+-----------------------------+--------------------------------------------------------------+
| ``?`` or ``;``              | Enter help or shell mode (when at start of a line)           |
|                             |                                                              |
|                             | ヘルプモードもしくはシェルモード（行頭で入力した場合）       |
+-----------------------------+--------------------------------------------------------------+
| ``^R``, ``^S``              | Incremental history search, described above                  |
|                             |                                                              |
|                             | インクリメンタル履歴検索（既出）                             |
+-----------------------------+--------------------------------------------------------------+
| **Cursor movement**                                                                        |
|                                                                                            |
| **カーソル移動**                                                                           |
+-----------------------------+--------------------------------------------------------------+
| Right arrow, ``^F``         | Move right one character                                     |
|                             |                                                              |
|                             | 1文字分右へ移動                                              |
+-----------------------------+--------------------------------------------------------------+
| Left arrow, ``^B``          | Move left one character                                      |
|                             |                                                              |
|                             | 1文字分左へ移動                                              |
+-----------------------------+--------------------------------------------------------------+
| Home, ``^A``                | Move to beginning of line                                    |
|                             |                                                              |
|                             | 行頭へ移動                                                   |
+-----------------------------+--------------------------------------------------------------+
| End, ``^E``                 | Move to end of line                                          |
|                             |                                                              |
|                             | 行末へ移動                                                   |
+-----------------------------+--------------------------------------------------------------+
| ``^P``                      | Change to the previous or next history entry                 |
|                             |                                                              |
|                             | 前もしくは次の履歴へ移動                                     |
+-----------------------------+--------------------------------------------------------------+
| ``^N``                      | Change to the next history entry                             |
|                             |                                                              |
|                             | 次の履歴へ移動                                               |
+-----------------------------+--------------------------------------------------------------+
| Up arrow                    | Move up one line (or to the previous history entry)          |
|                             |                                                              |
|                             | 上の行へ移動（もしくは前の履歴へ移動）                       |
+-----------------------------+--------------------------------------------------------------+
| Down arrow                  | Move down one line (or to the next history entry)            |
|                             |                                                              |
|                             | 下の行へ移動（もしくは次の履歴へ移動）                       |
+-----------------------------+--------------------------------------------------------------+
| Page-up                     | Change to the previous history entry that matches            |
|                             | the text before the cursor                                   |
|                             |                                                              |
|                             | カーソルの前の文字にマッチした前の履歴へ移動                 |
+-----------------------------+--------------------------------------------------------------+
| Page-down                   | Change to the next history entry that matches the            |
|                             | text before the cursor                                       |
|                             |                                                              |
|                             | カーソルの前の文字にマッチした次の履歴へ移動                 |
+-----------------------------+--------------------------------------------------------------+
| ``meta-F``                  | Move right one word                                          |
|                             |                                                              |
|                             | 右へ1単語分移動                                              |
+-----------------------------+--------------------------------------------------------------+
| ``meta-B``                  | Move left one word                                           |
|                             |                                                              |
|                             | 左へ1単語分移動                                              |
+-----------------------------+--------------------------------------------------------------+
| **Editing**                                                                                |
|                                                                                            |
| **編集中**                                                                                 |
+-----------------------------+--------------------------------------------------------------+
| Backspace, ``^H``           | Delete the previous character                                |
|                             |                                                              |
|                             | 前の1文字を削除                                              |
+-----------------------------+--------------------------------------------------------------+
| Delete, ``^D``              | Forward delete one character (when buffer has text)          |
|                             |                                                              |
|                             | 次の1文字を削除（次の文字があった場合）                      |
+-----------------------------+--------------------------------------------------------------+
| meta-Backspace              | Delete the previous word                                     |
|                             |                                                              |
|                             | 前の1単語を削除                                              |
+-----------------------------+--------------------------------------------------------------+
| ``meta-D``                  | Forward delete the next word                                 |
|                             |                                                              |
|                             | 次の1単語を削除                                              |
+-----------------------------+--------------------------------------------------------------+
| ``^W``                      | Delete previous text up to the nearest whitespace            |
|                             |                                                              |
|                             | 一番近い空白文字まで文字列を前方向に削除                     |
+-----------------------------+--------------------------------------------------------------+
| ``^K``                      | "Kill" to end of line, placing the text in a buffer          |
|                             |                                                              |
|                             | 行末までの全てをカット                                       |
+-----------------------------+--------------------------------------------------------------+
| ``^Y``                      | "Yank" insert the text from the kill buffer                  |
|                             |                                                              |
|                             | バッファから文字列をペースト                                 |
+-----------------------------+--------------------------------------------------------------+
| ``^T``                      | Transpose the characters about the cursor                    |
|                             |                                                              |
|                             | カーソル位置の文字と1つ前の文字を交換する                    |
+-----------------------------+--------------------------------------------------------------+
| Delete, ``^D``              | Forward delete one character (when buffer has text)          |
|                             |                                                              |
|                             | 次方向の1文字削除（次方向に文字が存在する場合）              |
+-----------------------------+--------------------------------------------------------------+

Customizing keybindings
~~~~~~~~~~~~~~~~~~~~~~~

キーバインドのカスタマイズ
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Julia's REPL keybindings may be fully customized to a user's preferences by passing a dictionary to ``REPL.setup_interface()``. The keys of this dictionary may be characters or strings. The key ``'*'`` refers to the default action. Control plus character ``x`` bindings are indicated with ``"^x"``. Meta plus ``x`` can be written ``"\\Mx"``. The values of the custom keymap must be ``nothing`` (indicating that the input should be ignored) or functions that accept the signature ``(PromptState, AbstractREPL, Char)``. For example, to bind the up and down arrow keys to move through history without prefix search, one could put the following code in ``.juliarc.jl``

JuliaのREPLキーバインドは、 ``REPL.setup_interface()`` を使うことによって、ユーザの好み合わせてカスタマイズ可能です。キーバインド辞書のキーは、文字もしくは文字列を指定します。キー ``'*'`` は、デフォルトの振る舞いに従います。CTRLと文字 ``x`` のバインドは、 ``"^x"`` と表記します。メタ文字と ``x`` のバインドは、 ``"\\Mx"`` と書くこととします。カスタマイズされたキーマップの値は、 ``nothing`` （入力は無視するという意味です）であるか、予約済みのもの ``(PromptState, AbstractREPL, Char)`` のいずれかでなければなりません。
::

    import Base: LineEdit, REPL

    const mykeys = Dict{Any,Any}(
      # Up Arrow
      "\e[A" => (s,o...)->(LineEdit.edit_move_up(s) || LineEdit.history_prev(s, LineEdit.mode(s).hist)),
      # Down Arrow
      "\e[B" => (s,o...)->(LineEdit.edit_move_up(s) || LineEdit.history_next(s, LineEdit.mode(s).hist))
    )

    Base.active_repl.interface = REPL.setup_interface(Base.active_repl; extra_repl_keymap = mykeys)

Users should refer to ``base/LineEdit.jl`` to discover the available actions on key input.

キーバインドによる変更可能な振る舞いに関しては、 ``base/LineEdit.jl`` を参照してください。

Tab completion
--------------

タブ補完
--------------

In both the Julian and help modes of the REPL, one can enter the first few characters of a function or type and then press the tab key to get a list all matches

REPLのJulianモードおよびヘルプモードにおいて、最初の数文字を入力した後、タブキーを押すと、入力文字がマッチした文字列の一覧が表示されます。
::

    julia> stri
    stride     strides     string      stringmime  strip

    julia> Stri
    StridedArray    StridedVecOrMat  AbstractString
    StridedMatrix   StridedVector

The tab key can also be used to substitute LaTeX math symbols with their Unicode equivalents,
and get a list of LaTeX matches as well

タブキーは、LaTeX数学記号（Unicode）の代わりに使うことが可能です。また、LaTex記号の一覧も表示することが可能です。
::

    julia> \pi[TAB]
    julia> π
    π = 3.1415926535897...

    julia> e\_1[TAB] = [1,0]
    julia> e₁ = [1,0]
    2-element Array{Int64,1}:
     1
     0

    julia> e\^1[TAB] = [1 0]
    julia> e¹ = [1 0]
    1x2 Array{Int64,2}:
     1  0

    julia> \sqrt[TAB]2     # √ is equivalent to the sqrt() function
    julia> √2
    1.4142135623730951

    julia> \hbar[TAB](h) = h / 2\pi[TAB]
    julia> ħ(h) = h / 2π
    ħ (generic function with 1 method)

    julia> \h[TAB]
    \hat              \heartsuit         \hksearow          \hookleftarrow     \hslash
    \hbar             \hermitconjmatrix  \hkswarow          \hookrightarrow    \hspace

A full list of tab-completions can be found in the :ref:`man-unicode-input` section of the manual.

タブ補完できる一覧に関しては、マニュアルの :ref:`man-unicode-input` セクションを参照してください。
