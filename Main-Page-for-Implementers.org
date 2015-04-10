* Introduction

Pattern matching is a powerful syntactic construct widely accepted in the
community of ML languages. It abstracts object destructuring, algebraic
data types, and conditional expression at once. While most pattern matching
macros in Common Lisp are based on interpreter and slow, recently
[[https://github.com/m2ym/optima][Optima]] has gain popularity largely due to its reasonable set of patterns
covering the most usecases, extensible interface using =defpattern=, and also
the speed, achieved by applying the sophisticated compilation methods in
(Le Fessant et. al. '00).

However, any library has its own problem.  To avoid the dependency as much
as possible, currently, Optima itself is implemented in an imperative
style. Given its goal is to ease functional programming, this is an
irony. As a result, Core algorithm and optimization are intermixed into a
large conglomerate, and how the optimization work is not visually intuitive
(in a source-code basis). 

Also, even with the extensibility provided by `defpattern`, it is still
difficult to add a pattern that cannot be derived from the original
patterns, such as a regular-expression binding pattern in =:optima.ppcre= .
Such a pattern should be implemented using an internal, non-exported
structures and methods.

[1] Optimizing Pattern Matching by Fabrice Le Fessant, Luc Maranget

* Trivia : Pattern Matcher with Multi-Layered Architecture

In light of this, I designed a compatible alternative to Optima with a
better architecture, called Trivia.

Optima is written in an imperative style with lots of loop macros. Pattern
matcher eases the effort of writing a recursive algorithm, but in fact, writing Optima with a pattern matcher requires Optima itself, or need some other pattern matching library which may not be compatible with Optima.  One way to avoid this
dependency is to include a primitive pattern matcher for
bootstrapping itself.

The source code of Trivia is divided into 3 levels, level 0, 1 and 2, where
the lower level bootstraps the higher level. Each level has its own =match=
macro, e.g., level 0 has =match0= and so on.  In detail, each level handles
the following functionality:

+ level 0 : list destructuring :: =match0= is a simple wrapper over
     destructuring-bind, which supports list, list*, variable, =_=, and
     constant patterns for s-exp parsing.
+ level 1 : core patterns :: =match1= supports =or1= and =guard1= patterns
     only. These are the minimal set of patterns required to
     implement a matcher. =match1= simply expands into nested =let= and
     =if= forms.
+ level 2a : pattern expander :: Provides several derived patterns over the
     level 1 patterns. All [[https://github.com/m2ym/optima][primitive, nonmodifiable pattern language in Optima]] are now implemented as a set of derived patterns in [[https://github.com/guicho271828/trivia/blob/master/level2/derived.lisp][level2/derived.lisp]].
+ level 2b : optimizer :: After the expansion, resulting level1 patterns
     are post-processed by a pattern optimizer function. Level 2 provides
     an extensible user interface to switch between different optimizer.

Thanks to the canonical expression of level 1 pattern, optimizer functions can be very simple, and easy to implement. In fact, now Trivia comes with 2 optimizers:

+ =:trivial= optimizer :: runs no optimization, and returns the pattern as
     it is. Interestingly, experiments suggests the gap between Optima and
     =:trivial= is minimal, and not so slow. Trivially correct and reliable.
+ =:balland2006= optimizer :: Implements /Fusion/ , /Intersection/ and
     /Swapping/ optimization in (Balland 2006). Currently [[https://github.com/guicho271828/trivia.balland2006][hosted in a
     separate repository]].  Preliminary results suggest that it achieves [[https://github.com/guicho271828/trivia/wiki/Benchmarking-Results][overall speedup up to *80x* ]]. All
     tests, *copied from optima*, are passing, so the correctness of the implementation is somewhat guaranteed. However, there may be a hidden bug compared to
     =:trivial=. Writing a "correct" algorithm is hard.

