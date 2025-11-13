``` commonlisp
(load-file "~/repos/cora/src/cora.el") ; load Cora library
(setq lexical-binding t)
```

# Lesson One

If you think to use a \`progn\` binding (\`begin\` in Racket Scheme), instead use a \`let\` binding (esp. if defining a local variable binding would be helpful for clarity). The holy grail.

``` commonlisp
(progn
  (blink-cursor-mode 0)
  (setq evil-normal-state-cursor '(box "#00c0ff")))

(let ()
  (blink-cursor-mode 1)
  (setq evil-normal-state-cursor '(box "#fff670")))

;; forms
(let ()
  (sexp)
  (sexp))

(let ((a (sexp))
      (b (sexp)))
  (sexp)
  (sexp))
```

A \`let\` binding evaluates each of its (S-expression) arguments after binding a series of of symbol definitions. The "empty let" contains no bindings and simply evaluates each of its arguments the way \`begin\` does in Scheme.

``` racket
(define (begin . args)
  (cond ((null? args) #<void>) ; return a void value for an empty begin.
        ((null? (cdr args)) (car args)) ; if last expression, return its value.
        (else (car args) ; evaluate the first expression for its side-effects.
              (apply begin (cdr args))))) ; recursively evaluate the rest.
```

## Define the relationship between \`let\` \`lambda\` and \`begin\` in Scheme?

``` commonlisp
;; begin is a let with no bindings.
(begin
  (sexp)
  (sexp))

(let ()
  (sexp)
  (sexp))

(lambda ()
  (sexp)
  (sexp))


;; a let is an immediately-invoked (lambda) function expression (iife).
(let ((a (sexp-a))
      (b (sexp-b)))
  (sexp)
  (sexp))

((lambda (a b)
  (sexp)
  (sexp))
 (sexp-a)
 (sexp-b))
```

``` racket
(let ((a 2)
      (b 8))
  (* a b))

((lambda (a b) (* a b)) ; self-evaluating lambda form.
   2 8)
```

``` racket
(begin
  (sexp)
  (sexp))

((lambda () ; self-evaluating lambda form
  (sexp)
  (sexp)))

(let ()
  (sexp)
  (sexp))


;; let is an immediately-invoked lambda function expression (iife).
(let ((a (sexp-a))
      (b (sexp-b)))
  (sexp)
  (sexp))

((lambda (a b)
  (sexp)
  (sexp))
 (sexp-a)
 (sexp-b))
```

The let expression is preferred over the iife lambda expression for expressiveness and readability, but there are times when the opposite is true.

``` commonlisp
(defun test-enc-round-trip (error-prelude)
  ((lambda (f s)
     (when (not (equal s (funcall f s)))
       (error (concat
                error-prelude
                "error: round-trip encryption test(s) failed"))))

   (lambda (s) ; lambda :: string -> string
     (let ((key 313))
       (join-chars (enc-encrypt-chars (- key) (enc-encrypt-chars key (string-to-list s))))))

   "lorem ipsum dolor sit amet, consectetur adipiscing elit"))

; vs

(defun test-enc-round-trip (error-prelude)
  (let ((f (lambda (s)
            (let ((key 313))
              (join-chars (enc-encrypt-chars (- key) (enc-encrypt-chars key (string-to-list s)))))))
        (s "lorem ipsum dolor sit amet, consectetur adipiscing elit"))
    (when (not (equal s (funcall f s)))
      (error (concat
               error-prelude
               "error: round-trip encryption test(s) failed")))))
```

( Lisps help me "see" in code (forms). )

``` commonlisp
(define begin (sexps)
  (mapcar 'eval sexps))
```

# Lesson Two

In general, prefer the the more general \`cond\` form to the narrower \`if\`, unless clarity is clearly better.

``` racket
(define (begin . args)
  (if (null? args)
      #<void> ; return a void value for an empty begin.
      (if (null? (cdr args))
          (car args) ; if last expression, return its value.
          (begin
            (car args) ; evaluate the first expression for its side-effects.
            (apply begin (cdr args)))))) ; recursively evaluate the rest.

(define (begin . args)
  (cond ((null? args) #<void>) ; return a void value for an empty begin.
        ((null? (cdr args)) (car args)) ; if last expression, return its value.
        (else (car args) ; evaluate the first expression for its side-effects.
              (apply begin (cdr args))))) ; recursively evaluate the rest.
```

# Lesson Three

Ponder the two choices below, let versus self-evaluating lambda. The self-executing lambda form is more obviously (and aesthetically) a functional programming form. It's elegant, if pedantic.

``` commonlisp
((lambda (base cap)
   (cond ((or (< ord base)
              (> ord (- cap 1))) ord) ; only modify characters in range
         (t (+ base (mod (+ (- ord base) encryption-key)
                         (- cap base))))))
 32 127)

; vs

(let* ((base 32)
       (cap 127)
       (range (- cap base)))
  (cond ((or (< ord base)
             (> ord (- cap 1))) ord) ; only modify characters in range
        (t (+ base (mod (+ (- ord base) encryption-key)
                        range)))))
```

# Lesson Four

ascii conversion

``` commonlisp
;; character(s) to string
(string ?a) ; => "a"
(string ?0 ?3 ?1 ?2) ; => "0312"

;; string to characers
(string-to-list "0312") => ; (48 51 49 50)

;; characters are integers
(= ?c 99)
```

# Lesson Five

Guards

``` python
def f()
  if condition: return

  otherstuff
```

``` commonlisp
(lambda ()
  (if condition ()
      otherstuff))
```

# Lesson Six

Macros

``` commonlisp
(defmacro for-comp (sym lst exp)
  (list 'mapcar (list 'lambda (list sym) exp) lst))
;(mapcar (lambda (a) (* a a)) '(3 1 2))

(defmacro for-comp (sym lst exp) ; using quasi-quotes
  `(mapcar (lambda (,sym) ,exp) ,lst))

; prototype ("for comprehension")
(for-comp a '(3 1 2) (* a a)) ; => (9 1 4)
```

``` commonlisp
(defmacro _l (sym exp)
  `(lambda ,sym ,exp))

; prototype
(mapcar (_l (a) (* a a a)) '(3 1 2)) ; (27 1 8)
```

# Lesson Seven

An "invalid function" error in Emacs Lisp (if you are coming from other Lisps) often means you haven't used \`funcall\` (which is unnecessary in many Lisp dialects).

# Lesson Eight

Recursive Anonymous Functions

``` commonlisp
(letrec ((fib (lambda (n)
                (cond ((< n 2) n)
                      (t (+ (funcall fib (- n 1))
                            (funcall fib (- n 2))))))))
  (funcall fib 38)) ; 39088169
```

# Lesson Nine

Optional Function Arguments

``` commonlisp
(defun test (&rest args)
  (+ (car args)
     (cadr args)
     (if (= 2 (length args))
         10
         (caddr args))))

(defun test (a b &optional c)
  (unless c (setq c 10))
  (+ a b c))

(test 1 2 3) ; => 6
(test 1 2) ; => 13
```

# Lesson Ten

Hash tables

``` commonlisp
; create empty hash table
(setq hash (make-hash-table :test 'equal))

; add key-value pairs
(puthash 'a 2 hash)
(puthash 'b 8 hash)
(puthash '2 20 hash)

; retrieve value
(gethash 'a hash) ; => 2
(gethash 2 hash) ; => 20
```

# Lesson Eleven

Structs

``` commonlisp
(require 'cl-lib)

(cl-defstruct person
  age
  male?
  height
  weight)

(setq bob (make-person
            :age 30
            :male? t
            :height 58
            :weight 205))

(person-weight bob)
```

# Lesson Twelve

Sorting

``` commonlisp
(setq lst '(3 1 2 0 5 4))
(sort (copy-sequence lst) '<=) ; (0 1 2 3 4 5)
```

``` commonlisp
(setq counts (tally '(3 1 2 5 4 8 3 1 5 5))) ; ((8 . 1) (4 . 1) (5 . 3) (2 . 1) (1 . 2) (3 . 2))

(sort ; one-layer sort (by second element in pair)
  (copy-sequence counts)
  (lambda (pair1 pair2)
    (> (cdr pair1)
       (cdr pair2)))) ; ((5 . 3) (1 . 2) (3 . 2) (8 . 1) (4 . 1) (2 . 1))

(sort ; dual-layer sort (by second element then by first)
  (copy-sequence counts)
  (lambda (pair-a pair-b)
    (if (not= (cdr pair-a) (cdr pair-b))
        (> (cdr pair-a) (cdr pair-b))
        (> (car pair-a) (car pair-b))))) ; ((5 . 3) (3 . 2) (1 . 2) (8 . 1) (4 . 1) (2 . 1))
```

# Lesson Thirteen

Threading Macro for Function Composition

``` commonlisp
(thread 5
  'sqrt
  (\ (a) (- a 1))
  (\ (a) (/ a 2))) ; 0.6180339887498949
```

# Lesson Fourteen

Closures and Lexical Scoping

``` commonlisp
(setq lexical-binding t)
(load-file "~/repos/cora/src/cora.el")


(defun make-counter (start)
  (let ((count start)) ; lisp closures are beautiful
    (lambda ()
      (setq count (inc count))
      count)))


(fset 'cntr (make-counter 0))

(cntr) ; 1
(cntr) ; 2
(cntr) ; 3

(fset'cntr2 (make-counter 200))

(cntr2) ; 201
(cntr2) ; 202
(cntr) ; 4
(cntr2) ; 203
```

Closure as Container

``` commonlisp
(setq lexical-binding t)

(defun make-task (s)
  (lambda (&rest args)
    (cond ((equal? :show (car args)) (concat "task: " s))
          ((equal? :rev (car args)) (reverse s))
          ((equal? :update (car args)) (setq s (cadr args)))
          (t "unknown command"))))

(fset 'test-task (make-task "this is a test"))

(test-task :show) ; task: this is a test
(test-task :rev) ; tset a si siht
(test-task :update "this is an updated test")
(test-task :show) ; task: this is an updated test
```

# Lesson Fifteen

Named Let

``` commonlisp
(setq lexical-binding t)

(named-let fib ((n 10))
  (if (< n 2) n
      (+ (fib (- n 1))
         (fib (- n 2))))) ; 55
```

# Lesson Sixteen

Luhn algorithm for credit card validation

``` commonlisp
(defun valid-cc? (s)
  (let ((diminish (\ (n)
                    (if (< n 10) n
                        (diminish (foldl
                                    (\ (acc c) (+ acc (char-to-int c)))
                                    0
                                    (string-to-list (number-to-string n))))))))
    (= 0 (thread s
           'string-to-list
           (\ (lst) (map (\ (c) (- c ?0)) lst))
           (\ (lst) (foldl
                      (\ (acc pair)
                        (let ((i (car pair))
                              (n (cdr pair)))
                          (+ acc (diminish (if (even? i) (* n 2) n)))))
                      0
                      (enumerate lst)))
           (\ (n) (mod (- 10 (mod n 10)) 10))))))
```
