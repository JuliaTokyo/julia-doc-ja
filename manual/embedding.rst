.. _man-embedding:

.. highlight:: c

**************************
 Embedding Julia
**************************

As we have seen (:ref:`man-calling-c-and-fortran-code`) Julia has a simple and efficient way to call functions written in C. But there are situations where the opposite is needed: calling Julia function from C code. This can be used to integrate Julia code into a larger C/C++ project, without the need to rewrite everything in C/C++. Julia has a C API to make this possible. As almost all programming languages have some way to call C functions, the Julia C API can also be used to build further language bridges (e.g. calling Julia from Python or C#).

:ref:`man-calling-c-and-fortran-code` で見たように、JuliaはC言語で記述された関数を容易に呼び出すことができました。また、C言語で実装されたコードからJuliaの関数を呼び出すような反対の状況も存在します。このような場合、Juliaの実装を全てC/C++で書きなおすのではなく、Juliaの実装をC/C++の実装にインテグレートするべきでしょう。JuliaにはC APIが実装されており、C/C++からJuliaの関数などを呼び出すことが可能です。ほとんどのプログラミング言語においてC言語で実装された関数を呼び出す何らかの方法が存在するように、JuliaのC APIは、様々なプログラミング言語との橋渡しをしています（例えば、PythonやC#からJuliaを呼び出すなど）。

High-Level Embedding
=====================

高水準の組み込み
=====================

We start with a simple C program that initializes Julia and calls some Julia code

Juliaを初期化・実行させる簡単なC言語のプログラムから始めてみましょう。
::

  #include <julia.h>

  int main(int argc, char *argv[])
  {
      /* required: setup the julia context */
      /* 必須：Juliaのコンテキスト準備 */
      jl_init(NULL);

      /* run julia commands */
      /* Juliaのコマンドを実行 */
      jl_eval_string("print(sqrt(2.0))");

      /* strongly recommended: notify julia that the
           program is about to terminate. this allows
           julia time to cleanup pending write requests
           and run all finalizers
      */
      /* 実装すべき：プログラムが終了するということをJuliaに通知する。
         この実装は、Juliaに終了処理をするための時間を与える。
      */
      jl_atexit_hook();
      return 0;
  }

In order to build this program you have to put the path to the Julia header into the include path and link against ``libjulia``. For instance, when Julia is installed to ``$JULIA_DIR``, one can compile the above test program ``test.c`` with gcc using

自身のプログラムをビルドするために、Juliaヘッダーに自身のプログラムのインクルードパスを設定するとともに、 ``libjulia`` をリンクする。例えば、 ``$JULIA_DIR`` にJuliaをインストールされている場合、上記のテストプログラムはGCCでコンパイルすることが可能です。
::

    gcc -o test -I$JULIA_DIR/include/julia -L$JULIA_DIR/usr/lib -ljulia test.c

Alternatively, look at the ``embedding.c`` program in the julia source tree in the ``examples/`` folder. The file ``ui/repl.c`` program is another simple example of how to set ``jl_compileropts`` options while linking against libjulia.

Juliaのソースツリーの ``examples/`` フォルダーにある ``embedding.c`` プログラムも同様にコンパイルすることができます。 ``ui/repl.c`` プログラムのファイルは、 libjuliaリンク時の ``jl_compileropts`` オプションに関する設定方法のもう一つの簡単な例です。

The first thing that has to be done before calling any other Julia C function is to initialize Julia. This is done by calling ``jl_init``, which takes as argument a C string (``const char*``) to the location where Julia is installed. When the argument is ``NULL``, Julia tries to determine the install location automatically.

C言語で実装された関数を呼び出す前に一番目にすべきことは、Juliaを初期化することです。初期化は、 ``jl_init`` を呼び出すだけです。初期化関数は、Juliaがインストールされたパスを引数のC文字列 (``const char*``) を引数とします。引数が ``NULL`` だった場合、Juliaはイントール先を自動的に特定しようとします。

The second statement in the test program evaluates a Julia statement using a call to ``jl_eval_string``.

テストプログラムの2番目の命令は、 ``jl_eval_string`` を呼び出し、Juliaの命令を評価しています。

Before the program terminates, it is strongly recommended to call ``jl_atexit_hook``.  The above example program calls this before returning from ``main``.

プログラムを終了する前に、 ``jl_atexit_hook`` を呼び出すことを強くおすすめします。上記のプログラムでは、 ``main`` 関数を終了する前にその関数を呼び出しています。

Using julia-config to automatically determine build parameters
--------------------------------------------------------------

ビルドパラメータを自動的生成してくれるjulia-configの使い方
--------------------------------------------------------------

The script *julia-config.jl* was created to aid in determining what build parameters are required by a program that uses embedded Julia.  This script uses the
build parameters and system configuration of the particular Julia distribution it is invoked by to export the necessary compiler flags for an embedding program to
interact with that distribution.  This script is located in the Julia shared data directory.

*julia-config.jl* というスクリプトは、組み込みJuliaのためのビルドパラメータ作成を補助するためのものです。ビルドパラメータや、特定のJuliaディストリビューションに適したコンパイル用フラグを作成するために使います。スクリプトは、Juliaの共有データディレクトリに配置されています。

Example
.......

例
.......

Below is essentially the same as above with one small change; the argument to ``jl_init`` is
now **JULIA_INIT_DIR** which is defined by *julia-config.jl*.

下記のプログラムは、前述のプログラムの ``jl_init`` の引数を  *julia-config.jl* で作成した **JULIA_INIT_DIR** に変更したものです。
::

  #include <julia.h>

  int main(int argc, char *argv[])
  {
     jl_init(JULIA_INIT_DIR);
     (void)jl_eval_string("println(sqrt(2.0))");
     jl_atexit_hook();
     return 0;
  }

On the command line
...................

コマンドラインでの使い方
........................

A simple use of this script is from the command line.  Assuming that *julia-config.jl* is located
in */usr/local/julia/share/julia*, it can be invoked on the command line directly and takes any
combination of 3 flags

*julia-config.jl* を簡単に使う方法として、コマンドラインがあります。 *julia-config.jl* は、 */usr/local/julia/share/julia* に配置されていると思います。コマンドライン上で直接呼び出すことができ、3つのフラグの組み合わせを設定します。
::

    /usr/local/julia/share/julia/julia-config.jl
    Usage: julia-config [--cflags|--ldflags|--ldlibs]

If the above example source is saved in the file *embed_exmaple.c*, then the following command will compile it into a running program on Linux and Windows (MSYS2 environment),
or if on OS/X, then substitute clang for gcc.

もし、前述のC言語のサンプルソースが *embed_exmaple.c* として保存されているのであれば、以下のコマンドにより、LinuxやWindows(MSYS2環境)において実行形式にコンパイルしてくれるでしょう。
::

    /usr/local/julia/share/julia/julia-config.jl --cflags --ldflags --ldlibs | xargs gcc embed_example.c

Use in Makefiles
................

メイクファイルでの使い方
..........................

But in general, embedding projects will be more complicated than the above, and so the following allows general makefile support as well -- assuming GNU make because
of the use of the **shell** macro expansions.  Additionally, though many times *julia-config.jl* may be found in the directory */usr/local*, this is not necessarily the case,
but Julia can be used to locate *julia-config.jl* too, and the makefile can be used to take advantage of that.  The above example is extended to use a Makefile

一般的に、組み込みプロジェクトは、上述したものよりも複雑なものです。そのため、下記のように一般的なメイクファイルの書き方をすることも可能です。ここでは、 **shell** マクロ拡張機能を使うため、GNUのmakeが利用可能であるとします。 *julia-config.jl* を、 */usr/local* に配置しなければいけないと思うかもしれませんが、その必要はありません。Juliaは、 *julia-config.jl* を探すことも可能だからです。メイクファイルは、それらを利用することが可能です。
::

    JL_SHARE = $(shell julia -e 'print(joinpath(JULIA_HOME,Base.DATAROOTDIR,"julia"))')
    CFLAGS   += $(shell $(JL_SHARE)/julia-config.jl --cflags)
    CXXFLAGS += $(shell $(JL_SHARE)/julia-config.jl --cflags)
    LDFLAGS  += $(shell $(JL_SHARE)/julia-config.jl --ldflags)
    LDLIBS   += $(shell $(JL_SHARE)/julia-config.jl --ldlibs)

    all: embed_example

Now the build command is simply **make**.

単に、 **make** というコマンドを実行するだけです。

Converting Types
========================

型の変換
========================

Real applications will not just need to execute expressions, but also return their values to the host program. ``jl_eval_string`` returns a ``jl_value_t*``, which is a pointer to a heap-allocated Julia object. Storing simple data types like ``Float64`` in this way is called ``boxing``, and extracting the stored primitive data is called ``unboxing``. Our improved sample program that calculates the square root of 2 in Julia and reads back the result in C looks as follows

実際のアプリケーションでは、式を実行するだけでなく、ホストプログラムに値を返します。 ``jl_eval_string`` は ``jl_value_t*`` を返します。Juliaオブジェクトによって、ヒープメモリ上に確保されたメモリへのポインターです。 ``Float64`` のようなシンプルなデータを記憶することを ``boxing`` と呼びます。記憶された基本型データを取り出すことを、 ``unboxing`` と呼びます。2の平方根を求め、その結果をCで読み出す改良したサンプルプログラムは、以下の通りです。

::

    jl_value_t *ret = jl_eval_string("sqrt(2.0)");

    if (jl_is_float64(ret)) {
        double ret_unboxed = jl_unbox_float64(ret);
        printf("sqrt(2.0) in C: %e \n", ret_unboxed);
    }

In order to check whether ``ret`` is of a specific Julia type, we can use the ``jl_is_...`` functions. By typing ``typeof(sqrt(2.0))`` into the Julia shell we can see that the return type is ``Float64`` (``double`` in C). To convert the boxed Julia value into a C double the ``jl_unbox_float64`` function is used in the above code snippet.

``ret`` がJuliaの型かどうかを確認するために、 ``jl_is_...`` 関数を使っています。Juliaシェルに ``typeof(sqrt(2.0))`` と実装されているので、その戻り値の型は ``Float64`` (C言語の ``double`` )ですね。ボクシングされたJuliaの値をC言語のdouble型に変換するために、 ``jl_unbox_float64`` 関数を使っています。

Corresponding ``jl_box_...`` functions are used to convert the other way

``jl_box_...`` という関数は、ボクシングするための関数です。
::

    jl_value_t *a = jl_box_float64(3.0);
    jl_value_t *b = jl_box_float32(3.0f);
    jl_value_t *c = jl_box_int32(3);

As we will see next, boxing is required to call Julia functions with specific arguments.

次で紹介しますが、 ボクシングは特定の引数付きでJulia関数を呼び出すことが必須です。

Calling Julia Functions
========================

Julia関数を呼び出す
========================

While ``jl_eval_string`` allows C to obtain the result of a Julia expression, it does not allow passing arguments computed in C to Julia. For this you will need to invoke Julia functions directly, using ``jl_call``

C言語側は、 ``jl_eval_string`` を通してJuliaの式から得られる結果が受け取れる一方で、C言語側での演算結果をJuliaの関数に引数として渡すことは許されません。そのため、 ``jl_call`` を使ってJulia関数を呼び出す必要があります。
::

    jl_function_t *func = jl_get_function(jl_base_module, "sqrt");
    jl_value_t *argument = jl_box_float64(2.0);
    jl_value_t *ret = jl_call1(func, argument);

In the first step, a handle to the Julia function ``sqrt`` is retrieved by calling ``jl_get_function``. The first argument passed to ``jl_get_function`` is a pointer to the ``Base`` module in which ``sqrt`` is defined. Then, the double value is boxed using ``jl_box_float64``. Finally, in the last step, the function is called using ``jl_call1``. ``jl_call0``, ``jl_call2``, and ``jl_call3`` functions also exist, to conveniently handle different numbers of arguments. To pass more arguments, use ``jl_call``

最初に、 ``jl_get_function`` を使って、Juliaの関数の ``sqrt`` の結果を得ます。 ``jl_get_function`` の第一引数は、 ``sqrt`` が定義されている ``Base`` モジュールへのポインタです。次に、 ``jl_box_float64`` を使ってdouble型の値をボクシングします。最後に、 ``jl_call1`` を使って、Juliaの関関数を呼び出します。 ``jl_call0``, ``jl_call2``, および ``jl_call3`` などの関数が存在します。これら関数により、異なる数の引数を容易に取り扱うことができます。多くの引数を渡したい場合は、``jl_call`` を使ってください。
::

    jl_value_t *jl_call(jl_function_t *f, jl_value_t **args, int32_t nargs)

Its second argument ``args`` is an array of ``jl_value_t*`` arguments and ``nargs`` is the number of arguments.

2番目の引数 ``args`` は、 ``jl_value_t*`` の配列です。 ``nargs`` は、引数の数です。

Memory Management
========================

メモリー管理
========================

As we have seen, Julia objects are represented in C as pointers. This raises the question of who is responsible for freeing these objects.

これまでのように、JuliaのオブジェクトはC言語のポインターとして表現されます。それらのオブジェクトを解放するのはだれの責任なのでしょうか？

Typically, Julia objects are freed by a garbage collector (GC), but the GC does not automatically know that we are holding a reference to a Julia value from C. This means the GC can free objects out from under you, rendering pointers invalid.

Juliaのオブジェクトは、ガベージコレクター (GC)によって解放されます。しかし、C言語側からJulia変数への参照を保持しているかどうかに関して、GCは知るすべがありません。GCは、ポインタを無効化することにより、オブジェクトを開放することができるのです。

The GC can only run when Julia objects are allocated. Calls like ``jl_box_float64`` perform allocation, and allocation might also happen at any point in running Julia code. However, it is generally safe to use pointers in between ``jl_...`` calls. But in order to make sure that values can survive ``jl_...`` calls, we have to tell Julia that we hold a reference to a Julia value. This can be done using the ``JL_GC_PUSH`` macros

GCは、Juliaオブジェクトが確保された場合のみ動作します。 ``jl_box_float64`` を呼び出してメモリを確保するように、メモリの確保は、Juliaのコードのどこでも発生するものです。一般的には、 ``jl_...`` を呼び出し合うことによってポインターを安全に取り扱うことができます。しかし、 ``jl_...`` を呼び出した後に、得られた値を無効化しないために、Juliaにその値への参照を保持するように命令する必要があります。この問題は、 ``JL_GC_PUSH`` マクロで解決することができます。

::

    jl_value_t *ret = jl_eval_string("sqrt(2.0)");
    JL_GC_PUSH1(&ret);
    // Do something with ret
    JL_GC_POP();

The ``JL_GC_POP`` call releases the references established by the previous ``JL_GC_PUSH``. Note that ``JL_GC_PUSH``  is working on the stack, so it must be exactly paired with a ``JL_GC_POP`` before the stack frame is destroyed.

``JL_GC_POP`` は、 ``JL_GC_PUSH`` によって設定された参照を無効にします。 ``JL_GC_PUSH``  はスタックのように振る舞うことに注意してください。ですので、スタックフレームが解放される前に、 ``JL_GC_POP`` と対で使うようにしてください。

Several Julia values can be pushed at once using the ``JL_GC_PUSH2`` , ``JL_GC_PUSH3`` , and ``JL_GC_PUSH4`` macros. To push an array of Julia values one can use the  ``JL_GC_PUSHARGS`` macro, which can be used as follows

``JL_GC_PUSH2`` , ``JL_GC_PUSH3`` , および ``JL_GC_PUSH4`` マクロを使うと、複数のJuliaのオブジェクト(値)を同時にプッシュすることが可能です。Juliaのオブジェクト(値)の配列をプッシュするために、``JL_GC_PUSHARGS`` マクロが用意されています。使い方は、以下の通りです。
::

    jl_value_t **args;
    JL_GC_PUSHARGS(args, 2); // args can now hold 2 `jl_value_t*` objects
    args[0] = some_value;
    args[1] = some_other_value;
    // Do something with args (e.g. call jl_... functions)
    JL_GC_POP();

The garbage collector also operates under the assumption that it is aware of every old-generation object pointing to a young-generation one. Any time a pointer is updated breaking that assumption, it must be signaled to the collector with the ``gc_wb`` (write barrier) function like so

全ての旧世代のオブジェクトから新世代のオブジェクトへのポインターを把握しているという前提のもとで、ガベージコレクターは動作します。ポインターに対して前提を壊すような更新がなされると、 ``gc_wb`` (書き込み防止) の情報とともにその更新が、ガベージコレクターに通知されます。
::

    jl_value_t *parent = some_old_value, *child = some_young_value;
    ((some_specific_type*)parent)->field = child;
    gc_wb(parent, child);

It is in general impossible to predict which values will be old at runtime, so the write barrier must be inserted after all explicit stores. One notable exception is if the ``parent`` object was just allocated and garbage collection was not run since then. Remember that most ``jl_...`` functions can sometimes invoke garbage collection.

どのオブジェクト(値)が、実行時に旧世代になるかを予測することは可能です。書き込み防止は、明示的に全てのオブジェクトがメモリ上に保持された後に設定されます。一つの例外は、 ``parent`` オブジェクトのメモリがちょうど確保された後に、ガベージコレクションが動作しないときです。

The write barrier is also necessary for arrays of pointers when updating their data directly. For example

書き込み防止は、ポインターの配列が直接更新されるような場合にも考慮する必要があります。例えば、
::

    jl_array_t *some_array = ...; // e.g. a Vector{Any}
    void **data = (void**)jl_array_data(some_array);
    jl_value_t *some_value = ...;
    data[0] = some_value;
    gc_wb(some_array, some_value);


Manipulating the Garbage Collector
---------------------------------------------------

ガベージコレクターの操作
---------------------------------------------------

There are some functions to control the GC. In normal use cases, these should not be necessary.

GCを設定するための関数が存在します。通常、使うことはないでしょう。

========================= ==============================================================================
``void jl_gc_collect()``   Force a GC run

                           GCを強制的に実行します
``void jl_gc_disable()``   Disable the GC

                           GCを無効にします
``void jl_gc_enable()``    Enable the GC

                           GC有効にします
========================= ==============================================================================

Working with Arrays
========================

配列を伴った場合の振る舞い
=============================

Julia and C can share array data without copying. The next example will show how this works.

JuliaとC言語は、コピーすることなくデータの配列を共有することが可能です。この振る舞いについて、次に例を示します。

Julia arrays are represented in C by the datatype ``jl_array_t*``. Basically, ``jl_array_t`` is a struct that contains:

Juliaでは、常に ``jl_array_t*``のデータ型を使って、Cでの表現がなされます。基本的に、``jl_array_t`` は、以下の情報を含む構造体です。:

- Information about the datatype
- データタイプ
- A pointer to the data block
- データブロックへのポインター
- Information about the sizes of the array
- 配列のサイズ

To keep things simple, we start with a 1D array. Creating an array containing Float64 elements of length 10 is done by

簡単のため、一次元配列を考えます。10個のFloat64型の要素を持つ配列は、以下のようにして作成されます。
::

    jl_value_t* array_type = jl_apply_array_type(jl_float64_type, 1);
    jl_array_t* x          = jl_alloc_array_1d(array_type, 10);

Alternatively, if you have already allocated the array you can generate a thin wrapper around its data

すでにメモリを確保している場合、簡単なラッパーが実装できます。
::

    double *existingArray = (double*)malloc(sizeof(double)*10);
    jl_array_t *x = jl_ptr_to_array_1d(array_type, existingArray, 10, 0);

The last argument is a boolean indicating whether Julia should take ownership of the data. If this argument is non-zero, the GC will call ``free`` on the data pointer when the array is no longer referenced.

最後の引数は、ブール値です。Juliaがデータのオーナーかどうかを示すものです。この引数が0でない場合、配列への参照がなくなった時点で、GCが ``free`` を呼び出します。

In order to access the data of x, we can use ``jl_array_data``

xのデータへアクセスするために、 ``jl_array_data`` を使います。
::

    double *xData = (double*)jl_array_data(x);

Now we can fill the array

配列にオブジェクトを代入します。
::

    for(size_t i=0; i<jl_array_len(x); i++)
        xData[i] = i;

Now let us call a Julia function that performs an in-place operation on ``x``

``x`` の要素を逆に並べ替えるJuliaの関数が呼び出されるように設定してみましょう。
::

    jl_function_t *func  = jl_get_function(jl_base_module, "reverse!");
    jl_call1(func, (jl_value_t*)x);

By printing the array, one can verify that the elements of ``x`` are now reversed.

配列を出力してみると、 ``x`` の要素が逆に並べ替えられていることを確認することができます。

Accessing Returned Arrays
---------------------------------

返却された配列へのアクセス
---------------------------------

If a Julia function returns an array, the return value of ``jl_eval_string`` and ``jl_call`` can be cast to a ``jl_array_t*``

Juliaの関数が配列を返却する場合、 ``jl_eval_string`` と ``jl_call`` の場合は、 ``jl_array_t*`` にキャストすることができます。
::

    jl_function_t *func  = jl_get_function(jl_base_module, "reverse");
    jl_array_t *y = (jl_array_t*)jl_call1(func, (jl_value_t*)x);

Now the content of ``y`` can be accessed as before using ``jl_array_data``.

``y`` は、 ``jl_array_data`` を呼び出した時のように、アクセスすることができます。

As always, be sure to keep a reference to the array while it is in use.

配列を使っている間は、配列への参照を必ず保持すべきです。

Multidimensional Arrays
---------------------------------

多次元配列
---------------------------------

Julia's multidimensional arrays are stored in memory in column-major order. Here is some code that creates a 2D array and accesses its properties

Juliaの多次元配列は、列順序でメモリー上に確保されます。2次元配列を作成し、そのプロパティにアクセスするコードを示します。
::

    // Create 2D array of float64 type
    // float64型の2次元配列を作成
    jl_value_t *array_type = jl_apply_array_type(jl_float64_type, 2);
    jl_array_t *x  = jl_alloc_array_2d(array_type, 10, 5);

    // Get array pointer
    // 配列のポインターを取得
    double *p = (double*)jl_array_data(x);
    // Get number of dimensions
    // 次元数を取得
    int ndims = jl_array_ndims(x);
    // Get the size of the i-th dim
    // i-th次元の配列のサイズを取得する
    size_t size0 = jl_array_dim(x,0);
    size_t size1 = jl_array_dim(x,1);

    // Fill array with data
    // 配列をデータで埋める
    for(size_t i=0; i<size1; i++)
        for(size_t j=0; j<size0; j++)
            p[j + size0*i] = i + j;

Notice that while Julia arrays use 1-based indexing, the C API uses 0-based indexing (for example in calling ``jl_array_dim``) in order to read as idiomatic C code.

Juliaの配列のインデックスは1から始まるのに対して、C 言語のAPIはインデックスが0から始まることに注意してください (例えば、 ``jl_array_dim`` を呼び出したときです) 。

Exceptions
==========

例外
==========

Julia code can throw exceptions. For example, consider

Juliaは、例外を送出することができます。例えば、下記の実装を考えてみてください。　
::

      jl_eval_string("this_function_does_not_exist()");

This call will appear to do nothing. However, it is possible to check whether an exception was thrown

この関数呼び出しは、目に見えて何かが起きるわけではありません。しかし、例外が送出されるかどうかを確認することは可能です。
::

    if (jl_exception_occurred())
        printf("%s \n", jl_typeof_str(jl_exception_occurred()));

If you are using the Julia C API from a language that supports exceptions (e.g. Python, C#, C++), it makes sense to wrap each call into libjulia with a function that checks whether an exception was thrown, and then rethrows the exception in the host language.

JuliaのC APIをサポートしている言語(例えば、Python, C#, C++)から呼び出す場合、例外を送出するかどうかを確認する関数とともにlibjuliaを使ってラッパーを実装することに意味があります。その後、Juliaを呼び出している言語に対して例外を再送出してください。


Throwing Julia Exceptions
-------------------------

Juliaの例外の送出
-------------------------

When writing Julia callable functions, it might be necessary to validate arguments and throw exceptions to indicate errors. A typical type check looks like

Juliaの関数では、引数を評価し、エラーを通知するために例外を送出するように実装しましょう。型の評価は、以下のようにして行います。
::

    if (!jl_is_float64(val)) {
        jl_type_error(function_name, (jl_value_t*)jl_float64_type, val);
    }

General exceptions can be raised using the funtions

一般的な例外は、関数を使って送出します。
::

    void jl_error(const char *str);
    void jl_errorf(const char *fmt, ...);

``jl_error`` takes a C string, and ``jl_errorf`` is called like ``printf`` 

``jl_error`` はC文字列を受け取りますし、 ``jl_errorf`` は ``printf`` のように呼び出します。
::

    jl_errorf("argument x = %d is too large", x);

where in this example ``x`` is assumed to be an integer.

この例では、 ``x`` が整数型だと仮定しています。

