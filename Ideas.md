## Inline/Splice pattern **implemented**

The expansion result of an inline pattern is inserted in the enclosing pattern in a splicing manner.
You can define an inline pattern via `defpattern-inline`. We have now two patterns implemented, `@` and `@@`.

### @@ pattern
This is useful for repeating the same pattern for a fixed number of time.
This will save a lot of code when parsing a long list, and
also beneficial when implementing array-pattern.

```lisp
(match x
   ((list x (@@ 10 _) y) ...))
;; is equivalent to

(match x
   ((list x _ _ _ _ _ _ _ _ _ _  y) ...))
```

### @ pattern
This is an identity pattern, i.e. does nothing other than returning the given pattern.

```lisp
(match x
   ((list x (@ 10) y) ...))
;; is equivalent to

(match x
   ((list x 10 y) ...))
```

## array patterns

a must-have, I guess. It would be great if the pattern can implement LU decomposition beautifully.

## Improving Regexp Pattern

Currently trivia.ppcre is implemented in a way that cannot be merged by the optimizer. If it use ppcre:parse-string and decompose the testing code, then it is possible to merge the common portion of two regexp patterns into one. In the following example, 

```lisp
(match str
  ((ppcre "aaa([bc]*)" b-and-cs) b-and-cs)
  ((ppcre "aa(d*)" ds))
```

The tests about the first two `a`'s are redundant, and should ideally be automatically merged and checked only once.

```lisp
(match str
  ((ppcre "aa(.*)" rest)
   (match rest
     ((ppcre "a([bc]*)" b-and-cs) b-and-cs)
     ((ppcre "(d*)" ds))))
```

However, since currently the ppcre pattern is compiled directly to the call to =ppcre:scan=, this is not possible.

