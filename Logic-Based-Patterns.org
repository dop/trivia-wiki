** And, Or pattern

+ Syntax :: and &rest /subpattterns/
            
            or  &rest /subpattterns/

They matches when all/some of the subpatterns matches against the element.
For example,

#+BEGIN_SRC lisp
(match x
  ((or (list 1 a)
       (cons a 3))
   a))
#+END_SRC

matches against both =(1 2)= and =(4 . 3)= and returns 2 and
4, respectively. Also,

#+BEGIN_SRC lisp
(match x
  ((and (list 1 _)
        (list _ 2))
   t))
#+END_SRC

is same as below.

#+BEGIN_SRC lisp
(match x
  ((list 1 2)
   t))
#+END_SRC
** Not pattern
+ Syntax :: not /subpattern/

It does not match when /subpattern/ matches. The variables used in
/subpattern/ is not visible in the body. 

** Guard pattern

+ Syntax :: guard /subpattern1/ /test-form/ {/generator-form/ /subpattern2/}*
+ /test-form/ :: a predicate form, evaluated.
+ /generator-form/ :: a form that produce a value, which are then matched against
     the next /subpattern2/.

The object is first matched against /subpattern1/. If that fails, whole
clause declines the matching. Otherwize, /test-form/ is evaluated. When the
result is true, then each of /generator-form/ is evaluated and matched
against corresponding /subpattern2/.

*Example*:

#+BEGIN_SRC lisp
(match (list 2 5)
  ((guard (list x y)     ; subpattern
          (= 10 (* x y)) ; test-form
          (- x y) (satisfies evenp)) ; generator1, subpattern1
   t))
;; --> nil, since (- x y) == 3 does not satisfies evenp
#+END_SRC

