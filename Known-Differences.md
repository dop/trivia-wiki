In this page, we describe the known differences between Trivia and Optima.

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

Assoc pattern also do **not** match against improper association list while they do on Optima.

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

