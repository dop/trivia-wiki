# Inline/Splice pattern (cf. https://github.com/m2ym/optima/issues/103)

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

Please consider gracefully. if approved, I will write one.
