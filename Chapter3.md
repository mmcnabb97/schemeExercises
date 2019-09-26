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
      (list-prim () (list (car args) (list (cadr args) (caddr))))
      (car-prim () (car args))
      (cdr-prim () (cdr args))
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

