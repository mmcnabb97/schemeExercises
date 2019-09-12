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
