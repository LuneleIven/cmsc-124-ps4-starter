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

Write one to three sentences here. Name the rule that fixes each field.

## P10

Write one to three sentences here. Name the rule that fixes each field.

## P11

Write one to three sentences here. Name the rule that fixes each field.

## P12

Write one to three sentences here. Name the rule that fixes each field.

## P13

Write one to three sentences here. Name the rule that fixes each field.

## P14

Write one to three sentences here. Name the rule that fixes each field.

## P15

Write one to three sentences here. Name the rule that fixes each field.

## P16

Write one to three sentences here. Name the rule that fixes each field.

