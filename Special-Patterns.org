
This section contains patterns that has specific roles in itself.

** Place Pattern

+ Syntax :: place /subpattern/

The subpattern is accessed by =symbol-macrolet= instead of =let=.

*Example*:

#+BEGIN_SRC lisp
(defvar *x* (list 0 1))
(match *x*
  ((list (place x) y)
   (setf x :success)
   (setf y :fail)))

(print *x*)
;; --> (:SUCCESS 1)
#+END_SRC

** Bind Pattern

+ Syntax :: <> /pattern/ /value/ &optional /var/

The current matching value is bound to =var=.
The result of evaluating =value= using =var= is then matched against =pattern=.
=var= is optional and can be omitted when =value= is a constant and does not need the current matching value.

This is important when you write a =defpattern= that has a default
value. Consider writing a pattern that matches against both of ='string=
and ='(string *)= and has a subpattern =length=. length should be bound to
='*= even when the input is ='string=. With =<>= pattern, it can be
implemented as below.

#+begin_src lisp
(defpattern string-type-specifier (length)
   `(or (list 'string ,length)
        (and 'string (<> ,length '*))))
#+end_src

** Access Pattern

Just want to access an element? It's time to use =access= pattern: 

+ Syntax :: access /#'accessor/ /subpattern/
+ Syntax :: access /'accessor/ /subpattern/
+ accessor :: a function name.

The object is not checked. The value of =funcall= ing the current object is
matched against /subpattern/.

Example:

#+BEGIN_SRC lisp
(match '((1 2 (3 4)) 5 (6))
  ((access #'flatten (list* _ _ 3 _))))
#+END_SRC
