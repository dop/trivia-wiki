In this page, we describe the known differences between Trivia and Optima.

## Extended Semantics of Constant patterns

In Optima, constant patterns are defined as follows:

```
A constant-pattern matches the constant itself.

Examples:
(match 1 (1 2)) => 2
(match "foo" ("foo" "bar")) => "bar"
(match '(1) ('(1) 2)) => 2
```

And [the tests only defines such cases that all elements are immediate literals such as characters, keywords and numbers.](https://github.com/m2ym/optima/blob/master/test/suite.lisp#L43)

We took a rather radical interpretation of this specification, allowing the symbols in the structural constants (arrays, structs) to be parsed as patterns. For example,

```lisp
(match #(0 1 2)
  (#(_ b 2) b))
;; -> 1

(match #2A((0 1 2) (3 4 5))
  (#2A((_ b 2) (_ _ f)) (+ b f)))
;; 1+5 -> 6

(match #S(person :name "bob")
  (#S(person :name name) name))
;; -> "bob"
```

The reason of this "patterns by default" strategy is that these readmacros (for vectors etc.) do not have quasiquotes/unquote (not even with fare-quasiquote). These literal patterns are converted to `vector`, `array` or `structure` patterns. To match a symbol itself, use `quote` such as `'_`.

Rather unintentionally, the same rule applies to the quoted list (2017/11/3). So, a pattern `'(a)` binds a variable `a`:

```lisp
(match '(1)
  ('(a) a))
;; -> 1
```

This is again undefined in optima's test cases, so this is still acceptable as an extension. However, I am currently in doubt of its utility, given the interaction to the contrib package `trivia.quasiquote`. In fact, this may be rather unintuitive (an example for parsing [the STRIPS PDDL format](https://helios.hud.ac.uk/scommv/IPC-14/repository/kovacs-pddl-3.1-2011.pdf)):

```lisp
;; checks if FORM is of certain form
(match form
  (`(forall ,args (when (and ,condition) (increase (total-cost) ,_)))
   ...))
```

This `quasiquote`-based pattern checks if the first element is `forall` and so on, but when it comes to `(total-cost)` it expands to a standard quote because there are no commas inside `(total-cost)`. This makes `(total-cost)` a pattern that is equivalent to `(list total-cost)` rather than `(list 'total-cost)`, and eventually the `eq`-check against `'total-cost` is not performed.

I am currently considering the removal of quoted cons from this extension, or a way to circumvent it by making `quasiquote` recognize the quoted literal. (2017/11/3)

## Assoc and Property Patterns

The first argument to assoc and property patterns are evaluated in trivia, while quoted in optima.

```cl
(trivia:match '((foo . 1))
  ((assoc foo val) (print val)))  ;; undefined variable FOO
(trivia:match '((foo . 1))
  ((assoc 'foo val) (print val))) ;; -> 1
(optima:match '((foo . 1))
  ((assoc foo val) (print val)))  ;; -> 1
(optima:match '((foo . 1))
  ((assoc 'foo val) (print val))) ;; does not match: 'FOO versus '(QUOTE FOO) == ''foo
```

Assoc pattern also do **not** match against an improper association list while they do on Optima.
Optima skips some contents that are not cons cells, while Trivia strictly follows the behavior as defined by ANSI spec, as it just calls the `assoc` function.

```cl
(trivia:match '(2 (:foo . 1))
  ((assoc :foo val) (print val))) ;; -> NIL, do not match
(optima:match '(2 (:foo . 1))
  ((assoc :foo val) (print val)))  ;; -> ignores 2 and matches against (:foo . 1)
```

See also: [Assoc, Property, Alist, Plist Pattern](https://github.com/guicho271828/trivia/wiki/Type-Based-Destructuring-Patterns#assoc-property-alist-plist-pattern)

## Infix when/unless Notation

This is only briefly introduced in [the REAMDE](https://github.com/m2ym/optima#macro-match) of Optima.
They come from [fare-matcher](http://www.cliki.net/fare-matcher). Since it introduces additional complexity (infix operators!) we are currently not supporting them.

## Macro `fail` Moved to Package `trivia.fail`

See [here](https://github.com/guicho271828/trivia/wiki/NEXT-FAIL-SKIP-macro).


## Meaning of "compatibility" : *Unit Tests form a specification*

Our interpretation of Trivia's compatibility to Optima can be summarized in the title. "If there is a test case in Optima, it defines the behavior of Trivia". The contents in this page are only those materials that do not follow this policy: The features which are tested in Optima but the corresponding tests are changed/removed in Trivia.

Notice that this compatibility criterion implies that the features not appearing in the unit tests of Optima do not have to be implemented in trivia, **even though they are in Optima's README**. The reasoning behind this policy is as follows:  Since it is not tested, there is no guarantee that the feature still work in the current or the future version of Optima. These "missing" features are not counted as incompatibility.

Still, it does not mean the feature is *never* implemented in Trivia: It is optional to us. For example, as discussed in #51, `otherwise` patterns (equivalent to _ pattern) are not tested in Optima (thus the actual behavior of this feature in Optima is *undefined*) but we implemented it as a result of request from @dfmorrison, which is a good thing because we add test cases and they can be backported to Optima.

