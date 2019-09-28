## Exercise 3.1 [*]

Consider the fourth example above. Then implement program-to-list so that it returns the list
(a-program
  (primapp-exp
    (incr-prim)
    ((primapp-exp
      (add-prim)
      ((lit-exp 3)
      (var-exp x))))))

<details>
<summary>Solution</summary>

```
(define-datatype program program?
                 (a-program
                   (exp expression?)))

(define-datatype expression expression?
                 (lit-exp
                   (datum number?))
                 (var-exp
                   (id symbol?))
                 (primapp-exp
                   (prim primitive?)
                   (rands (list-of expression?))))

(define-datatype primitive primitive?
                 (add-prim)
                 (substract-prim)
                 (mult-prim)
                 (incr-prim)
                 (decr-prim))

(define list-of
  (lambda (pred)
    (lambda (val)
      (or (null? val)
          (and (pair? val)
               (pred (car val))
               ((list-of pred) (cdr val)))))))

(define (program-to-list prog)
  (cases program prog
    (a-program (exp)
               (cons 'a-program
                     (list (expression-to-list))))))

(define (expression-to-list exp)
  (cases expression exp
    (lit-exp (datum) (list 'lit-exp datum))
    (var-exp (id) (list 'var-exp id))
    (primapp-exp (prim rands)
             (list 'primapp-exp
                   prim
                   (map (lambda (rand)
                          (expression-to-list rand) rands))))))
                           
   
```
</details>

## Exercise 3.2 [*]

In what order are subexpressions in a primative application evaluated? Is there a way too determine this empirically? Can the order affect the Result?

<details>
<summary>Solution</summary>

```                        
They are evaluated left-to-right

The evaluation order of subexpressions is determined by scheme

The order should not affect the result.
```
</details>

## Exercise 3.3 [*]

Write parse-program. See section 2.2.2

<details>
<summary>Solution</summary>

```                        
(define (parse-expression datum)
    (cond
      ((number? datum) (lit-exp datum))
      ((symbol? datum) (var-exp datum))
      ((pair? datum)
       (let ((primitive (parse-primitive (car exp))))
                     (primapp-exp (car primitive)
                                  (map (lambda (rand)
                                         (parse-expression rand))
                                       (cdr exp)))))
       (else (eopl:error 'parse-expression
              "Invalid concrete syntax ~s" datum))))

(define (parse-primitive prim)
  (cond ((eqv? prim '+)
         (cons (add-prim) 2))
        ((eqv? prim '-)
         (cons (subtract-prim) 2))
        ((eqv? prim '*)
         (cons (mult-prim) 2))
        ((eqv? prim 'add1)
         (cons (incr-prim) 1))
        ((eqv? prim 'sub1)
         (cons (decr-prim) 1))
        ))

(define (parse-program prog)
  (a-program (parse-expression prog)))
        
  
```
</details>

## Exercise 3.4 [*]

Test Eval-program using both run and read-eval-print loop

<details>
<summary>Solution</summary>

```                        
> (run '(add1 8))
> 9

First Test Works

>(read-eval-print)
->add1 8
9

Test 2 Works

Both tests are functional
```
</details>



## Exercise 3.5 [*]



<details>
<summary>Solution</summary>

```                        
(define (parse-primitive prim)
  (cond ((eqv? prim '+)
         (cons (add-prim) 2))
        ((eqv? prim '-)
         (cons (subtract-prim) 2))
        ((eqv? prim '*)
         (cons (mult-prim) 2))
        ((eqv? prim 'add1)
         (cons (incr-prim) 1))
        ((eqv? prim 'sub1)
         (cons (decr-prim) 1))
        ((eqv? prim 'print)
               (cons (print-prim) 1))
        ))
        
  (define apply-primitive
  (lambda (prim args)
    (cases primitive prim
      (add-prim () (+ (car args) (cadr args)))
      (subtract-prim () (- (car args) (cadr args)))
      (mult-prim () (* (car args) (cadr args)))
      (incr-prim () (+ (car args) 1))
      (decr-prim () (- (car args) 1))
      (print-prim () (1))
      )))
```
</details>

## Exercise 3.6 [*]



<details>
<summary>Solution</summary>

```                        
(define (parse-primitive prim)
  (cond ((eqv? prim '+)
         (cons (add-prim) 2))
        ((eqv? prim '-)
         (cons (subtract-prim) 2))
        ((eqv? prim '*)
         (cons (mult-prim) 2))
        ((eqv? prim 'add1)
         (cons (incr-prim) 1))
        ((eqv? prim 'sub1)
         (cons (decr-prim) 1))
        ((eqv? prim 'print)
               (cons (print-prim) 1))
        ))
        
        (define apply-primitive
  (lambda (prim args)
    (cases primitive prim
      (add-prim () (+ (car args) (cadr args)))
      (subtract-prim () (- (car args) (cadr args)))
      (mult-prim () (* (car args) (cadr args)))
      (incr-prim () (+ (car args) 1))
      (decr-prim () (- (car args) 1))
      (print-prim () (1))
      (minus-prim () (* (car args) -1))
      )))
```
</details>

## Exercise 3.7 [*]

TODO

<details>
<summary>Solution</summary>

```                        
(define (parse-primitive prim)
  (cond ((eqv? prim '+)
         (cons (add-prim) 2))
        ((eqv? prim '-)
         (cons (subtract-prim) 2))
        ((eqv? prim '*)
         (cons (mult-prim) 2))
        ((eqv? prim 'add1)
         (cons (incr-prim) 1))
        ((eqv? prim 'sub1)
         (cons (decr-prim) 1))
        ((eqv? prim 'print)
               (cons (print-prim) 1))
        ((eqv? prim 'minus)
               (cons (minus-prim) 1))
        ((eqv? prim 'list) 
               (cons (list-prim) 3)) 
        ((eqv? prim 'cons) 
               (cons (cons-prim) 2))
        ((eqv? prim 'car) 
               (cons (car-prim) 1))
        ((eqv? prim 'cdr) 
               (cons (cdr-prim) 1))
        ))
        
(define apply-primitive
  (lambda (prim args)
    (cases primitive prim
      (add-prim () (+ (car args) (cadr args)))
      (subtract-prim () (- (car args) (cadr args)))
      (mult-prim () (* (car args) (cadr args)))
      (incr-prim () (+ (car args) 1))
      (decr-prim () (- (car args) 1))
      (print-prim () (1))
      (minus-prim () (* (car args) -1))
      (list-prim () (list (car args) (list (cadr args) (caddr))))
      (car-prim () (car args))
      (cdr-prim () (cdr args))
      (cons-prim () (cons (car args) (cdr args)))
      (empty-list-prim () '())
      )))
```
</details>

## Exercise 3.8 [*]

TODO

<details>
<summary>Solution</summary>

```                        
(define (parse-primitive prim)
  (cond ((eqv? prim '+)
         (cons (add-prim) 2))
        ((eqv? prim '-)
         (cons (subtract-prim) 2))
        ((eqv? prim '*)
         (cons (mult-prim) 2))
        ((eqv? prim 'add1)
         (cons (incr-prim) 1))
        ((eqv? prim 'sub1)
         (cons (decr-prim) 1))
        ((eqv? prim 'print)
               (cons (print-prim) 1))
        ((eqv? prim 'minus)
               (cons (minus-prim) 1))
        ((eqv? prim 'list) 
               (cons (list-prim) 3)) 
        ((eqv? prim 'cons) 
               (cons (cons-prim) 2))
        ((eqv? prim 'car) 
               (cons (car-prim) 1))
        ((eqv? prim 'cdr) 
               (cons (cdr-prim) 1))
        ((eqv? prim 'setcar)
               (cons (setcar-prim) 2))
        ))
        
        
(define apply-primitive
  (lambda (prim args)
    (cases primitive prim
      (add-prim () (+ (car args) (cadr args)))
      (subtract-prim () (- (car args) (cadr args)))
      (mult-prim () (* (car args) (cadr args)))
      (incr-prim () (+ (car args) 1))
      (decr-prim () (- (car args) 1))
      (print-prim () (1))
      (minus-prim () (* (car args) -1))
      (list-prim () args)
      (car-prim () (caar args))
      (cdr-prim () (cdar args))
      (cons-prim () (cons (car args) (cdr args)))
      (empty-list-prim () '())
      (setcar-prim () (cons (car args) (cdr (cadr args))))
      )))
```
</details>

## Exercise 3.9 [*]

TODO

<details>
<summary>Solution</summary>

```
Did this one first since i figured it was easier to just start the implementation with it.
(define (parse-expression datum)
    (cond
      ((number? datum) (lit-exp datum))
      ((symbol? datum) (var-exp datum))
      ((pair? datum)
       (let ((primitive (parse-primitive (car exp))))
                 (if (not (= (cdr primitive) (length (cdr exp))))
                     (eopl:error 'parse-primitive
                                 "Incorrect number of parameters in ~s, correct number is ~s" 
                                 (car primitive) (length (cdr exp)))
                     (primapp-exp (car primitive)
                                  (map (lambda (rand)
                                         (parse-expression rand))
                                       (cdr exp))))))
       (else (eopl:error 'parse-expression
              "Invalid concrete syntax ~s" datum))))
      )))
```
</details>

## Exercise 3.10 [*]

Test Eval-program using both run and read-eval-print loop

<details>
<summary>Solution</summary>

```                        
(define eval-program
  (lambda (pgm)
    (cases program pgm
      (a-program (body)
                 (eval-expression body (init-env))))))

(define (apply-env env sym)
    ((car env) sym))

(define (extend-env syms vals env)
    (list
      (lambda (sym)
        (let ((pos (list-find-position sym syms)))
          (if (number? pos)
              (list-ref vals pos)
              (apply-env env sym))))
      (lambda (sym)
        (if (memv sym syms)
            #t
            (has-association? env sym)))))

(define empty-env
  (lambda ()
    (list
      (lambda (sym)
        (error 'apply-env "No binding for ~s" sym))
      (lambda (sym) #f))))

(define (list-find-position sym los)
    (list-index (lambda (sym1) (eqv? sym1 sym)) los))

(define (list-index pred ls)
    (cond
      ((null? ls) #f)
      ((pred (car ls)) 0)
      (else (let ((temp (list-index pred (cdr ls))))
              (if (number? temp)
                  (+ temp 1)
                  #f)))))

(define (has-association? env sym)
    ((cadr env) sym))

(define eval-expression
  (lambda (exp env)
    (cases expression exp
      (lit-exp (datum) datum)
      (var-exp (id) (apply-env env id))
      (primapp-exp (prim rands)
                   (let ((args (eval-rands rands env)))
                     (apply-primitive prim args)))
      (if-exp (test-exp true-exp false-exp)
              (if (true-value? (eval-expression test-exp env))
                  (eval-expression true-exp env)
                  (eval-expression false-exp env)))
      )))

(define eval-rands
  (lambda (rands env)
    (map (lambda (x) (eval-rand x env)) rands)))

(define eval-rand
  (lambda (rand env)
    (eval-expression rand env)))

(define apply-primitive
  (lambda (prim args)
    (cases primitive prim
      (add-prim () (+ (car args) (cadr args)))
      (subtract-prim () (- (car args) (cadr args)))
      (mult-prim () (* (car args) (cadr args)))
      (incr-prim () (+ (car args) 1))
      (decr-prim () (- (car args) 1))
      )))

(define init-env
  (lambda ()
    (extend-env
     '(i v x)
     '(1 5 10)
     (empty-env))))

(define (parse-expression exp)
        (cond ((number? exp)
               (lit-exp exp))
              ((symbol? exp)
               (var-exp exp))
              ((pair? exp)
               (cond ((eqv? (car exp) 'if)
                      (if-exp (parse-expression (cadr exp))
                              (parse-expression (caddr exp))
                              (parse-expression (cadddr exp))))
                     (else
                       (primapp-exp (parse-primitive (car exp))
                                    (map (lambda (rand)
                                           (parse-expression rand))
                                         (cdr exp))))))))

(define (parse-primitive prim)
  (cond ((eqv? prim '+)
         (add-prim))
        ((eqv? prim '-)
         (subtract-prim))
        ((eqv? prim '*)
         (mult-prim))
        ((eqv? prim 'add1)
         (incr-prim))
        ((eqv? prim 'sub1)
         (decr-prim))
        ))

(define (parse-program prog)
  (a-program (parse-expression prog)))

(define-datatype program program?
                 (a-program
                   (exp expression?)))


(define-datatype expression expression?
                 (lit-exp
                   (datum number?))
                 (var-exp
                   (id symbol?))
                 (primapp-exp
                   (prim primitive?)
                   (rands (list-of expression?)))
  (if-exp
                   (test-exp expression?)
                   (true-exp expression?)
                   (false-exp expression?))
  )

(define-datatype primitive primitive?
  (add-prim)
  (subtract-prim)
                 (mult-prim)
                 (incr-prim)
                 (decr-prim)
                
  )

(define list-of
  (lambda (pred)
    (lambda (val)
      (or (null? val)
          (and (pair? val)
               (pred (car val))
               ((list-of pred) (cdr val)))))))

(define true-value?
  (lambda (x)
    (not (zero? x))))

(define run
  (lambda (x)
    (eval-program (parse-program x))))
```
</details>

## Exercise 3.11 [*]

<details>
<summary>Solution</summary>

```                        
(define (parse-primitive prim)
  (cond ((eqv? prim '+)
         (add-prim))
        ((eqv? prim '-)
         (subtract-prim))
        ((eqv? prim '*)
         (mult-prim))
        ((eqv? prim 'add1)
         (incr-prim))
        ((eqv? prim 'sub1)
         (decr-prim))
        ((eqv? prim 'equal?)
         (equal-prim))
        ((eqv? prim 'zero?)
         (zero-prim))
        ((eqv? prim 'greater?)
         (greater-prim))
        ((eqv? prim 'less?)
         (less-prim))
        
        ))
        
        (define apply-primitive
  (lambda (prim args)
    (cases primitive prim
      (add-prim () (+ (car args) (cadr args)))
      (subtract-prim () (- (car args) (cadr args)))
      (mult-prim () (* (car args) (cadr args)))
      (incr-prim () (+ (car args) 1))
      (decr-prim () (- (car args) 1))
      (equal-prim () (if (= (car args) (cadr args)) 1 0))
      (zero-prim () (if (= (car args) 0) 1 0))
      (greater-prim () (if (> (car args) (cadr args)) 1 0))
      (less-prim () (if (< (car args) (cadr args)) 1 0))

      )))
```
</details>

## Exercise 3.12 [*]

<details>
<summary>Solution</summary>

```                        
(define (parse-primitive prim)
  (cond ((eqv? prim '+)
         (add-prim))
        ((eqv? prim '-)
         (subtract-prim))
        ((eqv? prim '*)
         (mult-prim))
        ((eqv? prim 'add1)
         (incr-prim))
        ((eqv? prim 'sub1)
         (decr-prim))
        ((eqv? prim 'equal?)
         (equal-prim))
        ((eqv? prim 'zero?)
         (zero-prim))
        ((eqv? prim 'greater?)
         (greater-prim))
        ((eqv? prim 'less?)
         (less-prim))
        ((eqv? prim 'list) 
               (list-prim)) 
        ((eqv? prim 'cons) 
               (cons-prim))
        ((eqv? prim 'car) 
               (car-prim))
        ((eqv? prim 'cdr) 
               (cdr-prim))
        ((eqv? prim 'null?)
               (null-prim))
        
        ))
        
(define apply-primitive
  (lambda (prim args)
    (cases primitive prim
      (add-prim () (+ (car args) (cadr args)))
      (subtract-prim () (- (car args) (cadr args)))
      (mult-prim () (* (car args) (cadr args)))
      (incr-prim () (+ (car args) 1))
      (decr-prim () (- (car args) 1))
      (equal-prim () (if (= (car args) (cadr args)) 1 0))
      (zero-prim () (if (= (car args) 0) 1 0))
      (greater-prim () (if (> (car args) (cadr args)) 1 0))
      (less-prim () (if (< (car args) (cadr args)) 1 0))
      (list-prim () args)
      (car-prim () (caar args))
      (cdr-prim () (cdar args))
      (cons-prim () (cons (car args) (cdr args)))
      (empty-list-prim () '())
      (null-prim () (if (null? (car args)) 1 0))
      

      )))
```
</details>

## Exercise 3.13 [*]

<details>
<summary>Solution</summary>

```                        
(sllgen:make-define-datatypes scanner-spec-3-13 grammar-3-13)

(define scanner-spec-3-13
  '((white-sp
      (whitespace) skip)
    (comment
      ("%" (arbno (not #\newline))) skip)
    (identifier
      (letter (arbno (or letter digit "?"))) symbol)
    (number
      (digit (arbno digit)) number)))

(define grammar-3-13
  '((program
      (expression)
      a-program)
    (expression
     (number)
      lit-exp)
    (expression (id)
      var-exp)
    (expression (primitive "(" (separated-list expression ",") ")")
      primapp-exp)
    (expression ("if" expression "then" expression "else" expression)
      if-exp)
    (expression ("cond" (arbno expression "==>" expression) "end")
      cond-exp)
    (primitive ("+")
      add-prim)
    (primitive ("-")
      substract-prim)
    (primitive ("*")
      mult-prim)
    (primitive ("add1")
      incr-prim)
    (primitive ("sub1")
      decr-prim)
    (primitive ("equal?")
      equal-prim)
    (primitive ("zero?")
      zero-prim)
    (primitive ("greater?")
      greater-prim)
    (primitive ("less?")
      less-prim)))

(define scan&parse
  (sllgen:make-string-parser
    scanner-spec-3-13
    grammar-3-13))



(define (run string)
    (eval-program
      (scan&parse string)))

(define list-of
  (lambda (pred)
    (lambda (val)
      (or (null? val)
          (and (pair? val)
               (pred (car val))
               ((list-of pred) (cdr val)))))))

(define eval-program
  (lambda (pgm)
    (cases program pgm
      (a-program (body)
                 (eval-expression body (init-env))))))

(define eval-expression
  (lambda (exp env)
    (cases expression exp
           (lit-exp (datum) datum)
           (var-exp (id) (apply-env env id))
           (if-exp (test-exp true-exp false-exp)
                   (if (true-value? (eval-expression test-exp env))
                       (eval-expression true-exp env)
                       (eval-expression false-exp env)))
           (cond-exp (test-exps conseq-exps)
                     (cond ((null? test-exps) 0)
                           ((true-value? (eval-expression (car test-exps) env))
                            (eval-expression (car conseq-exps) env))
                           (else (eval-expression (cond-exp (cdr test-exps)
                                                            (cdr conseq-exps))
                                                  env))))
           (primapp-exp (prim rands)
                        (let ((args (eval-rands rands env)))
                          (apply-primitive prim args))))))

(define eval-rands
  (lambda (rands env)
    (map (lambda (x) (eval-rand x env)) rands)))

(define eval-rand
  (lambda (rand env)
    (eval-expression rand env)))

(define apply-primitive
  (lambda (prim args)
    (cases primitive prim
      (add-prim () (+ (car args) (cadr args)))
      (subtract-prim () (- (car args) (cadr args)))
      (mult-prim () (* (car args) (cadr args)))
      (incr-prim () (+ (car args) 1))
      (decr-prim () (- (car args) 1))
      (equal-prim () (if (= (car args) (cadr args)) 1 0))
      (zero-prim () (if (= (car args) 0) 1 0))
      (greater-prim () (if (> (car args) (cadr args)) 1 0))
      (less-prim () (if (< (car args) (cadr args)) 1 0))
      )))

(define init-env
  (lambda ()
    (extend-env
      '(i v x)
      '(1 5 10)
      (empty-env))))
```
</details>

## Exercise 3.14 [*]

<details>
<summary>Solution</summary>

```                        
(define true
  (lambda ()
    (lambda (f s) f)))

(define false
  (lambda ()
    (lambda (f s) s)))
    
(define eval-program
  (lambda (pgm)
    (cases program pgm
      (a-program (body)
                 (eval-expression body (init-env))))))

(define eval-expression
  (lambda (exp env)
    (cases expression exp
      (lit-exp (datum) datum)
      (var-exp (id) (apply-env env id))
      (primapp-exp (prim rands)
                   (let ((args (eval-rands rands env)))
                     (apply-primitive prim args)))
      (if-exp (test-exp true-exp false-exp)
              (if (true-value? (eval-expression test-exp env))
                  (eval-expression true-exp env)
                  (eval-expression false-exp env)))
      )))

(define eval-rands
  (lambda (rands env)
    (map (lambda (x) (eval-rand x env)) rands)))

(define eval-rand
  (lambda (rand env)
    (eval-expression rand env)))

(define apply-primitive
  (lambda (prim args)
    (cases primitive prim
      (add-prim () (+ (car args) (cadr args)))
      (subtract-prim () (- (car args) (cadr args)))
      (mult-prim () (* (car args) (cadr args)))
      (incr-prim () (+ (car args) 1))
      (decr-prim () (- (car args) 1))
      (equal-prim () (boolean-value? (= (car args) (cadr args)) 1 0))
      (zero-prim () (boolean-value? (= (car args) 0) 1 0))
      (greater-prim () (boolean-value? (> (car args) (cadr args)) 1 0))
      (less-prim () (boolean-value? (< (car args) (cadr args)) 1 0))
      )))
      
(define (boolean-value? test)
    (if test (true) (false)))
    
(define init-env
  (lambda ()
    (extend-env
      '(i v x)
      '(1 5 10)
      (empty-env))))
      

```
</details>

## Exercise 3.15 [*]

<details>
<summary>Solution</summary>

```                        
(define (eval-bool-exp exp env)
    (cases bool-exp exp
           (equal-pred (args)
                       (if (= (eval-expression (car args) env)
                              (eval-expression (cadr args) env))
                           1 0))
           (zero-pred (arg)
                      (if (zero? (eval-expression arg env))
                          1 0))
           (greater-pred (args)
                         (if (> (eval-expression (car args) env)
                                (eval-expression (cadr args) env))
                             1 0))
           (less-pred (args)
                      (if (< (eval-expression (car args) env)
                             (eval-expression (cadr args) env))
                          1 0)))))

(define apply-primitive
  (lambda (prim args)
    (cases primitive prim
           (add-prim () (+ (car args) (cadr args)))
           (substract-prim () (- (car args) (cadr args)))
           (mult-prim () (* (car args) (cadr args)))
           (incr-prim () (+ (car args) 1))
           (decr-prim () (- (car args) 1)))))

(define init-env
  (lambda ()
    (extend-env
      '(i v x)
      '(1 5 10)
      (empty-env))))
```
</details>

## Exercise 3.16 [*]

<details>
<summary>Solution</summary>

```                        
(define-datatype program program?
                 (a-program
                   (exp expression?)))


(define-datatype expression expression?
                 (lit-exp
                   (datum number?))
                 (var-exp
                   (id symbol?))
                 (primapp-exp
                   (prim primitive?)
                   (rands (list-of expression?)))
  (if-exp
                   (test-exp expression?)
                   (true-exp expression?)
                   (false-exp expression?))
  (let-exp
   (ids (list-of symbol?))
   (rands (list-of expression?))
   (body expression?)
  ))

(define-datatype primitive primitive?
  (add-prim)
  (subtract-prim)
                 (mult-prim)
                 (incr-prim)
                 (decr-prim)
  (equal-prim)
  (zero-prim)
  (greater-prim)
  (less-prim)
                
  )

(define run
  (lambda (x)
    (eval-program (parse-program x))))

(define eval-program
  (lambda (pgm)
    (cases program pgm
      (a-program (body)
                 (eval-expression body (init-env))))))

(define (parse-expression exp)
        (cond ((number? exp)
               (lit-exp exp))
              ((symbol? exp)
               (var-exp exp))
              ((pair? exp)
               (cond ((eqv? (car exp) 'if)
                      (if-exp (parse-expression (cadr exp))
                              (parse-expression (caddr exp))
                              (parse-expression (cadddr exp))))
                     (else
                       (primapp-exp (parse-primitive (car exp))
                                    (map (lambda (rand)
                                           (parse-expression rand))
                                         (cdr exp))))))))

(define (parse-primitive prim)
  (cond ((eqv? prim '+)
         (add-prim))
        ((eqv? prim '-)
         (subtract-prim))
        ((eqv? prim '*)
         (mult-prim))
        ((eqv? prim 'add1)
         (incr-prim))
        ((eqv? prim 'sub1)
         (decr-prim))
        ((eqv? prim 'equal?)
         (equal-prim))
        ((eqv? prim 'zero?)
         (zero-prim))
        ((eqv? prim 'greater?)
         (greater-prim))
        ((eqv? prim 'less?)
         (less-prim))
        
        ))

(define (parse-program prog)
  (a-program (parse-expression prog)))

(define list-of
  (lambda (pred)
    (lambda (val)
      (or (null? val)
          (and (pair? val)
               (pred (car val))
               ((list-of pred) (cdr val)))))))

(define eval-expression
  (lambda (exp env)
    (cases expression exp
      (lit-exp (datum) datum)
      (var-exp (id) (apply-env env id))
      (primapp-exp (prim rands)
                   (let ((args (eval-rands rands env)))
                     (apply-primitive prim args)))
      (if-exp (test-exp true-exp false-exp)
              (if (true-value? (eval-expression test-exp env))
                  (eval-expression true-exp env)
                  (eval-expression false-exp env)))
      (let-exp (ids rands body)
                    (let ((args (eval-rands rands env)))
                      (eval-expression body (extend-env ids args env))))
      )))

(define eval-rands
  (lambda (rands env)
    (map (lambda (x) (eval-rand x env)) rands)))

(define eval-rand
  (lambda (rand env)
    (eval-expression rand env)))

(define init-env
  (lambda ()
    (extend-env
      '(i v x)
      '(1 5 10)
      (empty-env))))

(define (apply-env env sym)
    ((car env) sym))

(define (extend-env syms vals env)
    (list
      (lambda (sym)
        (let ((pos (list-find-position sym syms)))
          (if (number? pos)
              (list-ref vals pos)
              (apply-env env sym))))
      (lambda (sym)
        (if (memv sym syms)
            #t
            (has-association? env sym)))))

(define empty-env
  (lambda ()
    (list
      (lambda (sym)
        (error 'apply-env "No binding for ~s" sym))
      (lambda (sym) #f))))

(define (list-find-position sym los)
    (list-index (lambda (sym1) (eqv? sym1 sym)) los))

(define (list-index pred ls)
    (cond
      ((null? ls) #f)
      ((pred (car ls)) 0)
      (else (let ((temp (list-index pred (cdr ls))))
              (if (number? temp)
                  (+ temp 1)
                  #f)))))

(define (has-association? env sym)
    ((cadr env) sym))

(define apply-primitive
  (lambda (prim args)
    (cases primitive prim
      (add-prim () (+ (car args) (cadr args)))
      (subtract-prim () (- (car args) (cadr args)))
      (mult-prim () (* (car args) (cadr args)))
      (incr-prim () (+ (car args) 1))
      (decr-prim () (- (car args) 1))
      (equal-prim () (if (= (car args) (cadr args)) 1 0))
      (zero-prim () (if (= (car args) 0) 1 0))
      (greater-prim () (if (> (car args) (cadr args)) 1 0))
      (less-prim () (if (< (car args) (cadr args)) 1 0))
      

      )))

(define true-value?
  (lambda (x)
    (not (zero? x))))
```
</details>

## Exercise 3.17 [*]

<details>
<summary>Solution</summary>

```                        

```
</details>
