Trivia maintains a full compatibility with Optima. I briefly summarize what kind of pattern exists in these matchers.

*** <list,list*,vector> &rest subpatterns

All these patterns takes multiple subpatterns and checks the type, its length and matches the contents to the subpatterns. Like =list= checks its contents (as shown in the above example), =vector= checks its length and contents. =list*= checks the elements against the subpatterns but last, and also checks the =nthcdr= of the elements against the last pattern.

*** (cons cdr car)

=cons= checks its subpatterns =car= and =cdr= if the object is of type =cons=.

*** <and, or> &rest subpattterns

They matches when all/some of the subpatterns matches against the element.
For example,

#+BEGIN_SRC lisp
(match x
  ((or (list 1 a)
       (cons a 3))
   a))
#+END_SRC

matches against both ='(1 2)= and ='(4 . 3)= and returns 2 and
4, respectively. Also,

#+BEGIN_SRC lisp
(match x
  ((and (list 1 _)
        (cons _ 2))
   t))
#+END_SRC

is same as below.

#+BEGIN_SRC lisp
(match x
  ((list 1 2)
   t))
#+END_SRC


