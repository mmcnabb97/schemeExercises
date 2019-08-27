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
Write a 
## Exercise 1.10 [*]
In the last line of subst-in-symbol-expression, the recursion is on se
and not a smaller substructure. Why is the recursion guaranteed to halt?

## Exercise 1.11 [*]  
Eliminate the one call to `subst-in-symbol-expression` in `subst` by replacing
it by its definition and simplifying the resulting procedure. The result will
be a version of `subst` that does not need `subst-in-symbol-expression`. This
technique is called inlining, and is used by optimizing compilers.

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

```scheme
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
