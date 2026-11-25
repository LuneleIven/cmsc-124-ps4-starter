# The Search and the Stack

## The trace and the stack

```text
== trace P07 ==
Call: grandparent(tom,X)
  Call: parent(tom,_)
  Exit: parent(tom,liz)
  Call: parent(liz,X)
  Fail: parent(liz,X)
  Redo: parent(tom,liz)
  Exit: parent(tom,bob)
  Call: parent(bob,X)
  Exit: parent(bob,ann)
Exit: grandparent(tom,ann)
answer 1: X=ann
Redo: grandparent(tom,ann)
  Redo: parent(bob,ann)
  Exit: parent(bob,pat)
Exit: grandparent(tom,pat)
answer 2: X=pat
Redo: grandparent(tom,pat)
  Redo: parent(bob,pat)
  Fail: parent(bob,X)
  Redo: parent(tom,bob)
  Fail: parent(tom,_)
Fail: grandparent(tom,X)
== 2 answers ==
```

Counting `== trace P07 ==` as line 1, every Call pushes a frame: lines 2, 3, 5 and 9. The Fail on line 6 pops one for good, because liz has no children. The Exit on line 4 returns to grandparent, but its frame stays because later parent facts could still match. Activity 15 popped a frame on every return.

That kept frame is a choice point. The Redo on line 7 resumes parent(tom,liz), which still knows which fact it matched and which ones are untried. It undoes P=liz, and line 8 binds bob instead. Activity 15's returned frame had already thrown that record away.


## Clause order in reach and ancestor

`ancestor` and `reach` differ in where the recursive call sits relative to the goal that makes progress. In `ancestor`, both clauses read a `parent` fact before calling `ancestor` recursively. The base clause reads one parent edge; the recursive clause reads one parent edge and then asks the smaller question. Prolog can therefore reach a fact and return an answer before it considers another recursive call. Swapping the clauses changes only the order of enumeration: recursive-first yields deep descendants before shallow ones, but both orders eventually produce the same finite answers because every recursive call consumes a parent edge in a finite, acyclic family tree.

`reach` is left-recursive. Its recursive clause is `reach(X, Y) :- reach(X, Z), edge(Z, Y)`, and the recursive call comes before any `edge/2` goal. When this clause is tried first, a query such as `reach(a, d)` immediately calls `reach(a, Z)` with a fresh `Z`. That call enters the same clause again, producing another fresh variable, and so on. The search descends forever without ever reaching the base clause `edge(X, Y)`, so it never reaches an answer. This is not merely a different order: the first clause creates an infinite branch to the left of the base clause.

Swapping `reach` so the `edge` clause comes first changes that. Prolog first tries a direct edge, which grounds the search in a fact, and only on backtracking enters the recursive clause. Each recursive step then consumes an edge before calling `reach` again, so the search can produce answers. The cycle may still make the enumeration endless, but the first answer is reachable. My `route/3` avoids that problem directly by carrying a visited list and refusing to revisit a node. Because that list is passed down and checked with `\+ member`, the route search considers only simple paths and terminates instead of circling the cycle forever.
