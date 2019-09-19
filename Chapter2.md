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

## Exercise 2.13 [*]

Define term to be either a variable, a constant, or a list of terms. implement parse-term, unparse-term, and all-ids for the term language
<details>
<summary>Solution</summary>

```
(define list-of
  (lambda (pred)
    (lambda (val)
      (or (null? val)
          (and (pair? val)
               (pred (car val))
               ((list-of pred) (cdr val)))))))

(define constant?
  (lambda (datum)
    (or (string? datum)
        (number? datum)
        (boolean? datum)
        (null? datum))))

(define-datatype term term?
                 (var-term
                   (id symbol?))
                 (constant-term
                   (datum constant?))
                 (app-term
                   (terms (list-of term?))))

(define (parse-term datum)
    (cond
      ((symbol? datum) (var-term datum))
      ((constant? datum) (constant-term datum))
      ((pair? datum)
       (app-term (map (lambda (a-term)
                        (parse-term a-term))
                      datum)))
      (else (eopl:error 'parse
                        "Invalid syntax ~s" datum))))

(define (unparse-term exp)
    (cases term exp
           (var-term (id) id)
           (constant-term (datum) datum)
           (app-term (terms)
                     (map (lambda (term)
                            (unparse-term term))
                          terms))))

(define (all-ids exp)
    (all-ids-iter exp '()))

(define (all-ids-iter exp ids)
        (cases term exp
               (var-term (id)
                         (if (memv id ids)
                             ids
                             (cons id ids)))
               (constant-term (datum) ids)
               (app-term (terms)
                         (if (null? terms)
                             ids
                             (all-ids-iter (car terms)
                                           (all-ids-iter
                                             (app-term (cdr terms)) ids))))))
```
</details>

## Exercise 2.14 [*]

Consider the datatype of stacks of values, with an interface consisting of empty-stack, push, pop, top and empty-stack?. Write a specification for these operations in the style of the example above. What operations are constructors and which are observers?
<details>
<summary>Solution</summary>

```
(empty-stack) = [0] ;

(push e [s]) = [t] where (top [t]) = e and (pop [t]) = [s]

(pop [s]) = error if (empty-stack? [s]),
            [t] where (push (top [s]) t) = s otherwise

(top [s]) = error if (empty-stack? [s]),
            e where (push e (pop [s])) = [s] otherwise

(empty-stack? [s]) = true if [s] = [0],
                     false otherwise
                     
The constructors are push, pop, and empty-stack.
The observers are empty-stack? and top.
```
</details>

## Exercise 2.15 [*]

Consider the datatype of stacks of values, with an interface consisting of empty-stack, push, pop, top and empty-stack?. Write a specification for these operations in the style of the example above. What operations are constructors and which are observers?
<details>
<summary>Solution</summary>

```
(define a-list '(1 2 3 4 5))


(define (empty-stack)
  '())

(define (push lst var)
  (list var lst))

(define (pop lst)
  (let ([li (car lst)])
  (set! lst (cdr lst))
  li))

(define (top lst)
  (car lst))

(define (empty-stack? lst)
  (eqv? '() lst))

```
</details>

## Exercise 2.16 [*]

Implement the procedure list-find-last-position, which is like list-find-position except that it returns the position of the rightmost matching symbol. Do this without reverse or list->vector. When is list-find-position and list-find-last-position the same?
<details>
<summary>Solution</summary>

```
(define (list-find-last-position sym los)
    (list-index (lambda (sym1) (eqv? sym1 sym))los 0))

(define (list-index pred ls index)
    (cond ((null? ls) #f)
          ((pred (car ls))
           (if (memv (car ls) (cdr ls))
               (list-index pred (cdr ls) (+ index 1))
               index))
          (else (list-index pred (cdr ls) (+ index 1)))))

The procedures function the same whenever there is only one copy of the symbol in the list.
```
</details>

## Exercise 2.17 [*]

Add to the environment interface a predicate called has-association? rhat takes an environment env and symbol s and tests to see if s has associated value in env. Extend the procedural representation to implement this by representing the environemnt by two procedures: one that returns the value associated with a symbol and one that returns whether or not the symbol has an assoiation

<details>
<summary>Solution</summary>

```
(has-association? [f] s) = true if s=t and f(s)=k and [f] = (extend-env '(t) '(k) [g])
                                or (has-association? [g] s)
                           false if [f] = [0]

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

(define (has-association? env sym)
    ((cadr env) sym))

(define (apply-env env sym)
    ((car env) sym))

                   
```
</details>


## Exercise 2.18 [*]

Implement environment-to-list

<details>
<summary>Solution</summary>

```
(define (environment-to-list e)
    (cases environment e
           (empty-env-record ()
                             (list 'empty-env-record))
           (extended-env-record (syms vals env)
                                (append '(extended-env-record)
                                        (list syms)
                                        (list vals)
                                        (list (environment-to-list env))))))
```
</details>


## Exercise 2.19 [*]

Implement the stack data type of exercise 2.14 using an abstract syntax tree representation

<details>
<summary>Solution</summary>

```
(define scheme-value? (lambda (v) #t))

(define-datatype stack stack?
                 (empty-stack-record)
                 (push-record
                   (e scheme-value?)
                   (s stack?))
                 (pop-record
                   (s stack?)))


(define empty-stack
  (lambda ()
    (empty-stack-record)))

(define (push e s)
    (push-record e s))

(define (pop s)
    (cases stack s
           (empty-stack-record ()
             (eopl:error 'pop "The Stack is Empty"))
           (push-record (e1 s1) s1)
           (pop-record (s1) s1)))

(define (top s)
    (cases stack s
           (empty-stack-record ()
             (eopl:error 'top "Empty stack"))
           (push-record (e1 s1) e1)
           (pop-record (s1) (top s1))))

(define (empty-stack? s)
    (cases stack s
           (empty-stack-record () #t)
           (push-record (e s1) #f)
           (pop-record (s1) (empty-stack? s1))))
```
</details>

## Exercise 2.20 [*]

Implement has-association? of exercise 2.17 to the abstract syntax tree representation.

<details>
<summary>Solution</summary>

```
(define (has-association? env sym)
    (cases environment env
           (empty-env-record () #f)
           (extended-env-record
             (syms vals env)
             (if (memv sym syms)
                 #t
                 (has-association? env sym)))))
```
</details>

## Exercise 2.21 [*]

(What list structure does (extend-env '() '() (empty-env)) produce
<details>
<summary>Solution</summary>

```
(((()())))
```
</details>

## Exercise 2.22 [*]

Design a 2-element rib datatype and use it to implement the environement interface.
<details>
<summary>Solution</summary>

```
(define scheme-value? (lambda (v) #t))

(define-datatype 2-rib 2-rib?
                 (empty-2-rib-record)
                 (extend-2-rib-record
                   (syms (list-of symbol?))
                   (vals (list-of scheme-value?))
                   (rib 2-rib?)))

(define empty-2-rib
  (lambda ()
    (empty-2-rib-record)))

(define (extend-2-rib sym vals rib)
  (extend-2-rib-record sym vals rib))

(define (lookup sym rib)
  (cases 2-rib rib
    (empty-2-rib-record ()
                        (eopl:error 'lookup "This is an empty list"))
    (extend-2-rib-record (syms vals trib)
                         (let ((pos (list-find-position sym syms)))
               (if (number? pos)
                   (list-ref vals pos)
                   (lookup sym trib))))))

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

(define empty-env (lambda () (empty-2-rib)))

(define (extend-env syms vals env) (extend-2-rib syms vals env))

(define (apply-env env sym) (lookup sym env))
```
</details>

## Exercise 2.23 [*]

A simpler representation of environements would consist of a single pair of ribs: a list of symbols and a list of values. Implement the environemnt interface for this representation.
<details>
<summary>Solution</summary>

```
(define scheme-value? (lambda (v) #t))


(define-datatype rib rib?
  (rib-record
   (sym (list-of symbol?))
   (vals (list-of scheme-value?))))

(define empty-rib
  (lambda ()
    (rib-record '() '())))

(define (extend-rib sym vals r)
  (cases rib r
    (rib-record
     (sym1 vals1)
     (rib-record
      (append sym1 sym)
      (append vals1 vals)))))

(define (lookup sym r)
  (cases rib r
    (rib-record (syms vals)
                (let ((pos (list-find-position sym syms)))
                  (if (number? pos)
                      (list-ref vals pos)
                      (eopl:error 'lookup "~s is not in the list" sym))))))



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

(define empty-env (lambda () (empty-rib)))

(define (extend-env syms vals env) (extend-rib syms vals env))

(define (apply-env env sym) (lookup sym env))
```
</details>

## Exercise 2.24 [*]


<details>
<summary>Solution</summary>

```
(define empty-subst
  (lambda ()
    (lambda (sym)
      (var-term sym))))

(define (apply-subst s i)
  (s i))

(define (extend-subst i t s)
  (lambda (sym)
    (if (eqv? i sym) t (apply-subst s i))))
    

```
</details>

