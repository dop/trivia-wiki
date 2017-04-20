
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

