<!--no-pdf-->
# Why Each Prediction Reads the Way It Does

Write one entry per case, before you run anything. Name the rule behind each
predicted field. One to three sentences is enough. The grader checks the
structure of this file only. It does not judge how good the reasoning is, and
it sets no word minimum.

For every case you predicted wrongly, add a short correction after you run
it: what you predicted, what the language printed, and which rule you
misread. Corrections belong in the correction commits, not the prediction
commit.

Commit this file together with `predictions.tsv`, before any case runs:

```bash
git add predictions.tsv REASONING.md
```

The example below uses a query that is not one of the 16 cases, so it gives
no answer away. `parent(pat, X)` reads `parent(pat, jim)` because the `parent`
facts are tried top to bottom and only that one has `pat` as its first
argument:

```text
## parent(pat, X)

The goal scans the parent facts in source order. Only parent(pat, jim) has
pat first, so X binds to jim. No second fact matches, so the first ; finds
nothing and the search ends after one answer.
```

## P01
The parent facts are tried top to bottom. parent(tom, liz) is the first with tom first, so X=liz. The first ; moves to parent(tom, bob). No other fact starts with tom, so the count is 2.

## P02
The query is ground, so it has no bindings. parent(tom, bob) matches a fact and the answer is true. No second fact matches, so second is none and the count is 1.

## P03
No parent fact has tom as its second argument, because tom has no parent in the base. Unification fails on every fact, so the answer is false with 0 answers.

## P04
= is unification. It doesn't compute anything. X is bound to the term f(Y, a), and Y = b then fills in Y, so X prints as f(b,a). Bindings print in query order, X then Y. There is no choice point, so the count is 1.

## P05
Unifying f(X, b) with f(a, Y) matches arguments position by position, binding X to a and Y to b. There is exactly one way to unify, so the count is 1.

## P06
Two different atoms never unify, and no binding can change that. The answer is false with 0 answers.

## P07
P binds to liz first, and parent(liz, X) fails, so the search backtracks and P becomes bob. parent(bob, X) then gives ann, and the first ; gives pat. No other child of tom has children, so the count is 2.

## P08
The base clause comes first, so tom's own children liz and bob come out before the recursive clause runs. Then the recursion goes through bob, giving ann, pat, sue and jim. That makes 6 answers, starting with liz and then bob.

## P09

The base clause tries its recursive clause before its base clause, so it descends from tom through bob before returning tom's own children. The first successful descendant is sue (through bob and ann), then jim (through bob and pat); the search eventually yields six descendants total.

## P10

The base clause first finds `parent(pat, jim)`, giving X=pat. Its recursive clause then finds bob and tom as ancestors through their children; there are three answers total. The TSV's second answer `X=tom` skips bob, so that prediction does not match the clause order and search.

## P11

`reach` calls itself before consulting `edge`, so it repeatedly enters the same recursive clause without making progress. The graph's cycle cannot be escaped by backtracking into the edge clause within the tracer/enumerator budget, so the search reports no reached answers.

## P12

`reach_r` consumes an edge before recurring. There are infinitely many walks from a to d because the a-b-c cycle can be traversed any number of times before taking c-d; each walk succeeds, so the first two answers are both `true` and the count is infinite.

## P13

The three list elements make N=3, with only 1 answer.

## P14

`join` asks what are the values of X and Y that result in [1,2] when joined. There are three answers: `X = [], Y = [1, 2]` ,`X = [1], Y = [2]` and `X = [1, 2], Y = []`

## P15

The arithmetic comparison `X > 2` is called before `elem`. Comparing an uninstantiated variable raises an instantiation error, so the query reports error for all three fields.

## P16

`elem` binds X to list elements in order. The test 1 > 2 fails, so Prolog backtracks. X = 2 also fails. X = 3 passes. After that, elem has nothing left to offer. There is one answer, X = 3.

