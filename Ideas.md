## Inline/Splice pattern 

(cf. https://github.com/m2ym/optima/issues/103)

Inline pattens are useful in general, not only for lists.
consider the case below, assuming we have a pattern called `inline`.

```lisp
(defpattern skip (n)
   `(inline ,@(mapcar (constantly '_) (iota n))) ;; produces (inline _ _ _) for (skip 3)

(match x
   ((list x (skip 10) y) ...))

(match x
   ((list x _ _ _ _ _ _ _ _ _ _ y) ...))
```

This will save a lot of code when parsing a long list.
also beneficial when implementing array-pattern.

## array patterns

a must-have, I guess. It would be great if the pattern can implement LU decomposition beautifully.

## Improving Regexp Pattern

Currently trivia.ppcre is implemented in a way that cannot be merged by the optimizer. If it use ppcre:parse-string and decompose the testing code, then it is possible to merge the common portion of two regexp patterns into one. In the following example, 

```lisp
(match str
  ((ppcre "aaa([bc]*)" b-and-cs) b-and-cs)
  ((ppcre "aa(d*)" ds))
```

The tests about the first two =a='s are redundant, and should ideally be automatically merged and checked only once.

```lisp
(match str
  ((ppcre "aa(.*)" rest)
   (match rest
     ((ppcre "a([bc]*)" b-and-cs) b-and-cs)
     ((ppcre "(d*)" ds))))
```

However, since currently the ppcre pattern is compiled directly to the call to =ppcre:scan=, this is not possible.

