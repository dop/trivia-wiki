
** LAST pattern

=last= pattern is a wrapper around =CL:LAST=.
Matches against a list, and matches subpatterns against N last elements obtained by =CL:LAST=.

+ Syntax :: last /subpattern/ &optional /(n 1)/

#+BEGIN_SRC lisp
(match '(:a :b :c)
  ((last (list b _) 2)
    b))
;; -> :b
#+END_SRC

** READ pattern

+ Syntax :: read /subpattern/

Useful for simple parsing.

The current matching object should be a =string=.
=subpattern= is matched against the result of reading the string by =read-from-string=.
=end-of-file= and =parse-error= are ignored and the matching will be successful, where
=subpattern= is matched against NIL.

