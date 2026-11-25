# The Search and the Stack

Replace this paragraph after your checks pass. Paste the output of `./trace`
for one case. Then answer three questions about it.

- Which of its lines push a frame onto Activity 15's runtime stack? Which
  lines pop one?
- What does a `Redo` line resume that a returned frame cannot?
- Why does swapping the two clauses of `reach/2` change whether the search
  ever answers, while swapping the two clauses of `ancestor/2` only changes
  the order of the answers?

---

**Q3**:

P11 traced yields an infinite number of steps. The clauses of `reach/2` are `reach(X, Y) :- reach(X, Z), edge(Z, Y).` and `reach(X, Y) :- edge(X, Y)` which is naturally recursive because Prolog reads *left-to-right* and clause 1 keeps going to itself (`reach(a, Z)`) before checking an `edge` *fact*. Meanwhile `ancestor`'s clauses `ancestor(A, D) :- parent(A, D).` and  `ancestor(A, D) :- parent(A, X), ancestor(X, D).` have a finite list to base it's recursion so when swapped $\to$ `ancestor\_r(A, D) :- parent(A, X), ancestor\_r(X, D).` and   `ancestor\_r(A, D) :- parent(A, D).` it simply reverses the answer order.