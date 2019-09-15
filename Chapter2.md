## Exercise 2.1 [*]

Implement the four required operations for bigits. Then use it to calculate the factorial of 10. How does the execution time vary as this argument changes? how does the execution time  vary as the base changes? explain why.
<details>
<summary>Solution</summary>

```
(define zero
  (lambda (n)
    (list)
  )
)

(define iszero?
  (lambda (n)
    
    (if (eqv? n (car (zero))) (display #t) (display #f)) 
)
)

(define find-mult
  (lambda (n m x)
    (cond
      ((> x n) (find-mult (* n m) m x))
      (else n)
      )
    )
  )

(define (convert-to-bigit b n)
  (let loop ((m n) (acc empty))
    (if (< m b)
        (cons m acc)
        (loop (floor (/ m b))
              (cons (remainder m b) acc)))))


(define (convert-from-bigit-helper lst n)
  (if (null? lst)
    0
    (+ (car lst) ( * n (convert-from-bigit-helper (cdr lst) n )))      
    )
  )

(define (convert-from-bigit lst n)
  (convert-from-bigit-helper (reverse lst) n)
  )

(define (pred lst n)
  (convert-to-bigit n (- (convert-from-bigit lst n) 1))
  )

(define (succ lst n)
  (convert-to-bigit n (+ (convert-from-bigit lst n) 1))
  )

(define (fact x n)
  (convert-to-bigit n (fact-helper x n)))

(define (fact-helper x n)
  (if (eqv? x 0)
      1
      (* x (fact-helper (- x 1) n)
         )
      )
  )
  
  I found no noticable differences in run time as base increased.
```
</details>



## Exercise 2.2 [*]

Analyze each of these proposed representations critically. To what extent do they suceed or fail in satifying the specification of the data type.
<details>
<summary>Solution</summary>

```
Unary representation: It succeeds in representing all nonnegative but is more limited in terms of the operations that can be done in it. Many mathamatic operations are not applicable without conversion to another form (i.e. exponentials, division).

Scheme number representation: It contains full access to operations one would want on all nonnegative integers. It cannot though handle operations that are centered on a base besides base 10 (i.e. bitshift, xor)

Bignum representation: Can represent all nonnegative integers and can handle a variety of bases. Many mathamatic operations are not applicable without conversion to another form (i.e. exponentials, non-bitshift division).
```
</details>

## Exercise 2.3 [*]

Implement vector-of, which is like list-of, butworks for vectors instead of lists. DO this without using vector->list
<details>
<summary>Solution</summary>

```
(define (vector-of pred val)
  (vector? val)
           (vector-iter val pred))

(define (vector-iter vector pred)
        (iter-helper vector 0 pred))


  (define (iter-helper v i pred)
            (cond ((= i (vector-length v)) #t)
                  ((not (pred (vector-ref v i))) #f)
                  (else (iter-helper v (+ i 1) pred))))
```
</details>

## Exercise 2.4 [*]

Implement a bintree-to-list procedure for binary trees, so that (bintree-to-list (interior-node 'a (leaf-node 3) (leaf-node 4))) returns the list
(interior-node
  a
  (leaf-node 3)
  (leaf-node 4))
  
<details>
<summary>Solution</summary>

```
(define-datatype bintree bintree?
  (leaf-node
   (datum number?))
  (interior-node
   (key symbol?)
   (left bintree?)
   (right bintree?)))

(define leaf-sum
  (lambda (tree)
    (cases bintree tree
      (leaf-node (datum) datum)
      (interior-node (key left right)
                     (+ (leaf-sum left) (leaf-sum right))))))

(define (bintree-to-list tree)
    (cases bintree tree
           (leaf-node (datum) (list 'leaf-node datum))
           (interior-node (key left right)
                          (list 'interior-node
                                key
                                (bintree-to-list left)
                                (bintree-to-list right)))))
```
</details>

## Exercise 2.5 [*]

Use cases to write max-interior, which takes a binary tree of numbers with atleast one interior node and returns the symbol associated with an interior node with maximal leaf sum.
<details>
<summary>Solution</summary>

```
(define (prune tree)
  (cases bintree tree
    (leaf-node (datum) '())
    (interior-node (key left right)
                   (let ((sum (leaf-sum tree)))
                         (append (list (list key sum))
                                 (prune left)
                                 (prune right))))))

(define (find-max leaf-nums)
    (find-max-helper leaf-nums (car leaf-nums)))

(define (find-max-helper lst max)
        (cond ((null? lst) max)
              ((> (cadar lst) (cadr max))
               (find-max-helper (cdr lst) (car lst)))
              (else (find-max-helper (cdr lst) max))))

(define (max-interior tree)
    (car (find-max (prune tree))))
```
</details>

## Exercise 2.6 [*]

Draw the abstract syntax tree for the mambda calculus expression 
((lambda (a) (a b)) c)
<details>
<summary>Solution</summary>

```
See attached files
```
</details>

## Exercise 2.7 [*]

Define the data type and parse and unparse procedures for the above grammar. Then implement lexical address of exercise 1.31 using abstract syntax.
<details>
<summary>Solution</summary>

```
(define-datatype expression expression?
                 (lit-exp
                   (datum number?))
                 (var-exp
                   (id symbol?))
                 (if-exp
                   (test-exp expression?)
                   (true-exp expression?)
                   (false-exp expression?))
                 (lambda-exp
                   (ids (list-of symbol?))
                   (body expression?))
                 (app-exp
                   (rator expression?)
                   (rands (list-of expression?))))

(define (parse-expression datum)
    (cond
      ((number? datum) (lit-exp datum))
      ((symbol? datum) (var-exp datum))
      ((list? datum)
       (cond ((eqv? (car datum) 'if)
              (if-exp (parse-expression (cadr datum))
                      (parse-expression (caddr datum))
                      (parse-expression (cadddr datum))))
             ((eqv? (car datum) 'lambda)
              (lambda-exp (cadr datum)
                          (parse-expression (caddr datum))))
             (else
              (app-exp
                (parse-expression (car datum))
                (map (lambda (exp)
                       (parse-expression exp))
                     (cdr datum))))))
       (else (eopl:error 'parse-expression
              "Invalid concrete syntax ~s" datum))))

(define (unparse-expression exp)
    (cases expression exp
           (lit-exp (datum) datum)
           (var-exp (id) id)
           (if-exp (test-exp true-exp false-exp)
                   (list 'if (unparse-expression test-exp)
                         (unparse-expression true-exp)
                         (unparse-expression false-exp)))
           (lambda-exp (ids body)
                       (list 'lambda
                             ids
                             (unparse-expression body)))
           (app-exp (rator rands)
                    (append (list (unparse-expression rator))
                            (map (lambda (rand)
                                   (unparse-expression rand))
                                 rands)))))
                                 
 
 
 
 
 
 (define-datatype expression expression?
                 (lex-info
                   (id symbol?)
                   (sep (lambda (sep) (eqv? sep ':)))
                   (depth number?)
                   (position number?))
                 (free-info
                   (id symbol?))
                 (if-exp
                   (test-exp expression?)
                   (true-exp expression?)
                   (false-exp expression?))
                 (lambda-exp
                   (ids (list-of symbol?))
                   (body expression?))
                 (app-exp
                   (rator expression?)
                   (rands (list-of expression?))))

(define (unparse-expression exp)
    (cases expression exp
           (lex-info (id sep depth position) id)
           (free-info (id) id)
           (if-exp (test-exp true-exp false-exp)
                   (list 'if
                         (unparse-expression test-exp)
                         (unparse-expression true-exp)
                         (unparse-expression false-exp)))
           (lambda-exp (ids body)
                       (list 'lambda
                             ids
                             (unparse-expression body)))
           (app-exp (rator rands)
                    (append (list (unparse-expression rator))
                            (map (lambda (rand)
                                   (unparse-expression rand))
                                 rands)))))
```
</details>

## Exercise 2.8 [*]

Rewrite the solution to exercise 1.19 using abstract syntax . Then compare this version to the original solution.
<details>
<summary>Solution</summary>

```
(define-datatype expression expression?
                 (var-exp
                   (id symbol?))
                 (lambda-exp
                   (ids (list-of symbol?))
                   (body expression?))
                 (app-exp
                   (rator expression?)
                   (rands (list-of expression?))))

(define occurs-free?
  (lambda (var exp)
    (cond
      ((symbol? exp) (eqv? var exp))
      ((eqv? (car exp) 'lambda)
       (and (not (eqv? (caadr exp) var))
            (occurs-free? var (caddr exp))))
      (else (or (occurs-free? var (car exp))
                (occurs-free? var (cadr exp)))))))

(define (parse-expression datum)
    (cond
      ((symbol? datum) (var-exp datum))
      ((list? datum)
       (cond ((eqv? (car datum) 'if))
             ((eqv? (car datum) 'lambda)
              (lambda-exp (cadr datum)
                          (parse-expression (caddr datum))))
             (else
              (app-exp
                (parse-expression (car datum))
                (map (lambda (exp)
                       (parse-expression exp))
                     (cdr datum))))))
       (else (eopl:error 'parse-expression
              "Invalid concrete syntax ~s" datum))))

(define (remove-duplicates lst)
    (cond ((null? lst) '())
          ((memv (car lst) (cdr lst))
           (remove-duplicates (cdr lst)))
          (else (cons (car lst)
                      (remove-duplicates (cdr lst))))))

(define (free-vars exp)
    (let ((ast (parse-expression exp)))
      (define free-vars-iter
        (lambda (subexp)
          (cases expression subexp
                 (var-exp (id)
                          (if (occurs-free? id ast)
                              (list id)
                              '()))
                 (lambda-exp (id body)
                             (if (occurs-free? id ast)
                                 (append (list id)
                                         (free-vars-iter body))
                                 (free-vars-iter body)))
                 (app-exp (rator rand)
                          (append (free-vars-iter rator)
                                  (free-vars-iter rand))))))
      (remove-duplicates (free-vars-iter ast))))

(define (occurs-bound? var exp)
    (cases expression exp
           (var-exp (id) #f)
           (lambda-exp (id body)
                       (or (occurs-bound? var body)
                           (and (eqv? id var)
                                (occurs-free? var body))))
           (app-exp (rator rand)
                    (or (occurs-bound? var rator)
                        (occurs-bound? var rand)))))

(define (bound-vars exp)
    (let ((ast (parse-expression exp)))
      (define (bound-vars-iter subexp)
          (cases expression subexp
                 (var-exp (id)
                          (if (occurs-bound? id ast)
                              (list id)
                              '()))
                 (lambda-exp (id body)
                             (if (occurs-bound? id ast)
                                 (append (list id)
                                         (bound-vars-iter body))
                                 (bound-vars-iter body)))
                 (app-exp (rator rand)
                          (append (bound-vars-iter rator)
                                  (bound-vars-iter rand)))))
      (remove-duplicates (bound-vars-iter ast))))
```
</details>


## Exercise 2.9 [*]

The procedure parse expression is fragile: it does not detect several possible syntactic errors, such as (a b c), and aborts with inappropriate error messages for other errors such as (lambda). Modify it so that it is robust, accepting any datum and issuing an appropriate error ,message if the datum does not represent a lambda calculus expression

<details>
<summary>Solution</summary>

```
(define (parse-expression datum)
    (cond
      ((symbol? datum) (var-exp datum))
      ((list datum)
       (if (eqv? (car datum) 'lambda)
           (begin
             (tester (= (length datum) 3)
                     "Invalid lambda expression ~s"
                     datum)
             (lambda-exp (caadr datum)
                         (parse-expression (caddr datum))))
           (begin
             (tester (= (length datum) 2)
                     "Invalid procedure application ~s"
                     datum)
             (app-exp
               (parse-expression (car datum))
               (parse-expression (cadr datum))))))
      (else (eopl:error 'parse-expression
                        "Invalid concrete syntax ~s" datum))))
                        
(define tester
  (lambda (test msg datum)
    (if (not test)
        (eopl:error 'parse-expression msg datum) '#t)))
```
</details>

## Exercise 2.10 [*]

Implement fresh-id by defining all-ids, which finds all the symbols in an expression. This includes the free occurences, the bound occurences, and the lambda identifiers for which there are no bound occurences.
<details>
<summary>Solution</summary>

```
(define-datatype expression expression?
                 (var-exp
                   (id symbol?))
                 (lambda-exp
                   (ids (list-of symbol?))
                   (body expression?))
                 (app-exp
                   (rator expression?)
                   (rands (list-of expression?))))


(define (all-ids exp)
    (all-ids-iter exp '()))

(define (all-ids-iter exp ids)
        (cases expression exp
               (var-exp (id)
                        (if (memv id ids)
                            ids
                            (cons id ids)))
               (lambda-exp (id body)
                           (if (memv id ids)
                               (all-ids-iter body ids)
                               (all-ids-iter body (cons id ids))))
               (app-exp (rator rand)
                        (all-ids-iter rator (all-ids-iter rand ids)))))
(define fresh-id
  (lambda (exp s)
    (let ((syms (all-ids exp)))
      (letrec
              ((loop (lambda (n)
                       (let ((sym (string->symbol
                                    (string-append s
                                                   (number->string n)))))
                         (if (memv sym syms) (loop (+ n 1)) sym)))))
        (loop 0)))))
```
</details>


## Exercise 2.11 [*]

Extend parse-expression and unparse-expression to support this enhancement
<details>
<summary>Solution</summary>

```
part 1
(define-datatype expression expression?
                 (var-exp
                   (id symbol?))
                 (lambda-exp
                   (id symbol?)
                   (body expression?))
                 (app-exp
                   (rator expression?)
                   (rand expression?))
                 (lit-exp
                   (datum is-positive?))
                 (primapp-exp
                   (prim is-primitive?)
                   (rand1 expression?)
                   (rand2 expression?)))

(define (is-primitive? proc)
    (or (eqv? proc '*)
        (eqv? proc '+)
        (eqv? proc '-)
        (eqv? proc '/)))

(define (is-positive? datum)
    (and (number? datum)
         (positive? datum)))

(define (parse-expression datum)
    (cond
      ((symbol? datum) (var-exp datum))
      ((is-positive? datum) (lit-exp datum))
      ((pair? datum)
       (cond ((eqv? (car datum) 'lambda)
              (lambda-exp (caadr datum)
                          (parse-expression (caddr datum))))
             ((is-primitive? (car datum))
              (primapp-exp (car datum)
                           (parse-expression (cadr datum))
                           (parse-expression (caddr datum))))
             (else (app-exp
                     (parse-expression (car datum))
                     (parse-expression (cadr datum))))))
      (else (eopl:error 'parse-expression
                        "Invalid concrete syntax ~s" datum))))


(define (unparse-expression exp)
    (cases expression exp
           (var-exp (id) id)
           (lambda-exp (id body)
                       (list 'lambda (list id)
                             (unparse-expression body)))
           (app-exp (rator rand)
                    (list (unparse-expression rator)
                          (unparse-expression rand)))
           (lit-exp (datum) datum)
           (primapp-exp (prim rand1 rand2)
                        (list prim
                              (unparse-expression rand1)
                              (unparse-expression rand2)))))


(define (lambda-calculus-subst exp subst-exp subst-id)
    (letrec
            ((subst
               (lambda (exp)
                 (cases expression exp
                        (var-exp (id)
                                 (if (eqv? id subst-id)
                                     subst-exp
                                     exp))
                        (lambda-exp (id body)
                                    (if (eqv? id subst-id)
                                        exp
                                        (lambda-exp id (subst body))))
                        (app-exp (rator rand)
                                 (app-exp (subst rator)
                                          (subst rand)))
                        (lit-exp (datum)
                                 (lit-exp datum))
                        (primapp-exp (prim rand1 rand2)
                                     (primapp-exp prim
                                                  (subst rand1)
                                                  (subst rand2)))))))
      (subst exp)))



(define occurs-free?
  (lambda (var exp)
    (cases expression exp
           (var-exp (id) (eqv? id var))
           (lambda-exp (id body)
                       (and (not (eqv? id var))
                            (occurs-free? var body)))
           (app-exp (rator rand)
                    (or (occurs-free? var rator)
                        (occurs-free? var rand)))
           (lit-exp (datum) #f)
           (primapp-exp (prim rand1 rand2)
                        (or (occurs-free? var rand1)
                            (occurs-free? var rand2))))))


(define (all-ids exp)
    (all-ids-iter exp '()))

(define (all-ids-iter exp ids)
        (cases expression exp
               (var-exp (id)
                        (if (memv id ids)
                            ids
                            (cons id ids)))
               (lambda-exp (id body)
                           (if (memv id ids)
                               (all-ids-iter body ids)
                               (all-ids-iter body (cons id ids))))
               (app-exp (rator rand)
                        (all-ids-iter rator (all-ids-iter rand ids)))
               (lit-exp (datum) ids)
               (primapp-exp (prim rand1 rand2)
                            (all-ids-iter rand1 (all-ids-iter rand2 ids)))))

(define (fresh-id exp s)
    (let ((syms (all-ids exp)))
      (letrec
              ((loop (lambda (n)
                       (let ((sym (string->symbol
                                    (string-append s
                                                   (number->string n)))))
                         (if (memv sym syms) (loop (+ n 1)) sym)))))
        (loop 0))))

Part 2

(define (lambda-calculus-subst exp subst-exp subst-id)
    (letrec
            ((subst
               (lambda (exp)
                 (cases expression exp
                        (var-exp (id)
                                 (if (eqv? id subst-id)
                                     subst-exp
                                     exp))
                        (lambda-exp (id body)
                                    (cond ((eqv? id subst-id) exp)
                                          ((occurs-free? id subst-exp)
                                           (let ((the-fresh-id
                                                   (fresh-id body (symbol->string id))))
                                             (lambda-exp the-fresh-id
                                                         (subst (lambda-calculus-subst
                                                                  body
                                                                  (var-exp the-fresh-id)
                                                                  id)))))
                                          (else (lambda-exp id (subst body)))))
                        (app-exp (rator rand)
                                 (app-exp (subst rator)
                                          (subst rand)))
                        (lit-exp (datum)
                                 (lit-exp datum))
                        (primapp-exp (prim rand1 rand2)
                                     (primapp-exp prim
                                                  (subst rand1)
                                                  (subst rand2)))))))
      (subst exp)))
```
</details>


## Exercise 2.12 [*]

Implement these operators. Do they use recursion explicitly?
<details>
<summary>Solution</summary>

```
(define (alpha-subst exp dest-id orig-id)
    (letrec
            ((subst
               (lambda (exp)
                 (cases expression exp
                        (var-exp (id) exp)
                        (lambda-exp (id body)
                                    (if (not (occurs-free? dest-id
                                                           body))
                                        (lambda-exp dest-id
                                                    (lambda-calculus-subst
                                                      body
                                                      (var-exp dest-id)
                                                      orig-id))
                                        exp))
                        (app-exp (rator rand) exp)
                        (lit-exp (datum) exp)
                        (primapp-exp (prim rand1 rand2) exp)))))
      (subst exp)))

(define (beta-subst exp)
    (cases expression exp
           (var-exp (id) exp)
           (lambda-exp (id body) exp)
           (app-exp (rator rand)
                    (cases expression rator
                           (var-exp (sub-id) exp)
                           (lambda-exp (sub-id body)
                                       (lambda-calculus-subst
                                         body
                                         rand
                                         sub-id))
                           (app-exp (sub-rator sub-rand) exp)
                           (lit-exp (datum) exp)
                           (primapp-exp (prim rand1 rand2) exp)))
           (lit-exp (datum) exp)
           (primapp-exp (prim rand1 rand2) exp)))

(define (eta-subst exp)
    (letrec
            ((subst
               (lambda (exp)
                 (cases expression exp
                        (var-exp (id) exp)
                        (lambda-exp (id body)
                                    (cases expression body
                                           (var-exp (id) exp)
                                           (lambda-exp (sub-id sub-body)
                                                       exp)
                                           (app-exp (rator rand)
                                                    (if (not (occurs-free? rand
                                                                           rator))
                                                        rator
                                                        exp))
                                           (lit-exp (datum) exp)
                                           (primapp-exp (prim rand1 rand2)
                                                        exp)))
                        (app-exp (rator rand) exp)
                        (lit-exp (datum) exp)
                        (primapp-exp (prim rand1 rand2) exp)))))
      (subst exp)))
      
      
 None use recursion explicitly.
```
</details>
