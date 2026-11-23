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

`parent(tom, X)` checks for atom `tom`'s children as variable `X`s via **unification** against facts in `kb.pl`. By **recursion**, the child-chain is `tom `$\to$ `liz` and `tom` $\to$ `bob`. That is why these are the first and second answer respectively, with count as `2`.

## P02

`parent(tom, bob)` is a fact and **unification** checks if the case matches against any data in `kp.pl`, which it does match one fact therefore, the only answer is `true` and count is `1`.

## P03

`parent(X, tom)` checks for atom `tom`'s parents by **unification** against all facts but there's none. Therefore, the only answer is `false` and count is `0`.

## P04

`=` is **unification** and binds `Y=b` $\to$ `X=f(Y,a)` which resulted to `X=f(b,a)`. Therefore, only one answer and count is `1`.

## P05

**Unification** binds `X` $\to$ `a` and `Y` $\to$ `b` therefore the only answer is `X=a,Y=b` and count is `1`.

## P06

Since two atoms are attempted to be unified (via **unification**), the only answer is `false` and count is `0`.

## P07

`grandparent(tom, X)` looks for `toms`' grandchildren and he have two direct grandchildren only, so count is `2`.

## P08

`ancestor(tom, X)` looks for all of `toms`' descendants **recursively** and returns the top-most descendants first. `liz` and `bob` which are the direct children of `tom`, and count is `6` (`liz`, `bob`, `ann`, `pat`, `sue`, `jim`).

## P09

`ancestor_r(tom, X)` shares the process of `ancestor(tom, X)` (also **recursive**) but returns the bottom-most descendants which are `jim` and `sue`, and count is still `6`.

## P10

`ancestor(X, jim)` looks for `jim`'s ancestors **recursively** which are `pat` and `bob` directly, and count is `3` because `tom` is the top-most ancestor.

## P11

`reach(a, d)` checks via **recursion** **left-to-right** but since target is `a` and recursively enters the first parameter first, the result is `unbounded`.

## P12

`reach_r(a, d)` checks if `a` can reach `d` via **recursion** (acts like modus ponens) and it can, therefore the only answer is `true`, and count is `1`.


## P13

`len([a, b, c], N)` records the list's length using **recursion** on variable `N` until the base case. Therefore, the only answer is `N=3` and count is `1`.

## P14

`join(X, Y, [1, 2])` uses **list decomposition** to partition `[1, 2]` into all valid pairings of `X` and `Y`. The first answer is `X=[],Y=[1, 2]`, the second is `X=[1],Y=[2]`, and count is `3` because `X=[1,2],Y=[]` is also valid.

## P15

`X > 2, elem(X, [1-3])` is an invalid statement because arithmetic comparison on unbound variable `X` uses **left-to-right evaluation order**. This triggers an all three fields to be `error`.

## P16

`elem(X, [1-3]), X > 2` first binds `X` to list elements via **unification** before testing `X > 2` and by **left-to-right evaluation order**, it is now valid. Only `X=3` satisfies the condition, making it the only answer so count `1`.