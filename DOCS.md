# Coconut Documentation

```eval_rst
.. contents::
    :local:
    :depth: 2
```

## Overview

This documentation covers all the features of the [Coconut Programming Language](http://evhub.github.io/coconut/), and is intended as a reference/specification, not a tutorialized introduction. For a full introduction and tutorial of Coconut, see [the tutorial](HELP.html).

Coconut is a variant of [Python](https://www.python.org/) built for **simple, elegant, Pythonic functional programming**. Coconut syntax is a strict superset of Python 3 syntax. Thus, users familiar with Python will already be familiar with most of Coconut.

The Coconut compiler turns Coconut code into Python code. The primary method of accessing the Coconut compiler is through the Coconut command-line utility, which also features an interpreter for real-time compilation. In addition to the command-line utility, Coconut also supports the use of IPython/Jupyter notebooks.

While most of Coconut gets its inspiration simply from trying to make functional programming work in Python, additional inspiration came from [Haskell](https://www.haskell.org/), [CoffeeScript](http://coffeescript.org/), [F#](http://fsharp.org/), and [patterns.py](https://github.com/Suor/patterns).

## Compilation

### Installation

Since Coconut is hosted on the [Python Package Index](https://pypi.python.org/pypi/coconut), it can be installed easily using `pip`. Simply install [Python](https://www.python.org/downloads/), open up a command-line prompt, and enter
```
pip install coconut
```
which will install Coconut and its required dependencies. Coconut also has some optional dependencies, which can be installed by entering
```
pip install coconut[all]
```
which will enable the use of Coconut's `--jobs`, `--watch`, `--jupyter`, and `--mypy` flags. To install the optional dependencies only for a particular flag, simply put the flag name in place of `all`.

Alternatively, if you want to test out Coconut's latest and greatest, enter
```
pip install coconut-develop
```
which will install the most recent working [development build](https://github.com/evhub/coconut/tree/develop) (optional dependency installation is also supported in the same manner as above if you want). For more information on the current development build, check out the [development version of this documentation](DOCS.html). Be warned: `coconut-develop` is likely to be unstable—if you find a bug, please report it by [creating a new issue](https://github.com/evhub/coconut/issues/new).

### Usage

```
coconut [-h] [-v] [-t version] [-i] [-p] [-a] [-l] [-k] [-w] [-r] [-n]
        [-d] [-q] [-s] [--no-tco] [-c code] [-j processes] [-f]
        [--minify] [--jupyter ...] [--mypy ...] [--tutorial]
        [--documentation] [--style name] [--recursion-limit limit]
        [--verbose] [--trace]
        [source] [dest]
```

#### Positional Arguments

```
source              path to the Coconut file/folder to compile
dest                destination directory for compiled files (defaults to
                    the source directory)
```

#### Optional Arguments

```
-h, --help            show this help message and exit
-v, --version         print Coconut and Python version information
-t version, --target version
                    specify target Python version (defaults to universal)
-i, --interact        force the interpreter to start (otherwise starts if no
                    other command is given) (implies --run)
-p, --package         compile source as part of a package (defaults to only
                    if source is a directory)
-a, --standalone      compile source as standalone files (defaults to only
                    if source is a single file)
-l, --line-numbers, --linenumbers
                    add line number comments for ease of debugging
-k, --keep-lines, --keeplines
                    include source code in comments for ease of debugging
-w, --watch           watch a directory and recompile on changes
-r, --run             execute compiled Python
-n, --no-write, --nowrite
                    disable writing compiled Python
-d, --display         print compiled Python
-q, --quiet           suppress all informational output (combine with
                    --display to write runnable code to stdout)
-s, --strict          enforce code cleanliness standards
--no-tco, --notco     disable tail call optimization for ease of debugging
-c code, --code code  run Coconut passed in as a string (can also be piped
                    into stdin)
-j processes, --jobs processes
                    number of additional processes to use (defaults to 0)
                    (pass 'sys' to use machine default)
-f, --force           force overwriting of compiled Python (otherwise only
                    overwrites when source code or compilation parameters
                    change)
--minify              reduce size of compiled Python
--jupyter ..., --ipython ...
                    run Jupyter/IPython with Coconut as the kernel
                    (remaining args passed to Jupyter)
--mypy ...            run MyPy on compiled Python (remaining args passed to
                    MyPy) (implies --package --no-tco)
--tutorial            open the Coconut tutorial in the default web browser
--documentation       open the Coconut documentation in the default web
                    browser
--style name          Pygments syntax highlighting style (or 'none' to
                    disable) (defaults to COCONUT_STYLE environment
                    variable, if it exists, otherwise 'default')
--recursion-limit limit, --recursionlimit limit
                    set maximum recursion depth in compiler (defaults to
                    2000)
--verbose             print verbose debug output
--trace               show verbose parsing data (only available in coconut-
                    develop)
```

### Coconut Scripts

To run a Coconut file as a script, Coconut provides the command `coconut-run` as an alias for `coconut --run --quiet` that also passes all additional command-line arguments to the script being run. `coconut-run` will quietly compile the file if it's been changed or use the existing compiled Python if it hasn't and then run that. `coconut-run` can be used in a Unix shebang to create a Coconut script with the following line:
```bash
#!/usr/bin/env coconut-run
```

### Naming Source Files

Coconut source files should, so the compiler can recognize them, use the extension `.coco` (preferred), `.coc`, or `.coconut`. When Coconut compiles a `.coco` (or `.coc` / `.coconut`) file, it will compile to another file with the same name, except with `.py` instead of `.coco`, which will hold the compiled code. If an extension other than `.py` is desired for the compiled files, such as `.pyde` for [Python Processing](http://py.processing.org/), then that extension can be put before `.coco` in the source file name, and it will be used instead of `.py` for the compiled files. For example, `name.coco` will compile to `name.py`, whereas `name.pyde.coco` will compile to `name.pyde`.

### Compilation Modes

Files compiled by the `coconut` command-line utility will vary based on compilation parameters. If an entire directory of files is compiled (which the compiler will search recursively for any folders containing `.coco`, `.coc`, or `.coconut` files), a `__coconut__.py` file will be created to house necessary functions (package mode), whereas if only a single file is compiled, that information will be stored within a header inside the file (standalone mode). Standalone mode is better for single files because it gets rid of the overhead involved in importing `__coconut__.py`, but package mode is better for large packages because it gets rid of the need to run the same Coconut header code again in every file, since it can just be imported from `__coconut__.py`.

By default, if the `source` argument to the command-line utility is a file, it will perform standalone compilation on it, whereas if it is a directory, it will recursively search for all `.coco` (or `.coc` / `.coconut`) files and perform package compilation on them. Thus, in most cases, the mode chosen by Coconut automatically will be the right one. But if it is very important that no additional files like `__coconut__.py` be created, for example, then the command-line utility can also be forced to use a specific mode with the `--package` (`-p`) and `--standalone` (`-a`) flags.

### Compatible Python Versions

While Coconut syntax is based off of Python 3, Coconut code compiled in universal mode (the default `--target`), and the Coconut compiler, should run on any Python version `>= 2.6` on the `2.x` branch or `>= 3.2` on the `3.x` branch.

_Note: The tested against implementations are [CPython](https://www.python.org/) `2.6, 2.7, 3.2, 3.3, 3.4, 3.5, 3.6` and [PyPy](http://pypy.org/) `2.7, 3.2`._

As part of Coconut's cross-compatibility efforts, Coconut adds in new Python 3 built-ins and overwrites Python 2 built-ins to use the Python 3 versions where possible. Additionally, Coconut also overrides some Python 3 built-ins for optimization purposes. If access to the Python versions is desired, the old built-ins can be retrieved by prefixing them with `py_`.

For standard library compatibility, Coconut automatically maps imports under Python 3 names to imports under Python 2 names. Thus, Coconut will automatically take care of any standard library modules that were renamed from Python 2 to Python 3 if just the Python 3 name is used. For modules or objects that only exist in Python 3, however, Coconut has no way of maintaining compatibility.

Finally, while Coconut will try to compile Python-3-specific syntax to its universal equivalent, the following constructs have no equivalent in Python 2, and require the specification of a target of at least `3` to be used:
- destructuring assignment with `*`s (use Coconut pattern-matching instead),
- the `nonlocal` keyword,
- `exec` used in a context where it must be a function,
- keyword class definition,
- tuples and lists with `*` unpacking or dicts with `**` unpacking (requires `--target 3.5`),
- `@` as matrix multiplication (requires `--target 3.5`),
- `async` and `await` statements (requires `--target 3.5`), and
- formatting strings by prefixing them with `f` (requires `--target 3.6`).

### Allowable Targets

If the version of Python that the compiled code will be running on is known ahead of time, a target should be specified with `--target`. The given target will only affect the compiled code and whether or not the Python-3-specific syntax detailed above is allowed. Where Python 3 and Python 2 syntax standards differ, Coconut syntax will always follow Python 3 across all targets. The supported targets are:

- universal (default) (will work on _any_ of the below),
- `2`, `26` (will work on any Python `>= 2.6` but `< 3`),
- `27` (will work on any Python `>= 2.7` but `< 3`),
- `3`, `32` (will work on any Python `>= 3.2`),
- `33`, `34` (will work on any Python `>= 3.3`),
- `35` (will work on any Python `>= 3.5`),
- `36` (will work on any Python `>= 3.6`),
- `sys` (chooses the specific target corresponding to the current version).

_Note: Periods are ignored in target specifications, such that the target `2.7` is equivalent to the target `27`._

### `strict` Mode

If the `--strict` (or `-s`) flag is enabled, Coconut will throw errors on various style problems. These are
- mixing of tabs and spaces (without `--strict` will show a Warning),
- missing new line at end of file (without `--strict` will show a Warning),
- use of `from __future__` imports (without `--strict` will show a Warning)
- trailing whitespace at end of lines,
- semicolons at end of lines,
- use of the Python-style `lambda` statement,
- use of `u` to denote Unicode strings, and
- use of backslash continuations (use parenthetical continuation instead).

It is recommended that you use the `--strict` (or `-s`) flag if you are starting a new Coconut project, as it will help you write cleaner code.

## Integrations

### Syntax Highlighting

The current options for Coconut syntax highlighting are:

1. use **[SublimeText](https://www.sublimetext.com/)** (instructions below),
2. use an editor that supports **[Pygments](http://pygments.org/)** (instructions below),
3. use [`coconut.vim`](https://github.com/manicmaniac/coconut.vim), a third-party **[Vim](http://www.vim.org/)** highlighter,
4. use [`coconut-mode`](https://github.com/NickSeagull/coconut-mode), a third-party **[Emacs](https://www.gnu.org/software/emacs/)** highlighter, or
4. just treat Coconut as Python.

Instructions on how to set up syntax highlighting for SublimeText and Pygments are included below. If one of the actual highlighters above doesn't work, however, it should be sufficient to set up your editor so it interprets all `.coco` (also `.coc` and `.coconut`, although `.coco` is the preferred extension) files as Python code, as this should highlight most of your code well enough.

#### SublimeText

Coconut syntax highlighting for SublimeText requires that [Package Control](https://packagecontrol.io/installation), the standard package manager for SublimeText, be installed. Once that is done, simply:

1. open the SublimeText command palette by pressing `Ctrl+Shift+P`,
2. enter and select `Package Control: Install Package`, and
3. finally enter and select `Coconut`.

To make sure everything is working properly, open a `.coco` file, and make sure `Coconut` appears in the bottom right-hand corner. If something else appears, like `Plain Text`, click on it, select `Open all with current extension as...` at the top of the resulting menu, and then select `Coconut`.

#### Pygments

The same `pip install coconut` command that installs the Coconut command-line utility will also install the `coconut` Pygments lexer. How to use this lexer depends on the Pygments-enabled application being used, but in general simply enter `coconut` as the language being highlighted and/or use a valid Coconut file extension (`.coco`, `.coc`, or `.coconut`) and Pygments should be able to figure it out. For example, this documentation is generated with [Sphinx](http://www.sphinx-doc.org/en/stable/), with the syntax highlighting you see created by adding the line
```coconut_python
highlight_language = "coconut"
```
to Coconut's `conf.py`.

### IPython/Jupyter Support

If you prefer [IPython](http://ipython.org/) (the python kernel for the [Jupyter](http://jupyter.org/) framework) to the normal Python shell, Coconut can be used as a Jupyter kernel or IPython extension.

#### Kernel

If Coconut is used as a kernel, all code in the console or notebook will be sent directly to Coconut instead of Python to be evaluated. Otherwise, the Coconut kernel behaves exactly like the IPython kernel, including support for `%magic` commands.

The command `coconut --jupyter notebook` (or `coconut --ipython notebook`) will launch an IPython/Jupyter notebook using Coconut as the kernel and the command `coconut --jupyter console` (or `coconut --ipython console`) will launch an IPython/Jupyter console using Coconut as the kernel. Additionally, the command `coconut --jupyter` (or `coconut --ipython`) will add Coconut as a language option inside of all IPython/Jupyter notebooks, even those not launched with Coconut. This command may need to be re-run when a new version of Coconut is installed.

#### Extension

If Coconut is used as an extension, a special magic command will send snippets of code to be evaluated using Coconut instead of IPython, but IPython will still be used as the default.

The line magic `%load_ext coconut` will load Coconut as an extension, providing the `%coconut` and `%%coconut` magics and adding Coconut built-ins. The `%coconut` line magic will run a line of Coconut with default parameters, and the `%%coconut` block magic will take command-line arguments on the first line, and run any Coconut code provided in the rest of the cell with those parameters.

### MyPy Integration

Coconut has the ability to integrate with [MyPy](http://mypy-lang.org/) to provide optional static type-checking, including for all Coconut built-ins.

Simply pass `--mypy` (be careful to pass it only as the last argument), use [standard Python 3 type annotation syntax](https://www.python.org/dev/peps/pep-0484/), and Coconut will take care of the rest. By default, Coconut compiles Python 3 type annotations into `mypy --py2` compatible type comments. If you want to keep the Python 3 type annotations instead, simply pass `--target 3`.

In addition to function argument type annotation, Coconut also supports variable type annotation using the [new Python 3.6 syntax](https://www.python.org/dev/peps/pep-0526/), which compiles to `mypy --py2` compatible type comments unless `--target 3.6` is specified.

Coconut even supports `--mypy` in the interpreter, which will intelligently scan each new line of code, in the context of previous lines, for newly-introduced MyPy errors. For example:
```coconut
>>> a: str = count()[0]
<string>:14: error: Incompatible types in assignment (expression has type "int", variable has type "str")
```

_Note: Since [tail call optimization](#tail-call-optimization) prevents proper type-checking, `--mypy` implicitly disables it._

## Operators

### Lambdas

Coconut provides the simple, clean `->` operator as an alternative to Python's `lambda` statements. The syntax for the `->` operator is `(arguments) -> expression`. The operator has the same precedence as the old statement, which means it will often be necessary to surround the lambda in parentheses.

Additionally, Coconut also supports an implicit usage of the `->` operator of the form `(-> expression)`, which is equivalent to `((_=None) -> expression)`, which allows an implicit lambda to be used both when no arguments are required, and when one argument (assigned to `_`) is required.

_Note: If normal lambda syntax is insufficient, Coconut also supports an extended lambda syntax in the form of [statement lambdas](#statement-lambdas)._

##### Rationale

In Python, lambdas are ugly and bulky, requiring the entire word `lambda` to be written out every time one is constructed. This is fine if in-line functions are very rarely needed, but in functional programming in-line functions are an essential tool.

##### Python Docs

Lambda forms (lambda expressions) have the same syntactic position as expressions. They are a shorthand to create anonymous functions; the expression `(arguments) -> expression` yields a function object. The unnamed object behaves like a function object defined with:
```coconut
def <lambda>(arguments):
    return expression
```
Note that functions created with lambda forms cannot contain statements or annotations.

##### Example

**Coconut:**
```coconut
dubsums = map((x, y) -> 2*(x+y), range(0, 10), range(10, 20))
dubsums |> list |> print
```

**Python:**
```coconut_python
dubsums = map(lambda x, y: 2*(x+y), range(0, 10), range(10, 20))
print(list(dubsums))
```

### Partial Application

Coconut uses a `$` sign right after a function's name but before the open parenthesis used to call the function to denote partial application. It has the same precedence as subscription.

Coconut's partial application also supports the use of a `?` to skip partially applying an argument, deferring filling in that argument until the partially-applied function is called. This is useful if you want to partially apply argument(s) that aren't first in the argument order.

##### Rationale

Partial application, or currying, is a mainstay of functional programming, and for good reason: it allows the dynamic customization of functions to fit the needs of where they are being used. Partial application allows a new function to be created out of an old function with some of its arguments pre-specified.

##### Python Docs

Return a new `partial` object which when called will behave like _func_ called with the positional arguments _args_ and keyword arguments _keywords_. If more arguments are supplied to the call, they are appended to _args_. If additional keyword arguments are supplied, they extend and override _keywords_. Roughly equivalent to:
```coconut_python
def partial(func, *args, **keywords):
    def newfunc(*fargs, **fkeywords):
        newkeywords = keywords.copy()
        newkeywords.update(fkeywords)
        return func(*(args + fargs), **newkeywords)
    newfunc.func = func
    newfunc.args = args
    newfunc.keywords = keywords
    return newfunc
```
The `partial` object is used for partial function application which “freezes” some portion of a function's arguments and/or keywords resulting in a new object with a simplified signature.

##### Example

**Coconut:**
```coconut
expnums = range(5) |> map$(pow$(?, 2))
expnums |> list |> print
```

**Python:**
```coconut_python
# unlike this simple lambda, $ produces a pickleable object
expnums = map(lambda x: pow(x, 2), range(5))
print(list(expnums))
```

### Pipeline

Coconut uses pipe operators for pipeline-style function application. All the operators have a precedence in-between infix calls and comparisons and are left-associative. All operators also support in-place versions. The different operators are:
```coconut
(|>)    => pipe forward
(|*>)   => multiple-argument pipe forward
(<|)    => pipe backward
(<*|)   => multiple-argument pipe backward
```

##### Example

**Coconut:**
```coconut
def sq(x) = x**2
(1, 2) |*> (+) |> sq |> print
```

**Python:**
```coconut_python
import operator
def sq(x): return x**2
print(sq(operator.add(1, 2)))
```

### Compose

Coconut uses the `..` operator for function composition. It has a precedence in-between subscription and exponentiation. The in-place operator is `..=`.

##### Example

**Coconut:**
```coconut
fog = f..g
```

**Python:**
```coconut_python
# unlike this simple lambda, .. produces a pickleable object
fog = lambda *args, **kwargs: f(g(*args, **kwargs))
```

### Chain

Coconut uses the `::` operator for iterator chaining. Coconut's iterator chaining is done lazily, in that the arguments are not evaluated until they are needed. It has a precedence in-between bitwise or and infix calls. The in-place operator is `::=`.

##### Rationale

A useful tool to make working with iterators as easy as working with sequences is the ability to lazily combine multiple iterators together. This operation is called chain, and is equivalent to addition with sequences, except that nothing gets evaluated until it is needed.

##### Python Docs

Make an iterator that returns elements from the first iterable until it is exhausted, then proceeds to the next iterable, until all of the iterables are exhausted. Used for treating consecutive sequences as a single sequence. Chained inputs are evaluated lazily. Roughly equivalent to:
```coconut_python
def chain(*iterables):
    # chain('ABC', 'DEF') --> A B C D E F
    for it in iterables:
        for element in it:
            yield element
```

##### Example

**Coconut:**
```coconut
def N(n=0) = (n,) :: N(n+1)  # no infinite loop because :: is lazy

(range(-10, 0) :: N())$[5:15] |> list |> print
```

**Python:**
_Can't be done without a complicated iterator comprehension in place of the lazy chaining. See the compiled code for the Python syntax._

### Iterator Slicing

Coconut uses a `$` sign right after an iterator before a slice to perform iterator slicing. Coconut's iterator slicing works much the same as Python's sequence slicing, and looks much the same as Coconut's partial application, but with brackets instead of parentheses. It has the same precedence as subscription.

Iterator slicing works just like sequence slicing, including support for negative indices and slices, and support for `slice` objects in the same way as can be done with normal slicing. Iterator slicing makes no guarantee, however, that the original iterator passed to it be preserved (to preserve the iterator, use Coconut's [`tee` function](#tee)).

Coconut's iterator slicing is very similar to Python's `itertools.islice`, but unlike `itertools.islice`, Coconut's iterator slicing supports negative indices, and will preferentially call an object's `__getitem__`, if it exists. Coconut's iterator slicing is also optimized to work well with all of Coconut's built-in objects, only computing the elements of each that are actually necessary to extract the desired slice.

##### Example

**Coconut:**
```coconut
map((x)->x*2, range(10**100))$[-1] |> print
```

**Python:**
_Can't be done without a complicated iterator slicing function and inspection of custom objects. The necessary definitions in Python can be found in the Coconut header._

### Unicode Alternatives

Coconut supports Unicode alternatives to many different operator symbols. The Unicode alternatives are relatively straightforward, and chosen to reflect the look and/or meaning of the original symbol.

##### Full List

```
→ (\u2192)                  => "->"
↦ (\u21a6)                  => "|>"
*↦ (*\u21a6)                => "|*>"
↤ (\u21a4)                  => "<|"
↤* (\u21a4*)                => "<*|"
⋅ (\u22c5)                  => "*"
↑ (\u2191)                  => "**"
÷ (\xf7)                    => "/"
÷/ (\xf7/)                  => "//"
∘ (\u2218)                  => ".."
− (\u2212)                  => "-" (only subtraction)
⁻ (\u207b)                  => "-" (only negation)
¬ (\xac)                    => "~"
≠ (\u2260) or ¬= (\xac=)    => "!="
≤ (\u2264)                  => "<="
≥ (\u2265)                  => ">="
∧ (\u2227) or ∩ (\u2229)    => "&"
∨ (\u2228) or ∪ (\u222a)    => "|"
⊻ (\u22bb) or ⊕ (\u2295)    => "^"
« (\xab)                    => "<<"
» (\xbb)                    => ">>"
… (\u2026)                  => "..."
× (\xd7)                    => "@" (only matrix multiplication)
```

## Keywords

### `data`

The syntax for `data` blocks is a cross between the syntax for functions and the syntax for classes. The first line looks like a function definition, but the rest of the body looks like a class, usually containing method definitions. This is because while `data` blocks actually end up as classes in Python, Coconut automatically creates a special, immutable constructor based on the given arguments.

Coconut `data` blocks create immutable classes derived from `collections.namedtuple` and made immutable with `__slots__`. Coconut data statement syntax looks like:
```coconut
data <name>(<args>) [from <inherits>]:
    <body>
```
`<name>` is the name of the new data type, `<args>` are the arguments to its constructor as well as the names of its attributes, `<body>` contains the data type's methods, and `<inherits>` optionally contains any desired base classes.

In addition to supporting standard `collections.namedtuple` subclassing when `<args>` is a list of names, Coconut also supports an extended version where `<args>` can contain a starred argument to collect extra parameters.

Subclassing `data` types can be done easily by inheriting from them either in another `data` statement or a normal Python `class`. If a normal `class` statement is used, making the new subclass immutable will require adding the line
```coconut
__slots__ = ()
```
which will need to be put in the subclass body before any method or attribute definitions.

##### Rationale

A mainstay of functional programming that Coconut improves in Python is the use of values, or immutable data types. Immutable data can be very useful because it guarantees that once you have some data it won't change, but in Python creating custom immutable data types is difficult. Coconut makes it very easy by providing `data` blocks.

##### Python Docs

Returns a new tuple subclass. The new subclass is used to create tuple-like objects that have fields accessible by attribute lookup as well as being indexable and iterable. Instances of the subclass also have a helpful docstring (with type names and field names) and a helpful `__repr__()` method which lists the tuple contents in a `name=value` format.

Any valid Python identifier may be used for a field name except for names starting with an underscore. Valid identifiers consist of letters, digits, and underscores but do not start with a digit or underscore and cannot be a keyword such as _class, for, return, global, pass, or raise_.

Named tuple instances do not have per-instance dictionaries, so they are lightweight and require no more memory than regular tuples.

##### Examples

**Coconut:**
```coconut
data vector2(x, y):
    def __abs__(self):
        return (self.x**2 + self.y**2)**.5

v = vector2(3, 4)
v |> print  # all data types come with a built-in __repr__
v |> abs |> print
v.x = 2  # this will fail because data objects are immutable
```
_Showcases the syntax, features, and immutable nature of `data` types._
```coconut
data Empty()
data Leaf(n)
data Node(l, r)

def size(Empty()) = 0

@addpattern(size)
def size(Leaf(n)) = 1

@addpattern(size)
def size(Node(l, r)) = size(l) + size(r)

size(Node(Empty(), Leaf(10))) == 1
```
_Showcases the algebraic nature of `data` types when combined with pattern-matching._
```coconut
data vector(*pts):
    """Immutable arbitrary-length vector."""

    def __abs__(self) =
        self.pts |> map$(pow$(?, 2)) |> sum |> pow$(?, 0.5)

    def __add__(self, other) =
        vector(*other_pts) = other
        assert len(other_pts) == len(self.pts)
        map((+), self.pts, other_pts) |*> vector

    def __neg__(self) =
        self.pts |> map$((-)) |*> vector

    def __sub__(self, other) =
        self + -other
```
_Showcases starred `data` declaration._

**Python:**
```coconut_python
import collections
class vector2(collections.namedtuple("vector2", "x, y")):
    __slots__ = ()
    def __abs__(self):
        return (self.x**2 + self.y**2)**.5

v = vector2(3, 4)
print(v)
print(abs(v))
v.x = 2
```
```coconut
import collections
class Empty(collections.namedtuple("Empty", "")):
    __slots__ = ()
class Leaf(collections.namedtuple("Leaf", "n")):
    __slots__ = ()
class Node(collections.namedtuple("Node", "l, r")):
    __slots__ = ()

def size(tree):
    if isinstance(tree, Empty):
        return 0
    elif isinstance(tree, Leaf):
        return 1
    elif isinstance(tree, Node):
        return size(tree[0]) + size(tree[1])
    else:
        raise MatchError()

size(Node(Empty(), Leaf(10))) == 1
```
_Starred data declarations can't be done without a long sequence of method definitions. See the compiled code for the Python syntax._

### `match`

Coconut provides fully-featured, functional pattern-matching through its `match` statements.

##### Overview

Match statements follow the basic syntax `match <pattern> in <value>`. The match statement will attempt to match the value against the pattern, and if successful, bind any variables in the pattern to whatever is in the same position in the value, and execute the code below the match statement. Match statements also support, in their basic syntax, an `if <cond>` that will check the condition after executing the match before executing the code below, and an `else` statement afterwards that will only be executed if the `match` statement is not. What is allowed in the match statement's pattern has no equivalent in Python, and thus the specifications below are provided to explain it.

##### Syntax Specification

Coconut match statement syntax is
```coconut
match <pattern> in <value> [if <cond>]:
    <body>
[else:
    <body>]
```
where `<value>` is the item to match against, `<cond>` is an optional additional check, and `<body>` is simply code that is executed if the header above it succeeds. `<pattern>` follows its own, special syntax, defined roughly like so:

```coconut
pattern ::= (
    "(" pattern ")"                 # parentheses
    | "None" | "True" | "False"     # constants
    | "=" NAME                      # check
    | NUMBER                        # numbers
    | STRING                        # strings
    | [pattern "as"] NAME           # capture
    | NAME "(" patterns ")"         # data types
    | "(" patterns ")"              # sequences can be in tuple form
    | "[" patterns "]"              #  or in list form
    | "(|" patterns "|)"            # lazy lists
    | "{" pattern_pairs "}"         # dictionaries
    | ["s"] "{" pattern_consts "}"  # sets
    | ("(" | "[")                   # star splits
        patterns,
        "*" middle,
        patterns
      (")" | "]")                       # must both be parens or brackets
    | (                             # head-tail splits
        "(" patterns ")"
        | "[" patterns "]"
      ) "+" pattern
    | pattern "+" (                 # init-last splits
        "(" patterns ")"
        | "[" patterns "]"
      )
    | (                             # head-last splits
        "(" patterns ")"
        | "[" patterns "]"
      ) "+" pattern "+" (
        "(" patterns ")"                # this match must be the same
        | "[" patterns "]"              #  construct as the first match
      )
    | (                             # iterator splits
        "(" patterns ")"
        | "[" patterns "]"
        | "(|" patterns "|)"
      ) "::" pattern
    | pattern "is" exprs            # type-checking
    | pattern "and" pattern         # match all
    | pattern "or" pattern          # match any
    )
```

##### Semantic Specification

`match` statements will take their pattern and attempt to "match" against it, performing the checks and deconstructions on the arguments as specified by the pattern. The different constructs that can be specified in a pattern, and their function, are:
- Constants, Numbers, and Strings: will only match to the same constant, number, or string in the same position in the arguments.
- Variables: will match to anything, and will be bound to whatever they match to, with some exceptions:
  * If the same variable is used multiple times, a check will be performed that each use match to the same value.
  * If the variable name `_` is used, nothing will be bound and everything will always match to it.
- Explicit Bindings (`<pattern> as <var>`): will bind `<var>` to `<pattern>`.
- Checks (`=<var>`): will check that whatever is in that position is equal to the previously defined variable `<var>`.
- Type Checks (`<var> is <types>`): will check that whatever is in that position is of type(s) `<types>` before binding the `<var>`.
- Data Types (`<name>(<args>)`): will check that whatever is in that position is of data type `<name>` and will match the attributes to `<args>`.
- Lists (`[<patterns>]`), Tuples (`(<patterns>)`): will only match a sequence (`collections.abc.Sequence`) of the same length, and will check the contents against `<patterns>`.
- Lazy lists (`(|<patterns>|)`): same as list or tuple matching, but checks iterable (`collections.abc.Iterable`) instead of sequence.
- Dicts (`{<pairs>}`): will only match a mapping (`collections.abc.Mapping`) of the same length, and will check the contents against `<pairs>`.
- Sets (`{<constants>}`): will only match a set (`collections.abc.Set`) of the same length and contents.
- Head-Tail Splits (`<list/tuple> + <var>`): will match the beginning of the sequence against the `<list/tuple>`, then bind the rest to `<var>`, and make it the type of the construct used.
- Init-Last Splits (`<var> + <list/tuple>`): exactly the same as head-tail splits, but on the end instead of the beginning of the sequence.
- Head-Last Splits (`<list/tuple> + <var> + <list/tuple>`): the combination of a head-tail and an init-last split.
- Iterator Splits (`<list/tuple/lazy list> :: <var>`): will match the beginning of an iterable (`collections.abc.Iterable`) against the `<list/tuple/lazy list>`, then bind the rest to `<var>` or check that the iterable is done.

_Note: Like [iterator slicing](#iterator-slicing), iterator and lazy list matching make no guarantee that the original iterator matched against be preserved (to preserve the iterator, use Coconut's [`tee` function](#tee)._

When checking whether or not an object can be matched against in a particular fashion, Coconut makes use of Python's abstract base classes. Therefore, to enable proper matching for a custom object, register it with the proper abstract base classes.

##### Examples

**Coconut:**
```coconut
def factorial(value):
    match 0 in value:
        return 1
    else: match n is int in value if n > 0:  # possible because of Coconut's
        return n * factorial(n-1)            #   enhanced else statements
    else:
        raise TypeError("invalid argument to factorial of: "+repr(value))

3 |> factorial |> print
```
_Showcases `else` statements, which work much like `else` statements in Python: the code under an `else` statement is only executed if the corresponding match fails._
```coconut
data point(x, y):
    def transform(self, other):
        match point(x, y) in other:
            return point(self.x + x, self.y + y)
        else:
            raise TypeError("arg to transform must be a point")
    def __eq__(self, other):
        match point(=self.x, =self.y) in other:
            return True
        else:
            return False

point(1,2) |> point(3,4).transform |> print
point(1,2) |> (==)$(point(1,2)) |> print
```
_Showcases matching to data types. Values defined by the user with the `data` statement can be matched against and their contents accessed by specifically referencing arguments to the data type's constructor._
```coconut
data Empty()
data Leaf(n)
data Node(l, r)
Tree = (Empty, Leaf, Node)  # type union

def depth(Tree()) = 0

@addpattern(depth)
def depth(Tree(n)) = 1

@addpattern(depth)
def depth(Tree(l, r)) = 1 + max([depth(l), depth(r)])

Empty() |> depth |> print
Leaf(5) |> depth |> print
Node(Leaf(2), Node(Empty(), Leaf(3))) |> depth |> print
```
_Showcases how the combination of data types and match statements can be used to powerful effect to replicate the usage of algebraic data types in other functional programming languages._
```coconut
def duplicate_first([x] + xs as l) =
    [x] + l

[1,2,3] |> duplicate_first |> print
```
_Showcases head-tail splitting, one of the most common uses of pattern-matching, where a `+ <var>` (or `:: <var>` for any iterable) at the end of a list or tuple literal can be used to match the rest of the sequence._
```
def sieve([head] :: tail) =
    [head] :: sieve(n for n in tail if n % head)

@addpattern(sieve)
def sieve((||)) = []
```
_Showcases how to match against iterators, namely that the empty iterator case (`(||)`) must come last, otherwise that case will exhaust the whole iterator before any other pattern has a chance to match against it._

**Python:**
_Can't be done without a long series of checks for each `match` statement. See the compiled code for the Python syntax._

### `case`

Coconut's `case` statement is an extension of Coconut's `match` statement for performing multiple `match` statements against the same value, where only one of them should succeed. Unlike lone `match` statements, only one match statement inside of a `case` block will ever succeed, and thus more general matches should be put below more specific ones.

Each pattern in a case block is checked until a match is found, and then the corresponding body is executed, and the case block terminated. The syntax for case blocks is
```coconut
case <value>:
    match <pattern> [if <cond>]:
        <body>
    match <pattern> [if <cond>]:
        <body>
    ...
[else:
    <body>]
```
where `<pattern>` is any `match` pattern, `<value>` is the item to match against, `<cond>` is an optional additional check, and `<body>` is simply code that is executed if the header above it succeeds. Note the absence of an `in` in the `match` statements: that's because the `<value>` in `case <value>` is taking its place.

##### Example

**Coconut:**
```coconut
def classify_sequence(value):
    out = ""        # unlike with normal matches, only one of the patterns
    case value:     #  will match, and out will only get appended to once
        match ():
            out += "empty"
        match (_,):
            out += "singleton"
        match (x,x):
            out += "duplicate pair of "+str(x)
        match (_,_):
            out += "pair"
        match _ is (tuple, list):
            out += "sequence"
    else:
        raise TypeError()
    return out

[] |> classify_sequence |> print
() |> classify_sequence |> print
[1] |> classify_sequence |> print
(1,1) |> classify_sequence |> print
(1,2) |> classify_sequence |> print
(1,1,1) |> classify_sequence |> print
```

**Python:**
_Can't be done without a long series of checks for each `match` statement. See the compiled code for the Python syntax._

### Backslash-Escaping

In Coconut, the keywords `data`, `match`, `case`, `async` (keyword in Python 3.5), and `await` (keyword in Python 3.5) are also valid variable names. While Coconut can disambiguate these two use cases, when using one of these keywords as a variable name, a backslash is allowed in front to be explicit about using a keyword as a variable name.

##### Example

**Coconut:**
```coconut
\data = 5
print(\data)
```

**Python:**
```coconut_python
data = 5
print(data)
```

### Reserved Variables

It is illegal for a variable name to start with `_coconut`, as these variables are reserved for the compiler. If interacting with such variables is necessary, use [code passthrough](#code-passthrough).

## Expressions

### Statement Lambdas

The statement lambda syntax is an extension of the [normal lambda syntax](#lambdas) to support statements, not just expressions.

The syntax for a statement lambda is:
```
def (arguments) -> statement; statement; ...
```
where `arguments` can be standard function arguments or [pattern-matching function definition](#pattern-matching-functions) arguments and `statement` can be an assignment statement or a keyword statement. If the last `statement` (not followed by a semicolon) is an `expression`, it will automatically be returned.

Statement lambdas also support implicit lambda syntax, where when the arguments are omitted, as in `def -> _`, `def (_=None) -> _` is assumed.

##### Example

**Coconut:**
```coconut
L |> map$(def (x) -> y = 1 / x; y*(1 - y))
```

**Python:**
```coconut_python
def _lambda(x):
    y = 1 / x
    return y*(1 - y)
map(_lambda, L)
```

### Lazy Lists

Coconut supports the creation of lazy lists, where the contents in the list will be treated as an iterator and not evaluated until they are needed. Lazy lists can be created in Coconut simply by simply surrounding a comma-seperated list of items with `(|` and `|)` (so-called "banana brackets") instead of `[` and `]` for a list or `(` and `)` for a tuple.

Lazy lists use the same machinery as iterator chaining to make themselves lazy, and thus the lazy list `(| x, y |)` is equivalent to the iterator chaining expression `(x,) :: (y,)`, although the lazy list won't construct the intermediate tuples.

##### Rationale

Lazy lists, where sequences are only evaluated when their contents are requested, are a mainstay of functional programming, allowing for dynamic evaluation of the list's contents.

##### Example

**Coconut:**
```coconut
(| print("hello,"), print("world!") |) |> consume
```

**Python:**
_Can't be done without a complicated iterator comprehension in place of the lazy list. See the compiled code for the Python syntax._

### Implicit Partial Application

Coconut supports a number of different syntactical aliases for common partial application use cases. These are:
```coconut
.attr           =>      operator.attrgetter("attr")
.method(args)   =>      operator.methodcaller("method", args)
obj.            =>      getattr$(obj)
func$           =>      ($)$(func)
seq[]           =>      operator.getitem$(seq)
iter$[]         =>      # the equivalent of seq[] for iterators
.[a:b:c]        =>      operator.itemgetter(slice(a, b, c))
.$[a:b:c]       =>      # the equivalent of .[a:b:c] for iterators
```

##### Example

**Coconut:**
```coconut
1 |> "123"[]
mod$ <| 5 <| 3
```

**Python:**
```coconut_python
"123"[1]
mod(5, 3)
```

### Set Literals

Coconut allows an optional `s` to be prepended in front of Python set literals. While in most cases this does nothing, in the case of the empty set it lets Coconut know that it is an empty set and not an empty dictionary. Additionally, an `f` is also supported, in which case a Python `frozenset` will be generated instead of a normal set.

##### Example

**Coconut:**
```coconut
empty_frozen_set = f{}
```

**Python:**
```coconut_python
empty_frozen_set = frozenset()
```

### Imaginary Literals

In addition to Python's `<num>j` or `<num>J` notation for imaginary literals, Coconut also supports `<num>i` or `<num>I`, to make imaginary literals more readable if used in a mathematical context.

##### Python Docs

Imaginary literals are described by the following lexical definitions:
```coconut
imagnumber ::= (floatnumber | intpart) ("j" | "J" | "i" | "I")
```
An imaginary literal yields a complex number with a real part of 0.0. Complex numbers are represented as a pair of floating point numbers and have the same restrictions on their range. To create a complex number with a nonzero real part, add a floating point number to it, e.g., `(3+4i)`. Some examples of imaginary literals:
```coconut
3.14i   10.i    10i     .001i   1e100i  3.14e-10i
```

##### Example

**Coconut:**
```coconut
3 + 4i |> abs |> print
```

**Python:**
```coconut_python
print(abs(3 + 4j))
```

### Underscore Separators

Coconut allows for one underscore between digits and after base specifiers in numeric literals as specified in [PEP 515](https://www.python.org/dev/peps/pep-0515/). These underscores are ignored and should only be used to increase code readability.

##### Example

**Coconut:**
```coconut
10_000_000.0
```

**Python:**
```coconut_python
10000000.0
```

## Function Definition

### Tail Call Optimization

Coconut will perform automatic tail call optimization and tail recursion elimination on any function that meets the following criteria:

1. it must directly return (using either `return` or [assignment function notation](#assignment-functions)) a call to itself (tail recursion elimination, the most powerful optimization) or another function (tail call optimization).
2. it must not be a generator (uses `yield`) or an asynchronous function (uses `async`).

_Note: Tail call optimization (though not tail recursion elimination) will work even for 1) mutual recursion and 2) pattern-matching functions split across multiple definitions using [`addpattern`](#addpattern) or [`prepattern`](#prepattern)._

If you are encountering a `RuntimeError` due to maximum recursion depth, it is highly recommended that you rewrite your function to meet either the criteria above for tail call optimization, or the corresponding criteria for [`recursive_iterator`](#recursive-iterator), either of which should prevent such errors.

_Note: Tail call optimization and tail recursion elimination can be turned off by passing the `--no-tco` command-line option._

##### Example

**Coconut:**
```coconut
# unlike in Python, this function will never hit a maximum recursion depth error
def factorial(n, acc=1):
    case n:
        match 0:
            return acc
        match _ is int if n > 0:
            return factorial(n-1, acc*n)
```
_Showcases tail recursion elimination._
```coconut
# unlike in Python, neither of these functions will ever hit a maximum recursion depth error
def is_even(0) = True
@addpattern(is_even)
def is_even(n is int if n > 0) = is_odd(n-1)

def is_odd(0) = False
@addpattern(is_odd)
def is_odd(n is int if n > 0) = is_even(n-1)
```
_Showcases tail call optimization._

**Python:**
_Can't be done without rewriting the function(s)._

### Operator Functions

Coconut uses a simple operator function short-hand: surround an operator with parentheses to retrieve its function. Similarly to iterator comprehensions, if the operator function is the only argument to a function, the parentheses of the function call can also serve as the parentheses for the operator function.

##### Rationale

A very common thing to do in functional programming is to make use of function versions of built-in operators: currying them, composing them, and piping them. To make this easy, Coconut provides a short-hand syntax to access operator functions.

##### Full List

```coconut
(|>)        => # pipe forward
(|*>)       => # multi-arg pipe forward
(<|)        => # pipe backward
(<*|)       => # multi-arg pipe backward
(..)        => # function composition
(.)         => (getattr)
(::)        => (itertools.chain) # will not evaluate its arguments lazily
($)         => (functools.partial)
(+)         => (operator.add)
(-)         => # 1 arg: operator.neg, 2 args: operator.sub
(*)         => (operator.mul)
(**)        => (operator.pow)
(/)         => (operator.truediv)
(//)        => (operator.floordiv)
(%)         => (operator.mod)
(&)         => (operator.and_)
(^)         => (operator.xor)
(|)         => (operator.or_)
(<<)        => (operator.lshift)
(>>)        => (operator.rshift)
(<)         => (operator.lt)
(>)         => (operator.gt)
(==)        => (operator.eq)
(<=)        => (operator.le)
(>=)        => (operator.ge)
(!=)        => (operator.ne)
(~)         => (operator.inv)
(@)         => (operator.matmul)
(not)       => (operator.not_)
(and)       => # boolean and
(or)        => # boolean or
(is)        => (operator.is_)
(in)        => (operator.contains)
```

##### Example

**Coconut:**
```coconut
(range(0, 5), range(5, 10)) |*> map$(+) |> list |> print
```

**Python:**
```coconut_python
import operator
print(list(map(operator.add, range(0, 5), range(5, 10))))
```

### Assignment Functions

Coconut allows for assignment function definition that automatically returns the last line of the function body. An assignment function is constructed by substituting `=` for `:` after the function definition line. Thus, the syntax for assignment function definition is either
```coconut
def <name>(<args>) = <expr>
```
for one-liners or
```coconut
def <name>(<args>) =
    <stmts>
    <expr>
```
for full functions, where `<name>` is the name of the function, `<args>` are the functions arguments, `<stmts>` are any statements that the function should execute, and `<expr>` is the value that the function should return.

_Note: Assignment function definition can be combined with infix and/or pattern-matching function definition._

##### Rationale

Coconut's Assignment function definition is as easy to write as assignment to a lambda, but will appear named in tracebacks, as it compiles to normal Python function definition.

##### Example

**Coconut:**
```coconut
def binexp(x) = 2**x
5 |> binexp |> print
```

**Python:**
```coconut_python
def binexp(x): return 2**x
print(binexp(5))
```

### Pattern-Matching Functions

Coconut supports pattern-matching on the arguments to a function in that function's definition. The syntax for pattern-matching function definition is
```coconut
[match] def <name>(<pattern> [= <default>], ... [if <cond>]):
    <body>
```
where `<name>` is the name of the function, `<cond>` is an optional additional check, `<body>` is the body of the function, `<pattern>` is defined by Coconut's [`match` statement](#match), and `<default>` is the optional default if no argument is passed. The `match` keyword at the beginning is optional, but is sometimes necessary to disambiguate pattern-matching function definition from normal function definition, which will always take precedence.

If `<pattern>` has a variable name (either directly or with `as`), the resulting pattern-matching function will support keyword arguments using that variable name. If pattern-matching function definition fails, it will raise a [`MatchError`](#matcherror) object just like [destructuring assignment](#destructuring-assignment).

_Note: Pattern-matching function definition can be combined with assignment and/or infix function definition._

##### Example

**Coconut:**
```coconut
def last_two(_ + [a, b]):
    return a, b
def xydict_to_xytuple({"x":x is int, "y":y is int}):
    return x, y

range(5) |> last_two |> print
{"x":1, "y":2} |> xydict_to_xytuple |> print
```

**Python:**
_Can't be done without a long series of checks at the top of the function. See the compiled code for the Python syntax._

### Infix Functions

Coconut allows for infix function calling, where a function is surrounded by backticks and then can have arguments placed in front of or behind it. Backtick calling has a precedence in-between chaining and piping.

Coconut also supports infix function definition to make defining functions that are intended for infix usage simpler. The syntax for infix function definition is
```coconut
def <arg> `<name>` <arg>:
    <body>
```
where `<name>` is the name of the function, the `<arg>`s are the function arguments, and `<body>` is the body of the function. If an `<arg>` includes a default, the `<arg>` must be surrounded in parentheses.

_Note: Infix function definition can be combined with assignment and/or pattern-matching function definition._

##### Rationale

A common idiom in functional programming is to write functions that are intended to behave somewhat like operators, and to call and define them by placing them between their arguments. Coconut's infix syntax makes this possible.

##### Example

**Coconut:**
```coconut
def a `mod` b = a % b
(x `mod` 2) `print`
```

**Python:**
```coconut_python
def mod(a, b): return a % b
print(mod(x, 2))
```

### Dotted Function Definition

Coconut allows for function definition using a dotted name to assign a function as a method of an object as specified in [PEP 542](https://www.python.org/dev/peps/pep-0542/).

##### Example

**Coconut:**
```coconut
def MyClass.my_method(self):
    ...
```

**Python:**
```coconut_python
def my_method(self):
    ...
MyClass.my_method = my_method
```

## Statements

### Destructuring Assignment

Coconut supports significantly enhanced destructuring assignment, similar to Python's tuple/list destructuring, but much more powerful. The syntax for Coconut's destructuring assignment is
```coconut
[match] <pattern> = <value>
```
where `<value>` is any expression and `<pattern>` is defined by Coconut's [`match` statement](#match). The `match` keyword at the beginning is optional, but is sometimes necessary to disambiguate destructuring assignment from normal assignment, which will always take precedence. Coconut's destructuring assignment is equivalent to a match statement that follows the syntax:
```coconut
match <pattern> in <value>:
    pass
else:
    err = MatchError(<error message>)
    err.pattern = "<pattern>"
    err.value = <value>
    raise err
```
If a destructuring assignment statement fails, then instead of continuing on as if a `match` block had failed, a [`MatchError`](#matcherror) object will be raised describing the failure.

##### Example

**Coconut:**
```coconut
_ + [a, b] = [0, 1, 2, 3]
print(a, b)
```

**Python:**
_Can't be done without a long series of checks in place of the destructuring assignment statement. See the compiled code for the Python syntax._

### Decorators

Unlike Python, which only supports a single variable or function call in a decorator, Coconut supports any expression.

##### Example

**Coconut:**
```coconut
@ wrapper1 .. wrapper2 $(arg)
def func(x) = x**2
```

**Python:**
```coconut_python
def wrapper(func):
    return wrapper1(wrapper2(arg, func))
@wrapper
def func(x):
    return x**2
```

### `else` Statements

Coconut supports the compound statements `try`, `if`, and `match` on the end of an `else` statement like any simple statement would be. This is most useful for mixing `match` and `if` statements together, but also allows for compound `try` statements.

##### Example

**Coconut:**
```coconut
if invalid(input_list):
    raise Exception()
else: match [head] + tail in input_list:
    print(head, tail)
else:
    print(input_list)
```

**Python:**
```coconut_python
from collections.abc import Sequence
if invalid(input_list):
    raise Exception()
elif isinstance(input_list, Sequence):
    head, tail = inputlist[0], inputlist[1:]
    print(head, tail)
else:
    print(input_list)
```

### `except` Statements

Python 3 requires that if multiple exceptions are to be caught, they must be placed inside of parentheses, so as to disallow Python 2's use of a comma instead of `as`. Coconut allows commas in except statements to translate to catching multiple exceptions without the need for parentheses, since, as in Python 3, `as` is always required to bind the exception to a name.

##### Example

**Coconut:**
```coconut
try:
    unsafe_func(arg)
except SyntaxError, ValueError as err:
    handle(err)
```

**Python:**
```coconut_python
try:
    unsafe_func(arg)
except (SyntaxError, ValueError) as err:
    handle(err)
```

### Implicit `pass`

Coconut supports the simple `class name(base)` and `data name(args)` as aliases for `class name(base): pass` and `data name(args): pass`.

##### Example

**Coconut:**
```coconut
data Empty
data Leaf(item)
data Node(left, right)
```

**Python:**
```coconut_python
import collections
class Empty(collections.namedtuple("Empty", "")):
    __slots__ = ()
class Leaf(collections.namedtuple("Leaf", "n")):
    __slots__ = ()
class Node(collections.namedtuple("Node", "l, r")):
    __slots__ = ()
```

### In-line `global` And `nonlocal` Assignment

Coconut allows for `global` or `nonlocal` to precede assignment to a variable or list of variables to make that assignment `global` or `nonlocal`, respectively.

##### Example

**Coconut:**
```coconut
global state_a, state_b = 10, 100
```

**Python:**
```coconut_python
global state_a, state_b; state_a, state_b = 10, 100
```

### Code Passthrough

Coconut supports the ability to pass arbitrary code through the compiler without being touched, for compatibility with other variants of Python, such as [Cython](http://cython.org/) or [Mython](http://mython.org/). Anything placed between `\(` and the corresponding close parenthesis will be passed through, as well as any line starting with `\\`, which will have the additional effect of allowing indentation under it.

##### Example

**Coconut:**
```coconut
\\cdef f(x):
    return x |> g
```

**Python:**
```coconut_python
cdef f(x):
    return g(x)
```

## Built-Ins

### Enhanced Built-Ins

Coconut's `map`, `zip`, `filter`, `reversed`, and `enumerate` objects are enhanced versions of their Python equivalents that support `reversed`, `repr`, optimized normal (and iterator) slicing (all but `filter`), `len` (all but `filter`), and have added attributes which subclasses can make use of to get at the original arguments to the object:

- `map`: `_func`, `_iters`
- `zip`: `_iters`
- `filter`: `_func`, `_iter`
- `reversed`: `_iter`
- `enumerate`: `_iter`, `_start`

##### Example

**Coconut:**
```coconut
map((+), range(5), range(6)) |> len |> print
range(10) |> filter$((x) -> x < 5) |> reversed |> tuple |> print
```

**Python:**
_Can't be done without defining a custom `map` type. The full definition of `map` can be found in the Coconut header._

### `addpattern`

Takes one argument that is a [pattern-matching function](#pattern-matching-functions), and returns a decorator that adds the patterns in the existing function to the new function being decorated, where the existing patterns are checked first, then the new. Roughly equivalent to:
```
def addpattern(base_func):
    """Decorator to add a new case to a pattern-matching function, where the new case is checked last."""
    def pattern_adder(func):
        def add_pattern_func(*args, **kwargs):
            try:
                return base_func(*args, **kwargs)
            except MatchError:
                return func(*args, **kwargs)
        return add_pattern_func
    return pattern_adder
```

##### Example

**Coconut:**
```
def factorial(0) = 1

@addpattern(factorial)
def factorial(n) = n * factorial(n - 1)
```

**Python:**
_Can't be done without a complicated decorator definition and a long series of checks for each pattern-matching. See the compiled code for the Python syntax._

### `prepattern`

Takes one argument that is a [pattern-matching function](#pattern-matching-functions), and returns a decorator that adds the patterns in the existing function to the new function being decorated, where the new patterns are checked first, then the existing.

Equivalent to:
```
def prepattern(base_func):
    """Decorator to add a new case to a pattern-matching function, where the new case is checked first."""
    def pattern_prepender(func):
        return addpattern(func)(base_func)
    return pattern_prepender
```

##### Example

**Coconut:**
```
def factorial(n) = n * factorial(n - 1)

@prepattern(factorial)
def factorial(0) = 1
```

**Python:**
_Can't be done without a complicated decorator definition and a long series of checks for each pattern-matching. See the compiled code for the Python syntax._

### `reduce`

Coconut re-introduces Python 2's `reduce` built-in, using the `functools.reduce` version.

##### Python Docs

**reduce**(_function, iterable_**[**_, initializer_**]**)

Apply _function_ of two arguments cumulatively to the items of _sequence_, from left to right, so as to reduce the sequence to a single value. For example, `reduce((x, y) -> x+y, [1, 2, 3, 4, 5])` calculates `((((1+2)+3)+4)+5)`. The left argument, _x_, is the accumulated value and the right argument, _y_, is the update value from the _sequence_. If the optional _initializer_ is present, it is placed before the items of the sequence in the calculation, and serves as a default when the sequence is empty. If _initializer_ is not given and _sequence_ contains only one item, the first item is returned.

##### Example

**Coconut:**
```coconut
product = reduce$(*)
range(1, 10) |> product |> print
```

**Python:**
```coconut_python
import operator
import functools
product = functools.partial(functools.reduce, operator.mul)
print(product(range(1, 10)))
```

### `takewhile`

Coconut provides `itertools.takewhile` as a built-in under the name `takewhile`.

##### Python Docs

**takewhile**(_predicate, iterable_)

Make an iterator that returns elements from the _iterable_ as long as the _predicate_ is true. Equivalent to:
```coconut_python
def takewhile(predicate, iterable):
    # takewhile(lambda x: x<5, [1,4,6,4,1]) --> 1 4
    for x in iterable:
        if predicate(x):
            yield x
        else:
            break
```

##### Example

**Coconut:**
```coconut
negatives = takewhile(numiter, (x) -> x<0)
```

**Python:**
```coconut_python
import itertools
negatives = itertools.takewhile(numiter, lambda x: x<0)
```

### `dropwhile`

Coconut provides `itertools.dropwhile` as a built-in under the name `dropwhile`.

##### Python Docs

**dropwhile**(_predicate, iterable_)

Make an iterator that drops elements from the _iterable_ as long as the _predicate_ is true; afterwards, returns every element. Note: the iterator does not produce any output until the predicate first becomes false, so it may have a lengthy start-up time. Equivalent to:
```coconut_python
def dropwhile(predicate, iterable):
    # dropwhile(lambda x: x<5, [1,4,6,4,1]) --> 6 4 1
    iterable = iter(iterable)
    for x in iterable:
        if not predicate(x):
            yield x
            break
    for x in iterable:
        yield x
```

##### Example

**Coconut:**
```coconut
positives = dropwhile(numiter, (x) -> x<0)
```

**Python:**
```coconut_python
import itertools
positives = itertools.dropwhile(numiter, lambda x: x<0)
```

### `tee`

Coconut provides an optimized version of `itertools.tee` as a built-in under the name `tee`.

##### Python Docs

**tee**(_iterable, n=2_)

Return _n_ independent iterators from a single iterable. Equivalent to:
```coconut_python
def tee(iterable, n=2):
    it = iter(iterable)
    deques = [collections.deque() for i in range(n)]
    def gen(mydeque):
        while True:
            if not mydeque:             # when the local deque is empty
                newval = next(it)       # fetch a new value and
                for d in deques:        # load it to all the deques
                    d.append(newval)
            yield mydeque.popleft()
    return tuple(gen(d) for d in deques)
```
Once `tee()` has made a split, the original _iterable_ should not be used anywhere else; otherwise, the _iterable_ could get advanced without the tee objects being informed.

This itertool may require significant auxiliary storage (depending on how much temporary data needs to be stored). In general, if one iterator uses most or all of the data before another iterator starts, it is faster to use `list()` instead of `tee()`.

##### Example

**Coconut:**
```coconut
original, temp = tee(original)
sliced = temp$[5:]
```

**Python:**
```coconut_python
import itertools
original, temp = itertools.tee(original)
sliced = itertools.islice(temp, 5, None)
```

### `consume`

Coconut provides the `consume` function to efficiently exhaust an iterator and thus perform any lazy evaluation contained within it. `consume` takes one optional argument, `keep_last`, that defaults to 0 and specifies how many, if any, items from the end to return as an iterable (`None` will keep all elements).

Equivalent to:
```coconut
def consume(iterable, keep_last=0):
    """Fully exhaust iterable and return the last keep_last elements."""
    return collections.deque(iterable, maxlen=keep_last) # fastest way to exhaust an iterator
```

##### Rationale

In the process of lazily applying operations to iterators, eventually a point is reached where evaluation of the iterator is necessary. To do this efficiently, Coconut provides the `consume` function, which will fully exhaust the iterator given to it.

##### Example

**Coconut:**
```coconut
range(10) |> map$((x) -> x**2) |> map$(print) |> consume
```

**Python:**
```coconut_python
collections.deque(map(print, map(lambda x: x**2, range(10))), maxlen=0)
```

### `count`

Coconut provides a modified version of `itertools.count` that supports `in`, normal slicing, optimized iterator slicing, `count` and `index` sequence methods, `repr`, and `_start` and `_step` attributes as a built-in under the name `count`.

##### Python Docs

**count**(_start=0, step=1_)

Make an iterator that returns evenly spaced values starting with number _start_. Often used as an argument to `map()` to generate consecutive data points. Also, used with `zip()` to add sequence numbers. Roughly equivalent to:
```coconut_python
def count(start=0, step=1):
    # count(10) --> 10 11 12 13 14 ...
    # count(2.5, 0.5) -> 2.5 3.0 3.5 ...
    n = start
    while True:
        yield n
        n += step
```

##### Example

**Coconut:**
```coconut
count()$[10**100] |> print
```

**Python:**
_Can't be done quickly without Coconut's iterator slicing, which requires many complicated pieces. The necessary definitions in Python can be found in the Coconut header._

### `datamaker`

Coconut provides the `datamaker` function to allow direct access to the base constructor of data types created with the Coconut `data` statement. This is particularly useful when writing alternative constructors for data types by overwriting `__new__`.

For `data` objects, `datamaker` always returns a constructor that always behaves as the underlying data type's original constructor, exactly as the data type was declared.

For non-`data` objects, equivalent to:
```coconut
def datamaker(data_type):
    """Returns base data constructor of data_type."""
    return super(data_type, data_type).__new__$(data_type)
```

##### Example

**Coconut:**
```coconut
data Tuple(elems):
    def __new__(cls, *elems):
        return elems |> datamaker(cls)
```

**Python:**
```coconut_python
import collections
class Tuple(collections.namedtuple("Tuple", "elems")):
    __slots__ = ()
    def __new__(cls, *elems):
        return super(cls, cls).__new__(cls, elems)
```

### `fmap`

In functional programming, `fmap(func, obj)` takes a data type `obj` and returns a new data type with `func` mapped over the contents. Coconut's `fmap` function does the exact same thing in Coconut.

`fmap` can also be used on built-ins such as `str`, `list`, `set`, and `dict` as a variant of `map` that returns back an object of the same type. The behavior of `fmap` for a given object can be overridden by defining an `__fmap__(self, func)` method that will be called whenever `fmap` is invoked on that object.

##### Example

**Coconut:**
```coconut
[1, 2, 3] |> fmap$(x -> x+1) == [2, 3, 4]

data Nothing()
data Just(n)

Just(3) |> fmap$(x -> x*2) == Just(6)
Nothing() |> fmap$(x -> x*2) == Nothing()
```

**Python:**
```coconut_python
list(map(lambda x: x+1, [1, 2, 3])) == [2, 3, 4]

import collections
class Nothing(collections.namedtuple("Nothing", "")):
    __slots__ = ()
class Just(collections.namedtuple("Just", "n")):
    __slots__ = ()

Just(*map(lambda x: x*2, Just(3))) == Just(6)
Nothing(*map(lambda x: x*2, Nothing())) == Nothing()
```

### `recursive_iterator`

Coconut provides a `recursive_iterator` decorator that provides significant optimizations for any stateless, recursive function that returns an iterator. To use `recursive_iterator` on a function, it must meet the following criteria:

1. your function either always `return`s an iterator or generates an iterator using `yield`,
2. when called multiple times with the same arguments, your function produces the same iterator (your function is stateless),
3. your function gets called (usually calls itself) multiple times with the same arguments, and
4. all arguments passed to your function have a unique pickling (usually true for most arguments).

If you are encountering a `RuntimeError` due to maximum recursion depth, it is highly recommended that you rewrite your function to meet either the criteria above for `recursive_iterator`, or the corresponding criteria for Coconut's [tail call optimization](#tail-call-optimization), either of which should prevent such errors.

Furthermore, `recursive_iterator` also allows the resolution of a [nasty segmentation fault in Python's iterator logic that has never been fixed](http://bugs.python.org/issue14010). Specifically, instead of writing
```coconut
seq = get_elem() :: seq
```
which will crash due to the aforementioned Python issue, write
```coconut
@recursive_iterator
def seq() = get_elem() :: seq()
```
which will work just fine.

##### Example

**Coconut:**
```coconut
@recursive_iterator
def fib() = (1, 1) :: map((+), fib(), fib()$[1:])
```

**Python:**
_Can't be done without a long decorator definition. The full definition of the decorator in Python can be found in the Coconut header._

### `parallel_map`

Coconut provides a parallel version of `map` under the name `parallel_map`. `parallel_map` makes use of multiple processes, and is therefore much faster than `map` for CPU-bound tasks. Use of `parallel_map` requires `concurrent.futures`, which exists in the Python 3 standard library, but under Python 2 will require `pip install futures` to function.

Because `parallel_map` uses multiple processes for its execution, it is necessary that all of its arguments be pickleable. Only objects defined at the module level, and not lambdas, objects defined inside of a function, or objects defined inside of the interpreter, are pickleable. Furthermore, on Windows, it is necessary that all calls to `parallel_map` occur inside of an `if __name__ == "__main__"` guard.

##### Python Docs

**parallel_map**(_func, \*iterables_)

Equivalent to `map(func, *iterables)` except _func_ is executed asynchronously and several calls to _func_ may be made concurrently. If a call raises an exception, then that exception will be raised when its value is retrieved from the iterator.

##### Example

**Coconut:**
```coconut
parallel_map(pow$(2), range(100)) |> list |> print
```

**Python:**
```coconut_python
import functools
import concurrent.futures
with concurrent.futures.ProcessPoolExecutor() as executor:
    print(list(executor.map(functools.partial(pow, 2), range(100))))
```

### `concurrent_map`

Coconut provides a concurrent version of `map` under the name `concurrent_map`. `concurrent_map` makes use of multiple threads, and is therefore much faster than `map` for IO-bound tasks. Use of `concurrent_map` requires `concurrent.futures`, which exists in the Python 3 standard library, but under Python 2 will require `pip install futures` to function.

##### Python Docs

**concurrent_map**(_func, \*iterables_)

Equivalent to `map(func, *iterables)` except _func_ is executed asynchronously and several calls to _func_ may be made concurrently. If a call raises an exception, then that exception will be raised when its value is retrieved from the iterator.

##### Example

**Coconut:**
```coconut
concurrent_map(get_data_for_user, get_all_users()) |> list |> print
```

**Python:**
```coconut_python
import functools
import concurrent.futures
with concurrent.futures.ThreadPoolExecutor() as executor:
    print(list(executor.map(get_data_for_user, get_all_users())))
```

### `MatchError`

A `MatchError` is raised when a [destructuring assignment](#destructuring-assignment) statement fails, and thus `MatchError` is provided as a built-in for catching those errors. `MatchError` objects support two attributes, `pattern`, which is a string describing the failed pattern, and `value`, which is the object that failed to match that pattern.

## Coconut Modules

### `coconut.__coconut__`

It is sometimes useful to be able to access Coconut built-ins from pure Python. To accomplish this, Coconut provides `coconut.__coconut__`, which behaves exactly like the `__coconut__.py` header file included when Coconut is compiled in package mode.

All Coconut built-ins are accessible from `coconut.__coconut__`. The recommended way to import them is to use `from coconut.__coconut__ import` and import whatever built-ins you'll be using. For example:
```coconut_python
from coconut.__coconut__ import parallel_map
```

### `coconut.convenience`

It is sometimes useful to be able to use the Coconut compiler from code, instead of from the command line. The recommended way to do this is to use `from coconut.convenience import` and import whatever convenience functions you'll be using. Specifications of the different convenience functions are as follows.

#### `parse`

**coconut.convenience.parse**(_code,_ **[**_mode_**]**)

Likely the most useful of the convenience functions, `parse` takes Coconut code as input and outputs the equivalent compiled Python code. The second argument, _mode_, is used to indicate the context for the parsing.

Each _mode_ has two components: what parser it uses, and what header it prepends. The parser determines what Coconut code is allowed as input, and the header determines how the compiled Python can be used. Possible values of _mode_ are:

- `"sys"`: (the default)
    + parser: file
        * The file parser can parse any Coconut code.
    + header: sys
        * This header imports `coconut.__coconut__` to access the necessary Coconut objects.
- `"exec"`:
    + parser: file
    + header: exec
        * When passed to `exec` at the global level, this header will create all the necessary Coconut objects itself instead of importing them.
- `"file"`:
    + parser: file
    + header: file
        * This header is meant to be written to a `--standalone` file and should not be passed to `exec`.
- `"package"`:
    + parser: file
    + header: package
        * This header is meant to be written to a `--package` file and should not be passed to `exec`.
- `"block"`:
    + parser: file
    + header: none
        * No header is included, thus this can only be passed to `exec` if code with a header has already been executed at the global level.
- `"single"`:
    + parser: single
        * Can only parse one line of Coconut code.
    + header: none
- `"eval"`:
    + parser: eval
        * Can only parse a Coconut expression, not a statement.
    + header: none
- `"debug"`:
    + parser: debug
        * Can parse any Coconut code and allows leading whitespace.
    + header: none

#### `setup`

**coconut.convenience.setup**(_target, strict, minify, line\_numbers, keep\_lines, no\_tco_**)**

`setup` can be used to pass command line flags for use in `parse`. The possible values for each flag argument are:

- _target_: `None` (default), or any [allowable target](#allowable-targets)
- _strict_: `False` (default) or `True`
- _minify_: `False` (default) or `True`
- _line\_numbers_: `False` (default) or `True`
- _keep\_lines_: `False` (default) or `True`
- _no\_tco_: `False` (default) or `True`

#### `cmd`

**coconut.convenience.cmd**(_args_, **[**_interact_**]**)

Executes the given _args_ as if they were fed to `coconut` on the command-line, with the exception that unless _interact_ is true or `-i` is passed, the interpreter will not be started. Additionally, since `parse` and `cmd` share the same convenience parsing object, any changes made to the parsing with `cmd` will work just as if they were made with `setup`.

#### `version`

**coconut.convenience.version**(**[**_which_**]**)

Retrieves a string containing information about the Coconut version. The optional argument _which_ is the type of version information desired. Possible values of _which_ are:

- `"num"`: the numerical version (the default)
- `"name"`: the version codename
- `"spec"`: the numerical version with the codename attached
- `"tag"`: the version tag used in GitHub and documentation URLs
- `"-v"`: the full string printed by `coconut -v`

#### `CoconutException`

If an error is encountered in a convenience function, a `CoconutException` instance may be raised. `coconut.convenience.CoconutException` is provided to allow catching such errors.
