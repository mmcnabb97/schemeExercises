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
