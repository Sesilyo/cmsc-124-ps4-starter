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

The goal scans the parent facts in source order and takes the first whose
first argument is tom. `parent(tom, liz)` matches, so X binds to liz.` parent(tom,bob)` also matches so the X also binds to bob. No other fact matches, so the count is 2.


## P02

The goal scans the parent facts in source order for one with tom first and bob second. `parent(tom, bob)` exists, so the first answer is true. It has no variables and nothing else matches, so the second is none and the count is 1.

## P03

The goal scans the parent facts in source order for one with tom as the second argument. No fact has tom as a child, so the first answer is false, the second is none, and the count is 0.

## P04

Unification makes two terms identical. X = f(Y, a) unifies X with the term f(Y, a), leaving Y unbound, and Y = b then fills Y in, so the first answer is X=f(b,a),Y=b. Each = succeeds once, so the second is none and the count is 1.

## P05

Unification makes two terms identical by binding variables, when that's possible. f(X, b) unifies with f(a, Y) by binding X to a and Y to b, so the first answer is X=a,Y=b. There is only one way to unify them, so the second is none and the count is 1.

## P06

Unification makes two terms identical by binding variables, when that's possible. a doesn't unify with b, because two different atoms are different and no binding can change that. a = b fails, so the first answer is false, the second is none, and the count is 0.

## P07

The body of grandparent runs left to right, so `parent(tom, P)` binds P to liz first, because that fact is written first. `parent(liz, X)` fails, so the search backtracks into the first goal and rebinds P to bob. Then `parent(bob, X)` gives X=ann first and X=pat second, and nothing is left untried, so the count is 2.

## P08

ancestor tries its base clause first, so `parent(tom, X)` gives X=liz first and X=bob second. Backtracking then enters the recursive clause. `ancestor(liz, X)` is a dead end, while `ancestor(bob, X)` gives ann and pat from its base clause, then sue and jim from its recursive clause. The count is 6 with 2 base answers plus 4 recursive answers.

## P09

ancestor_r tries its recursive clause first, so P binds to liz, whose subtree is a dead end, and then to bob. Inside bob's subtree the recursive clause runs first at every level, so the answers come out as X=sue, X=jim, X=ann, X=pat, and then the base clause adds X=liz and X=bob. The set is the same as P08, only the order differs, so the count is 6.

## P10

ancestor tries its base clause first, so parent(X, jim) gives X=pat first. The recursive clause then scans parent(X, P) in file order and asks ancestor(P, jim) for each fact, and the successes give X=bob second and X=tom third. The count is 3 with 1 base answer plus 2 recursive answers.

## P11

reach tries its first clause first, whose body starts with another call to reach with the same first argument. That call matches the same clause again, so the question never gets smaller, and no edge fact is read, so nothing fails or backtracks and clause 2 is never reached. Because the search never reaches even the first answer, the first field is false, the second is none, and the count is 0.

## P12

reach_r tries its base clause first, which fails because there is no edge(a, d), so the recursive clause walks a to b to c, and edge(c, d) succeeds, so the first answer is true. The cycle a, b, c, a and the lack of a visited list let the search take edge(c, a) and prove the same fact again, so the second answer is true. The search never runs out of options, so the count is unbounded.

## P13

Only the recursive clause of len matches [a, b, c], so it asks about the tail again and again until the list is empty. The base clause gives 0 and `is` adds 1 on the way back up for each element, so the first answer is N=3. No other clause matches at any level, so the second is none and the count is 1.


## P14

The third argument is bound and the first two are not, so join runs backward. Clause 1 is tried first and gives X=[],Y=[1,2]. Clause 2 then moves 1 into X and asks join(T, Y, [2]), which gives X=[1],Y=[2] next, and once more gives X=[1,2],Y=[] when the third argument reaches []. The count is 3.

## P15

The goals run left to right, so X > 2 is tried before elem has bound X. X is unbound and > needs numbers on both sides, so Prolog throws an instantiation error instead of failing. The search stops there and never reaches `elem`, so the first field, the second field is unbounded, and the count is error.

## P16

The goals run left to right, so elem binds X to 1 first and X > 2 is then checked with X already a number. That comparison fails for 1 and 2, so the search backtracks into elem each time, and only X=3 passes. The first answer is X=3, the second is none, and the count is 1.

