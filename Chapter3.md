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
