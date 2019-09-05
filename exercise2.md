# Scheme Basics
Read the first two chapters of
EOPL: Essentials of Programming Languages (2nd edition), and answer the
following questions.

## Exercise 1.1 [*]

Write a syntactic derivation that proves `(-7 . (3 . (14 . ())))` is a list of
numbers.
<details>
<summary>Solution</summary>

```
<list-of-numbers> ::= () | (<number> . <list-of-numbers>)

<list-of-numbers>
=> (<number> . <list-of-numbers>)
=> (-7 . <list-of-numbers>)
=> (-7 . (<number> . <list-of-numbers>))
=> (-7 . (3 . <list-of-numbers>))
=> (-7 . (3 . (<number> . <list-of-numbers>)))
=> (-7 . (3 . (14 . <list-of-numbers>)))
=> (-7 . (3 . (14 . ())))
```
</details>

## Exercise 1.2 [*]
Rewrite the `<datum>` grammar without using the Kleene star or plus. Then
indicate the changes to the above derivation that are required by this revised
grammar.

<details>
<summary>Solution</summary>

```
<list>          ::= (<datum>  <list>)
<dotted-datum>  ::= (<datum>) | (<datum> . <dotted-datum>)
<vector>        ::= #() | #(<datum> <vector>)
<data>          ::= <datum> | <datum> <data>

<list>
(<datum>  <list>)
(<data>  <list>)
(<datum> <data>  <list>)
(<datum> <datum>  <list>)
(<datum> <datum>  <datum>)
(<boolean>  <datum> <datum>)
(#t  <datum> <datum>)
(#t  <dotted-datum> <datum>)
(#t  (<dotted-datum>.<datum>) <datum>)
(#t  (<datum>.<datum>) <datum>)
(#t  (<symbol>.<datum>) <datum>)
(#t  (foo.<datum>) <datum>)
(#t  (foo.<list>) <datum>)
(#t  (foo.()) <datum>)
(#t  (foo.()) <number>)
(#t  (foo.()) 3)
```
</details>

## Exercise 1.3 [*]
Write a syntactic derivation that proves `(a "mixed" #(bag (of . data)))`
is a datum, using either the grammar in the book or the
revised grammar from the preceding exercise. What is wrong with
`(a . b . c)`?
<details>
<summary>Solution</summary>

```
<datum>
<list>
({<datum>}*)
(<datum> <datum> <datum>)
(<symbol> <datum> <datum>)
(a <datum> <datum>)
(a <string> <datum>)
(a "mixed" <datum>)
(a "mixed" <vector>)
(a "mixed" #({<datum>}*))
(a "mixed" #(<datum><datum>))
(a "mixed" #(<symbol><datum>))
(a "mixed" #(bag <datum>))
(a "mixed" #(bag <dotted-datum>))
(a "mixed" #(bag ({datum}+.<datum>)))
(a "mixed" #(bag (<datum>.<datum>)))
(a "mixed" #(bag (<symbol>.<datum>)))
(a "mixed" #(bag (of.<datum>)))
(a "mixed" #(bag (of.<symbol>)))
(a "mixed" #(bag (of.data)))
```
</details>

## Exercise 1.4 [*]
Prove that if e is an element of [expression], then there are the same number of left and right parentheses in e  (where [expression] is defined as in section 1.2)
<details>
<summary>Solution</summary>

```
the definition of <expression> is so defined:
<expression> ::= <identifier>
             ::= (lambda (<identifier>) <expression>)
             ::= (<expression> <expression>)
1. if e is an <identifier> then the number of parentheses is a constant 0 and IH is trivially true.
2. if e is (lambda (<identifier>) <expression>) then the number of left parantheses is 2 plus any left paranthesis created by the <expression> and the number of right parenthesis is 2 plus any right paranthesis created by the <expression>.
3. if e is (<expression> <expression>) then the number of left parantheses is 1 plus any left paranthesis created by either <expression> and the number of right parenthesis is 1 plus any right paranthesis created by either <expression>.
4. Each <expression> in the definitions can then recursively become any rule of <expression>. Therefore we know that the number of parantheses added at each individual step must be:
(a) 0 left and 0 right
(b) 2 left and 2 right
(c) 1 left and 1 right
5. Using the above information, we can prove if IH(K) is true then IH(K+1) is true
1. The number of left paranthesis at IH(k) is equal to l and the number of right paranthesis at IH(k) is equal to r such that l=r.
2. IH(K) must have a number of <expressions> which we will define as s. 
3. At IH(K+1) we know that these <expressions> will be split in some arbitrary combination between <identifier>, (lambda (<identifier>) <expression>), and (<expression> <expression>). 
4. The <identifier>s will add 0 left paratheses and 0 right parantheses. We will divide the subsection of s into s1. 
l+s1*0=r+s1*0
5. The (lambda (<identifier>) <expression>)s will add 2 left paratheses and 2 right parantheses. We will divide the subsection of s into s2. 
l+s1*0+s2*2=r+s1*0+s2*2
6. The (<expression> <expression>)s will add 1 left paratheses and 1 right parantheses. We will divide the subsection of s into s3. 
l+s1*0+s2*2+s3*1=r+s1*0+s2*2+s3*1
7. We can first remove the s1*0 term from both sides since that will always equal 0.
l+s2*2+s3*1=r+s2*2+s3*1
8. We can then multiply through the other constants.
l+2s2+s3=r+2s2+s3
9. Next we can subtract 2s2 from both sides.
l+s3=r+s3
10. Next we can subtract s3 from both sides.
1=r
11. this equal the original IH(K) term. Therefore we have sufficiently proven that the number of left and right parantheses must always be equal.
```
</details>

## Exercise 1.5 [*]
This version of list-of-numbers? works properly only when it's argument is a list. Extend the definition of list-of-numbers? so that it will work on an artitrary Scheme [datum] and return #f on any argument that is not a list.
<details>
<summary>Solution</summary>

```
(define list-of-numbers?
  (lambda (lst)
    (if (list? lst )
      (if (null? lst)
        #t
        (and
          (number? (car lst))
          (list-of-numbers? (cdr lst))))#f)))
        
```
</details>

## Exercise 1.6 [*]
What happens if nth-elt and list-length are passed symbols when a list is expected? What is the behavior of list-ref and length  in such cases? Write robust versions of nth-elt and list-length.

<details>
<summary>Solution</summary>

```
Both will throw an error is given an improper type.

List-ref and length wll provide the proper, error checked answers

(define nth-elt
  (lambda (lst n)
    (cond ((null? lst) (error 'nth-elt
          "List is too short by ~s elements" (+ n 1)))
          ((not (list? lst)) (error "lst not a list"))
          ((zero? n) (car lst))
          (else (nth-elt (cdr lst) (- n 1))))))
          
(define list-length
  (lambda (lst)
    (cond ((null? lst) 0)
          ((not (list? lst)) (error "lst not a list"))
          (else (+ 1 (list-length (cdr lst)))))))
```
</details>

## Exercise 1.7 [*]

The error message from nth-elt is uninformative. Rewrite nth-elt so that it produces a more informative error message, such as (a b c) does not contain an element 4." Hint: use letrec to create a local recursive procedure that does the real work.
<details>
<summary>Solution</summary>

```
(define nth-elt
  (lambda (lst n)
    (letrec ((process-first
               (lambda (rest i)
                 (if (zero? i) (car rest)
                     (process-rest (cdr rest) (- i 1)))))
             (process-rest
               (lambda (rest i)
                 (if (null? rest)
                     (begin
                       (display lst)
                       (display " does not have an element ")
                       (display n)
                       (newline))
                     (process-first rest i)))))
      (process-rest lst n))))
```
</details>
## Exercise 1.8 [*]
In the definition of remove-first, if the inner if's alternative (cons ...) were replaced by (remove-first s (cdr los)), what function would the resulting procedure compute?
<details>
<summary>Solution</summary>

```
It would give the second instance of s or an empty list if s is not found.
```
</details>

## Exercise 1.9 [*]
In the definition of remove, if the inner if's alternative (cons ...) were replaced by (remove s (cdr los)), what function would the resulting procedure compute?
<summary>Solution</summary>

```
It would give  an empty list.
```
</details>

## Exercise 1.10 [*]
In the last line of subst-in-symbol-expression, the recursion is on se
and not a smaller substructure. Why is the recursion guaranteed to halt?

<summary>Solution</summary>

```
The result will be either empty or both null? and symbol? conditions will be met.
```
</details>

## Exercise 1.11 [*]  
Eliminate the one call to `subst-in-symbol-expression` in `subst` by replacing
it by its definition and simplifying the resulting procedure. The result will
be a version of `subst` that does not need `subst-in-symbol-expression`. This
technique is called inlining, and is used by optimizing compilers.
<summary>Solution</summary>

```
(define subst
  (lambda (new old slist)
    (if (null? slist)
        '()
        (cons
          ((lambda (new old se)
             (if (symbol? se)
                 (if (eqv? se old) new se)
                 (subst new old se)))
           new old (car slist))
          (subst new old (cdr slist))))))
```
</details>

## Exercise 1.12 [**]
In our example, we began by eliminating the Kleene star in the grammar for
`<s-list>`. When a production is expressed using Kleene star, often the
recursion can be expressed using `map`. Write `subst` following the original
grammar by using `map`.

<details>
<summary>Solution</summary>

```scheme
(define subst
  (lambda (new old slist)
    (if (null? slist)
      '()
      (map (lambda (x)
             (subst-in-symbol-expression new old x))
           slist))))

(define subst-in-symbol-expression
  (lambda (new old se)
    (if (symbol? se)
      (if (eqv? se old)
          new
          se)
      (subst new old se))))


(subst 'a 'b '((b c) (b () d))); => '((a c) (a () d))
```
</details>

Exercise 1.13 [**]
Rewrite the grammar for `<s-list>` to use Kleene star, and rewrite
`notate-depth-in-s-list` using `map`.
```
<s-list>            ::= ()
                    ::= ({<symbol-expression>}*)
<symbol-expression> ::= <symbol> | <s-list>
```
<details>
<summary>Solution</summary>

```
(define notate-depth
  (lambda (slist)
    (notate-depth-in-s-list slist 0)))

(define notate-depth-in-s-list
  (lambda (slist d)                     
    (if (null? slist)
      '()
      (map (lambda (x)
             (notate-depth-in-symbol-expression x d))
           slist))))

(define notate-depth-in-symbol-expression
  (lambda (se d)
    (if (symbol? se)
      (list se d)
      (notate-depth-in-s-list se (+ d 1)))))

;> (notate-depth '(a (b () c) ((d)) e))
;((a 0) ((b 1) () (c 1)) (((d 2))) (e 0)))
```
</details>

Exercise 1.14 [**]
Given the assumption `0 ≤ n < length(von)`, prove that `partial-vector-sum` is
correct.

<details>
<summary>Solution</summary>

```
IH(0) holds as it is trivally true. All elements in an empty vector always sum to 0.

Assume IH(k), prove IH(k+1)
By IH(k), we know 
0+v[1]+v[2]+v[3]+...+v[k] = a
where a is the sum of the first k elements in vector v.
Since v is not empty, we know the sum of the k+1 is given by
0+v[1]+v[2]+v[3]+v[4]+...+v[k+1]
This can be furthur expanded to
0+v[1]+v[2]+v[3]+v[4]+...+v[k] + v[k+1]
We can then substitute a in
a+ v[k+1]
This means that the partial-vector-sum of the elements up to k+1 is the sum of the elements up to k plus k+1.
This completes our proof.

```
</details>

Exercise 1.15 [*]
Define, test, and debug the following procedures. Assume that `s` is any symbol,
`n` is a nonnegative integer, `lst` is a list, `v` is a vector, `los` is a list
of symbols, `vos` is a vector of symbols, `slist` is an s-list, and `x` is any
object; and similarly `s1` is a symbol, `los2` is a list of symbols, `x1` is an
object, etc. Also assume that `pred` is a predicate, that is, a procedure that
takes any Scheme object and returns either `#t` or `#f`. Make no other
assumptions about the data unless further restrictions are given as part of a
particular problem. For these exercises, there is no need to check that the
input matches the description; for each procedure, assume that its input values
are members of the specified sets.

To test these procedures, at the very minimum try all of the given examples.
Also use other examples to test these procedures, since the given examples are
not adequate to reveal all possible errors.

1.  `(duple n x)` returns a list containing n copies of x.
```
> (duple 2 3)
(3 3)
> (duple 4 '(ho ho))
((ho ho) (ho ho) (ho ho) (ho ho))
> (duple 0 '(blah))
()
```
<details>
<summary>Solution</summary>

```scheme
(define (duple n x)
  (if (= n 0)
      '()
      (cons x
            (duple (- n 1) x))))

(duple 2 3); => '(3 3)
(duple 4 '(ho ho)); => '((ho ho) (ho ho) (ho ho) (ho ho))
(duple 0 '(blah)); => '()
```
</details>

2. `(invert lst)`, where `lst` is a list of 2-lists (lists of length two),
returns a list with each 2-list reversed.
```
> (invert '((a 1) (a 2) (b 1) (b 2)))
((1 a) (2 a) (1 b) (2 b))
```
<details>
<summary>Solution</summary>

```scheme
(define (invert lst)
  (if (null? lst)
      '()
      (cons (list (cadr (car lst)) (car (car lst)))
            (invert (cdr lst)))))


(invert '((a 1) (a 2) (b 1) (b 2)))
; =>    '((1 a) (2 a) (1 b) (2 b))
```
</details>

3. `(filter-in pred lst)` returns the list of those elements in `lst` that satisfy the predicate
pred.
```
> (filter-in number? '(a 2 (1 3) b 7))
(2 7)
> (filter-in symbol? '(a (b c) 17 foo))
(a foo)
```
<details>
<summary>Solution</summary>

```scheme
(define (filter-in pred lst)
  (if (null? lst)
      '()
      (if (pred (car lst))
          (cons (car lst)
                (filter-in pred (cdr lst)))
          (filter-in pred (cdr lst)))))

(filter-in number? '(a 2 (1 3) b 7)); => (2 7)
(filter-in symbol? '(a (b c) 17 foo)); => (a foo)
```
</details>

4. `(every? pred lst)` returns `#f` if any element of `lst` fails to satisfy
`pred`, and returns `#t` otherwise.
```
> (every? number? '(a b c 3 e))
#f
> (every? number? '(1 2 3 5 4))
#t
```
<details>
<summary>Solution</summary>

```scheme
(define (every? pred lst)
  (if (null? lst)
      #t
      (and (pred (car lst))
           (every? pred (cdr lst)))))

(every? number? '(a b c 3 e)); => #f
(every? number? '(1 2 3 5 4)); => #t
```
</details>

5. `(exists? pred lst)` returns `#t` if any element of `lst` satisfies `pred`,
and returns `#f` otherwise.
```
> (exists? number? '(a b c 3 e))
#t
> (exists? number? '(a b c d e))
#f
```

<details>
<summary>Solution</summary>

```scheme
(define (exists? pred lst)
   (if (null? lst)
       #f
       (or (pred (car lst))
            (exists? pred (cdr lst)))))
```
</details>

6. `(vector-index pred v)` returns the zero-based index of the first element of
`v` that satisfies the predicate `pred`, or `#f` if no element of `v` satisfies
`pred`.
```
> (vector-index (lambda (x) (eqv? x 'c)) '#(a b c d))
2
> (vector-ref '#(a b c) (vector-index (lambda (x) (eqv? x 'b)) '#(a b c)))
b
```

<details>
<summary>Solution</summary>

```scheme
(define vector-index
  (lambda (pred v)
    (vector-index-rec pred v (vector-length v))))

(define vector-index-rec
  (lambda (pred v n)
    (if (pred (vector-ref v (- n 1)))
        (- n 1)
        (vector-index-rec pred v (- n 1)))))

(define vector-index
  (lambda (pred v)
    (letrec ((vector-index-rec
              (lambda (pred v n)
                (if (pred (vector-ref v (- n 1)))
                    (- n 1)
                    (vector-index-rec pred v (- n 1))))))
      (vector-index-rec pred v (vector-length v)))))
```
</details>

7. `(list-set lst n x)` returns a list like `lst`, except that the n-th element,
using zero-based indexing, is `x`.
```
> (list-set '(a b c d) 2 '(1 2))
(a b (1 2) d)
> (list-ref (list-set '(a b c d) 3 '(1 5 10)) 3)
(1 5 10)
```

<details>
<summary>Solution</summary>

```scheme
(define (list-set lst n x)
  (if (= n 0)
      (cons x (cdr lst))
      (cons (car lst)
            (list-set (cdr lst) (- n 1) x))))
```
</details>

8. `(product los1 los2)` returns a list of 2-lists that represents the
Cartesian product of `los1` and `los2`. The 2-lists may appear in any order.
```
> (product '(a b c) '(x y))
((a x) (a y) (b x) (b y) (c x) (c y))
```

<details>
<summary>Solution</summary>

```scheme
(define (product los1 los2)
  (if (null? los1)
      '()
      (append (partial-product (car los1) los2)
            (product (cdr los1) los2))))

(define (partial-product s los)
  (if (null? los)
      '()
      (cons (list s (car los))
            (partial-product s (cdr los)))))

(define (product los1 los2)
  (if (null? los1)
      '()
      (append (map (lambda (x)
                     (list (car los1) x))
                   los2)
            (product (cdr los1) los2))))
```
</details>

9. `(down lst)` wraps parentheses around each top-level element of `lst`.
```
> (down '(1 2 3))
((1) (2) (3))
> (down '((a) (fine) (idea)))
(((a)) ((fine)) ((idea)))
> (down '(a (more (complicated)) object))
((a) ((more (complicated))) (object))
```

<details>
<summary>Solution</summary>

```scheme
(define (down lst)
  (if (null? lst)
      '()
      (cons (list (car lst))
            (down (cdr lst)))))
```
</details>

10. `(vector-append-list v lst)` returns a new vector with the elements of `lst`
attached to the end of `v`. Do this without using `vector->list`, `list->vector`,
and `append`.
```
> (vector-append-list '#(1 2 3) '(4 5))
#(1 2 3 4 5)
```

Exercise 1.16 [**]
Solve the following exercises

1. 
<details>
<summary>Solution</summary>

```
(define up
  (lambda (lst)
    (cond ((null? lst)
           '())
          ((list? (car lst))
           (append (car lst) (up (cdr lst))))
          (else (cons (car lst) (up (cdr lst)))))))
```
</details>

2. 
<details>
<summary>Solution</summary>

```
(define swapper
  (lambda (s1 s2 slist)
    (cond ((null? slist)
           '())
          ((list? (car slist))
           (cons (swapper s1 s2 (car slist))
                 (swapper s1 s2 (cdr slist))))
          ((eq? s1 (car slist))
           (cons s2
                 (swapper s1 s2 (cdr slist))))
          ((eq? s2 (car slist))
           (cons s1
                 (swapper s1 s2 (cdr slist))))
          (else (cons (car slist)
                      (swapper s1 s2 (cdr slist)))))))
```
</details>

3. 
<details>
<summary>Solution</summary>

```
(define count-occurrences
(lambda (s slist)
(cond ((null? slist) 0)
((list? (car slist))
(+ (count-occurrences s (car slist))
(count-occurrences s (cdr slist))))
((eqv? s (car slist))
(+ 1 (count-occurrences s (cdr slist))))
(else (count-occurrences s (cdr slist))))))
```
</details>

4. 
<details>
<summary>Solution</summary>

```
(define flatten
  (lambda (slist)
    (cond ((null? slist) '())
          ((null? (car slist)) (flatten (cdr slist)))
          ((list? (car slist))
           (append (flatten (car slist))
                   (flatten (cdr slist))))
          (else (cons (car slist)
                      (flatten (cdr slist)))))))
```
</details>

5.
<details>
<summary>Solution</summary>

```
(define merge
  (lambda (lon1 lon2)
    (cond ((null? lon1) lon2)
          ((null? lon2) lon1)
          ((<= (car lon1) (car lon2))
           (cons (car lon1)
                 (merge (cdr lon1) lon2)))
          (else (cons (car lon2)
                      (merge lon1 (cdr lon2)))))))
```
</details>

Exercise 1.17 [**]
Solve the following exercises

1. 
<details>
<summary>Solution</summary>

```
(define path
(lambda (n bst)
(letrec ((finder
(lambda (tree thepath)
(cond ((null? tree) '())
((= (car tree) n) thepath)
(else (let ((foundl (finder (cadr tree)
(append thepath '(left)))))
(if (not (null? foundl))
foundl
(finder (caddr tree)
(append thepath '(right))))))))))
(finder bst '()))))
```
</details>

2. 
<details>
<summary>Solution</summary>

```
- Uses filter-in from 1.15

(define sort
  (lambda (lon)
    (define less
      (lambda (n) (lambda (x) (< x n))))
    (define greaterorequal
      (lambda (n) (lambda (x) (>= x n))))
    (if (null? lon)
        '()
        (append (sort (filter-in (less (car lon)) (cdr lon)))
                (list (car lon))
                (sort (filter-in (greaterorequal (car lon)) (cdr lon))))))
```
</details>

3. 
<details>
<summary>Solution</summary>

```
(define sort
  (lambda (predicate lon)
    (define ispred
      (lambda (pred n) (lambda (x) (pred x n))))
    (define isnotpred
      (lambda (pred n) (lambda (x) (not (pred x n)))))
    (if (null? lon)
        '()
        (append (sort predicate (filter-in (ispred predicate (car lon))
                                 (cdr lon)))
                (list (car lon))
                (sort predicate (filter-in (isnotpred predicate (car lon))
                                 (cdr lon)))))))
```
</details>


Exercise 1.18 [**]
Solve the following exercises

1. 
<details>
<summary>Solution</summary>

```
(define compose
(lambda (p1 p2)
(lambda (x) (p1 (p2 x)))))
```
</details>

2. 
<details>
<summary>Solution</summary>

```
(define collect
  (lambda (s slist errvalue col)
    (cond ((null? slist) (col errvalue))
          ((list? (car slist))
           (let ((e (collect s (car slist) errvalue
                             (lambda (seen)
                               (col `(compose ,seen car))))))
             (if (eqv? e errvalue)
                 (collect s (cdr slist) errvalue
                          (lambda (seen)
                            (col `(compose ,seen cdr))))
                 e)))
          (else (if (eqv? s (car slist))
                    (col 'car)
                    (collect s (cdr slist) errvalue
                             (lambda (seen)
                               (if (eqv? seen errvalue)
                                   errvalue
                                   (col `(compose ,seen cdr))))))))))

(define car&cdr
  (lambda (s slist errvalue)
    (define id
      (lambda (x) x))
    (collect s slist errvalue id)))
```
</details>

3. 
<details>
<summary>Solution</summary>

```
(define depth
  (lambda (s slist)
    (cond ((not (list? slist))
           (list slist (list s)))
          ((null? slist)
           (list s))
          ((null? (cdr slist))
           (depth s (car slist)))
          ((not (null? (cdr slist)))
           (list (car slist)
                 (depth s (cdr slist)))))))


(define collect2
  (lambda (s slist errvalue col)
    (cond ((null? slist) (col errvalue))
          ((list? (car slist))
           (let ((e (collect2 s (car slist) errvalue
                              (lambda (seen)
                                (col (depth 'car seen))))))
             (if (eqv? e errvalue)
                 (collect2 s (cdr slist) errvalue
                           (lambda (seen)
                             (col (depth 'cdr seen))))
                 e)))
          (else (if (eqv? s (car slist))
                    (col '(car))
                    (collect2 s (cdr slist) errvalue
                              (lambda (seen)
                                (if (eqv? seen errvalue)
                                    errvalue
                                    (col (depth 'cdr seen))))))))))

(define car&cdr2
  (lambda (s slist errvalue)
    (define identity
      (lambda (x) x))
    (collect2 s slist errvalue identity)))
```
</details>

## Exercise 1.19 [*]

Write a procedure free-vars that takes a list structure representing an expression in lambda calculus syntax given above and return a set z9a list without duplicates) of all the variables that occcur free in the expression. Similarly,write a procedure bound-vars that returns a set of all variables that occur bound in its argument.
<details>
<summary>Solution</summary>

```
-Using occurs-free and occurs-bound which appear on page 32

(define occurs-free?
  (lambda (var exp)
    (cond
      ((symbol? exp) (eqv? var exp))
      ((eqv? (car exp) 'lambda)
       (and (not (eqv? (caadr exp) var))
            (occurs-free? var (caddr exp))))
      (else (or (occurs-free? var (car exp))
                (occurs-free? var (cadr exp)))))))

(define extractvari
  (lambda (exp)
    (define collector
      (lambda (exp vars)
        (cond ((symbol? exp)
               (if (not (memq exp vars))
                   (cons exp vars)))
              ((eqv? (car exp) 'lambda)
               (collector (caddr exp) vars))
              (else (append (collector (car exp) vars)
                            (collector (cadr exp) vars))))))
    (collector exp '())))

(define free-vars
  (lambda (exp)
    (filter-in (lambda (var)
                 (occurs-free? var exp))
               (extractvari exp))))
               
               
(define occurs-bound?
(lambda (var exp)
    (cond
      ((symbol? exp) #f)
      ((eqv? (car exp) 'lambda)
       (or (occurs-bound? var (caddr exp))
           (and (eqv? (caadr exp) var)
                (occurs-free? var (caddr exp)))))
      (else (or (occurs-bound? var (car exp))
                (occurs-bound? var (cadr exp)))))))
                
 (define bound-vars
   (lambda (exp)
    (filter-in (lambda (var)
                 (occurs-bound? var exp))
               (extractvari exp))))
```
</details>

## Exercise 1.20 [*]

Give an example of a lambda calculus expression in which a variable occurs free but which has a value that is independent of the value of the free variable.

<details>
<summary>Solution</summary>

```
((lambda (x) y) z)
```
</details>

## Exercise 1.21 [*]

GIve an exampl of  a  lambda calculus expression where the same variable occurs both bound and unbound.

<details>
<summary>Solution</summary>

```
((lambda (x) x) x)
```
</details>

## Exercise 1.22 [*]

Scheme lambda expressions may have any number of formal paramenters, and scheme procedure calls may have any number of operands. Modify the formal definitions of occurs free and occur bound to allow lambda expressions with any number of formal parameters and procedure calls  with any number of operands. Then modify the procedures occurs-free and occurs-bound to follow these new definitions.

<details>
<summary>Solution</summary>

```
A variable x occurs free in a lambda calculus expression E if and only if:
1. E is a variable reference and E is identical to x
OR
2. E is of the form (lambda (y1 y2 y3... yn) E'), where n > 0 and x is distinct from every y, and x occurs free in E'
OR
3. E is of the form (E1 E2) and x is a component and free in E1 or in E2

(define occurs-free?
  (lambda (var exp)
    (cond
      ((symbol? exp) (eqv? var exp))
      ((eqv? (car exp) 'lambda)
       (and (not (memq var (cadr exp)))    
            (occurs-free? var (caddr exp))))
      (else (or (occurs-free? var (car exp))
                (occurs-free? var (cadr exp)))))))
                
A variable x occurs free in a lambda calculus expression E if and only if:
1. E is of the form (lambda (y1 y2 y3... yn) E'), where n > 0 and x occurs bound in E or x is equal to any y and x  occurs free in E'
OR
2. E is of the form (E1 E2) and x is a component and bound in E1 or in E2

(define occurs-bound?
  (lambda (var exp)
    (cond
      ((symbol? exp) #f)
      ((eqv? (car exp) 'lambda)
       (or (occurs-bound? var (caddr exp))
           (and (memq var (cadr exp))
                (occurs-free? var (caddr exp)))))
      (else (or (occurs-bound? var (car exp))
                (occurs-bound? var (cadr exp)))))))
```
</details>

## Exercise 1.23 [*]

Extend the formal definitions of occurs free and occurs bound to include if statements

<details>
<summary>Solution</summary>

```
A variable x occurs free in a lambda calculus expression E if and only if
1. E is a variable reference and E is identical to x
OR
2. E is of the form (lambda (y1 y2 y3... yn) E'), where n > 0 and x is distinct from every y, and x occurs free in E'
OR
3. E is of the form (E1 E2) and x is a component and free in E1 or in E2
OR
4. E is of the form (if E1 E2 E3) and x is a component and free in E1 or in E2 or in E3



A variable x occurs free in a lambda calculus expression E if and only if:
1. E is of the form (lambda (y1 y2 y3... yn) E'), where n > 0 and x occurs bound in E or x is equal to any y and x  occurs free in E'
OR
2. E is of the form (E1 E2) and x is a component and bound in E1 or in E2
OR
3. E is of the form (if E1 E2 E3) and x is a component and bound in E1 or in E2 or in E3

```
</details>

## Exercise 1.24 [*]

Extend the formal definitions of occurs free and occurs bound to include let and let* expressions

<details>
<summary>Solution</summary>

```
A variable x occurs free in a lambda calculus expression E if and only if
1. E is a variable reference and E is identical to x
OR
2. E is of the form (lambda (y1 y2 y3... yn) E'), where n > 0 and x is distinct from every y, and x occurs free in E'
OR
3. E is of the form (E1 E2) and x is a component and free in E1 or in E2
OR
4. E is of the form (if E1 E2 E3) and x is a component and free in E1 or in E2 or in E3
OR
5. E is of the form (let [y1 s1] [y2 s2]...[yn sn], where n > 0 and x is distinct from every y, and x occurs free in E'
OR
6. E is of the form (let* [y1 (list s1)] [y2 (cons s2 y1)]...[yn (cons sn yn-1)], where n > 0 and x is distinct from every y, and x occurs free in E'



A variable x occurs free in a lambda calculus expression E if and only if:
1. E is of the form (lambda (y1 y2 y3... yn) E'), where n > 0 and x occurs bound in E or x is equal to any y and x  occurs free in E'
OR
2. E is of the form (E1 E2) and x is a component and bound in E1 or in E2
OR
3. E is of the form (if E1 E2 E3) and x is a component and bound in E1 or in E2 or in E3
OR 
4.E is of the form (let [y1 s1] [y2 s2]...[yn sn], where n > 0 and x occurs bound in E or x is equal to any y and x  occurs free in E'
OR
5.E is of the form (let* [y1 (list s1)] [y2 (cons s2 y1)]...[yn (cons sn yn-1)], where n > 0 and x occurs bound in E or x is equal to any y and x occurs free in E'
```
</details>

## Exercise 1.25 [*]

Extend the formal definitions of occurs free and occurs bound to include scheme quotations ( expressions of the form (quote [datum]))

<details>
<summary>Solution</summary>

```
A variable x occurs free in a lambda calculus expression E if and only if
1. E is a variable reference and E is identical to x
OR
2. E is of the form (lambda (y1 y2 y3... yn) E'), where n > 0 and x is distinct from every y, and x occurs free in E'
OR
3. E is of the form (E1 E2) and x is a component and free in E1 or in E2
OR
4. E is of the form (if E1 E2 E3) and x is a component and free in E1 or in E2 or in E3
OR
5. E is of the form (let [y1 s1] [y2 s2]...[yn sn], where n > 0 and x is distinct from every y, and x occurs free in E'
OR
6. E is of the form (let* [y1 (list s1)] [y2 (cons s2 y1)]...[yn (cons sn yn-1)], where n > 0 and x is distinct from every y, and x occurs free in E'
OR
7.E is of the form (quote E1) and x is a component and free in E1

A variable x occurs free in a lambda calculus expression E if and only if:
1. E is of the form (lambda (y1 y2 y3... yn) E'), where n > 0 and x occurs bound in E or x is equal to any y and x  occurs free in E'
OR
2. E is of the form (E1 E2) and x is a component and bound in E1 or in E2
OR
3. E is of the form (if E1 E2 E3) and x is a component and bound in E1 or in E2 or in E3
OR 
4.E is of the form (let [y1 s1] [y2 s2]...[yn sn], where n > 0 and x occurs bound in E or x is equal to any y and x  occurs free in E'
OR
5.E is of the form (let* [y1 (list s1)] [y2 (cons s2 y1)]...[yn (cons sn yn-1)], where n > 0 and x occurs bound in E or x is equal to any y and x occurs free in E'
6. E is of the form (quote E1) and x is a component and bound in E1


```
</details>

## Exercise 1.26 [*]

Extend the formal definitions of occurs free and occurs bound to include scheme assignment (set!) expressions.
<details>
<summary>Solution</summary>

```
(set!) does not actually bind or free anything. It only alters previous bindings. Therefore, the previous formal definition is still sufficently valid.

A variable x occurs free in a lambda calculus expression E if and only if
1. E is a variable reference and E is identical to x
OR
2. E is of the form (lambda (y1 y2 y3... yn) E'), where n > 0 and x is distinct from every y, and x occurs free in E'
OR
3. E is of the form (E1 E2) and x is a component and free in E1 or in E2
OR
4. E is of the form (if E1 E2 E3) and x is a component and free in E1 or in E2 or in E3
OR
5. E is of the form (let [y1 s1] [y2 s2]...[yn sn], where n > 0 and x is distinct from every y, and x occurs free in E'
OR
6. E is of the form (let* [y1 (list s1)] [y2 (cons s2 y1)]...[yn (cons sn yn-1)], where n > 0 and x is distinct from every y, and x occurs free in E'
OR
7.E is of the form (quote E1) and x is a component and free in E1

A variable x occurs free in a lambda calculus expression E if and only if:
1. E is of the form (lambda (y1 y2 y3... yn) E'), where n > 0 and x occurs bound in E or x is equal to any y and x  occurs free in E'
OR
2. E is of the form (E1 E2) and x is a component and bound in E1 or in E2
OR
3. E is of the form (if E1 E2 E3) and x is a component and bound in E1 or in E2 or in E3
OR 
4.E is of the form (let [y1 s1] [y2 s2]...[yn sn], where n > 0 and x occurs bound in E or x is equal to any y and x  occurs free in E'
OR
5.E is of the form (let* [y1 (list s1)] [y2 (cons s2 y1)]...[yn (cons sn yn-1)], where n > 0 and x occurs bound in E or x is equal to any y and x occurs free in E'
6. E is of the form (quote E1) and x is a component and bound in E1


```
</details>

## Exercise 1.27 [*]

In the following expressions, draw an arrow from each variable reference to its associated formal parameter declaration.

<details>
<summary>Solution</summary>

```
See Uploaded Files
```
</details>

## Exercise 1.28 [*]

Repeat the above exercise with programs written in a block structure language other than Scheme

<details>
<summary>Solution</summary>

```
See Uploaded Files
```
</details>

## Exercise 1.29 [*]

What is wrong with the following lexical-address expression?
(lambda (a)
  (lambda (a)
    (a : 1 0)))

<details>
<summary>Solution</summary>

```
The address would default to the closest declaration. Therefore the correct expression would be
(lambda (a)
  (lambda (a)
    (a : 0 0)))
Since the a is in the first position down from a lambda (a)

```
</details>

## Exercise 1.30 [*]

Write a scheme expression that is equivalent to the following lexical-address expression from which variable names have been removed.
(lambda 1
  (lambda 1
    (: 1 0)))
<details>
<summary>Solution</summary>

```
(lambda(x)
  (lambda(y)
    (x))

```
</details>

## Exercise 1.31 [*]

Write a procedure lexical-address that takes aany express and returns the expression with every variable reference v replaced by a list (v: d p), as above. If variable reference v is free, produce the list (v free) instead.

<details>
<summary>Solution</summary>

```
(define setaddress
  (lambda (v d p)
    (list v ': d p))
    
 (define getvar
  (lambda (address)
    (car address)))
    
(define getdep
  (lambda (address)
    (caddr address)))

(define getpos
  (lambda (address)
    (cadddr address)))

(define incrdep
  (lambda (address)
    (setaddress (getvar address)
                          (+ 1 (getdep address))
                          (getpos address)))

(define getaddress
  (lambda (exp addresses)
    (define iter
      (lambda (lst)
        (cond ((null? lst) (list exp 'free))
              ((eqv? exp (getvar (car lst))) (car lst))
              (else (getaddress exp (cdr lst))))))
    (iter addresses)))

(define index
  (lambda (v declarations)
    (define helper
      (lambda (lst ind)
        (cond ((null? lst) 'free)
              ((eqv? (car lst) v) ind)
              (else (helper (cdr lst) (+ ind 1))))))
    (helper declarations 0)))

```
</details>
