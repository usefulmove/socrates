[*"the roots of lisp" paper*](~/repos/socrates/support/roots-of-lisp-jmc.pdf)

"The unusual thing about Lisp — in fact, the defining quality of Lisp — is that it can be writtin in itself."

# Seven Primitive Operators

(operator arg1 arg2 arg3 …)

## quote

(quote x) returns x

``` commonlisp
(quote x) ; => x
'x ; => x

(quote (a b c)) ; => (a b c)
```

"Code and data are made out of the same data structures, and the quote operator is the way we distinguish between them."

## atom?

returns t (true) if the argument is not a cons cell, otherwise returns nil (false).

``` commonlisp
(atom 8) ; => t
(atom ()) ; => t
(atom (list 1 2 3)) ; => nil
```

## eq?

return t (true) if the values of the arguments are the same atom or both the empty list, otherwise returns nil (false).

``` commonlisp
(eq 8 8) ; => t
(eq '() '()) ; => t
(eq 'a 'b) ; -> nil
```

## car and cdr

``` commonlisp
(car '(3 1 2)) ; => 3
(cdr '(3 1 2)) ; => (1 2)
```

## cons

``` commonlisp
(cons 3 '(1 2)) ; => (3 1 2)
(cons 'a (cons 'b (cons 'c '()))) ; => (a b c)
```

## cond

(cond (p1 e1) … (pn en)) predicate expressions (pi) in each list argument are evaluated in order until one returns t (true). when one is found, the value of the corresponding expression (ei) is returned as the value of the whole cond expression.

``` commonlisp
(cond ((eq 'a 'b) 'one)
      ((atom 'a) 'two)) ; => two
```

Note: "In five of our seven primitive operators, the arguments are always evaluated when an expression beginning with that operator is evaluated. We call an operator of that type a function (quote and cond are evaluated differently)."

# Recursive Anonymous Functions

``` commonlisp
(letrec ((fib (lambda (n)
                (cond ((< n 2) n)
                      (t (+ (funcall fib (- n 1))
                            (funcall fib (- n 2))))))))
  (funcall fib 38)) ; 39088169
```

# The Surprise

``` commonlisp
(defun eval. (e a)
  (cond ((atom e) (assoc. e a))
        ((atom (car e)) (cond ((eq (car e) 'quote) (cadr e))
                              ((eq (car e) 'atom) (atom (eval. (cadr e) a)))
                              ((eq (car e) 'eq) (eq (eval. (cadr e) a)
                                                    (eval. (caddr e) a)))
                              ((eq (car e) 'car) (car (eval. (cadr e) a)))
                              ((eq (car e) 'cdr) (cdr (eval. (cadr e) a)))
                              ((eq (car e) 'cons) (cons (eval. (cadr e) a)
                                                        (eval. (caddr e) a)))
                              ((eq (car e) 'cond) (evcon. (cdr e) a))
                              (t (eval. (cons (assoc. (car e) a)
                                              (cdr e)) a))))
        ((eq (caar e) 'label) (eval. (cons (caddar e) (cdr e))
                                     (cons (list (cadar e) (car e)) a)))
        ((eq (caar e) 'lambda) (eval. (caddar e)
                                      (append. (pair. (cadar e) (evlis. (cdr e) a))
                                               a)))))


(defun evcon. (c a)
  (cond ((eval. (caar c) a)
         (eval. (cadar c) a))
        (t (evcon. (cdr c) a))))


(defun evlis. (m a)
  (cond ((null m) '())
        (t (cons (eval. (car m) a)
                 (evlis. (cdr m) a)))))
```
