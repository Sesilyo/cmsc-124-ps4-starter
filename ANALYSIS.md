# The Search and the Stack

Case P08
== trace P08 ==
Call: ancestor(tom,X)
  Call: parent(tom,X)
  Exit: parent(tom,liz)
Exit: ancestor(tom,liz)
answer 1: X=liz
Redo: ancestor(tom,liz)
  Redo: parent(tom,liz)
  Exit: parent(tom,bob)
Exit: ancestor(tom,bob)
answer 2: X=bob
Redo: ancestor(tom,bob)
  Redo: parent(tom,bob)
  Fail: parent(tom,X)
  Call: parent(tom,_)
  Exit: parent(tom,liz)
  Call: ancestor(liz,X)
    Call: parent(liz,X)
    Fail: parent(liz,X)
    Call: parent(liz,_)
    Fail: parent(liz,_)
  Fail: ancestor(liz,X)
  Redo: parent(tom,liz)
  Exit: parent(tom,bob)
  Call: ancestor(bob,X)
    Call: parent(bob,X)
    Exit: parent(bob,ann)
  Exit: ancestor(bob,ann)
Exit: ancestor(tom,ann)
answer 3: X=ann
Redo: ancestor(tom,ann)
  Redo: ancestor(bob,ann)
    Redo: parent(bob,ann)
    Exit: parent(bob,pat)
  Exit: ancestor(bob,pat)
Exit: ancestor(tom,pat)
answer 4: X=pat
Redo: ancestor(tom,pat)
  Redo: ancestor(bob,pat)
    Redo: parent(bob,pat)
    Fail: parent(bob,X)
    Call: parent(bob,_)
    Exit: parent(bob,ann)
    Call: ancestor(ann,X)
      Call: parent(ann,X)
      Exit: parent(ann,sue)
    Exit: ancestor(ann,sue)
  Exit: ancestor(bob,sue)
Exit: ancestor(tom,sue)
answer 5: X=sue
Redo: ancestor(tom,sue)
  Redo: ancestor(bob,sue)
    Redo: ancestor(ann,sue)
      Redo: parent(ann,sue)
      Fail: parent(ann,X)
      Call: parent(ann,_)
      Exit: parent(ann,sue)
      Call: ancestor(sue,X)
        Call: parent(sue,X)
        Fail: parent(sue,X)
        Call: parent(sue,_)
        Fail: parent(sue,_)
      Fail: ancestor(sue,X)
      Redo: parent(ann,sue)
      Fail: parent(ann,_)
    Fail: ancestor(ann,X)
    Redo: parent(bob,ann)
    Exit: parent(bob,pat)
    Call: ancestor(pat,X)
      Call: parent(pat,X)
      Exit: parent(pat,jim)
    Exit: ancestor(pat,jim)
  Exit: ancestor(bob,jim)
Exit: ancestor(tom,jim)
answer 6: X=jim
Redo: ancestor(tom,jim)
  Redo: ancestor(bob,jim)
    Redo: ancestor(pat,jim)
      Redo: parent(pat,jim)
      Fail: parent(pat,X)
      Call: parent(pat,_)
      Exit: parent(pat,jim)
      Call: ancestor(jim,X)
        Call: parent(jim,X)
        Fail: parent(jim,X)
        Call: parent(jim,_)
        Fail: parent(jim,_)
      Fail: ancestor(jim,X)
      Redo: parent(pat,jim)
      Fail: parent(pat,_)
    Fail: ancestor(pat,X)
    Redo: parent(bob,pat)
    Fail: parent(bob,_)
  Fail: ancestor(bob,X)
  Redo: parent(tom,bob)
  Fail: parent(tom,_)
Fail: ancestor(tom,X)
== 6 answers ==



- Which of its lines push a frame onto Activity 15's runtime stack? Which
  lines pop one?

Activity 15 says a call pushes a fresh frame and returning pops it. In the P08 trace:

`Call:` lines push a frame. `Call: ancestor(tom,X)` and `Call: parent(tom,X)` each make a new frame with its own arguments, return address, saved state, and a link to its caller. The caller is now suspended, waiting for the callee to finish. The trace indents deeper each time, which shows the stack growing.
`Exit:` and `Fail:` lines pop a frame. `Exit: parent(tom,liz)` pops its frame and gives back `X=liz`, and the return address tells `ancestor(tom,X)` where to continue. `Fail: parent(liz,X)` also pops its frame, but it gives back no value, so the caller tries its next clause or fails too.

- What does a `Redo` line resume that a returned frame cannot?

A popped frame is gone. All it held was its own data, return address, saved state, and links, and all it could do was return one value. A `Redo` resumes a choice point, which is the saved information about which alternatives are still untried. Activity 15 says backtracking goes back to the most recent choice point that still has alternatives, undoes the bindings made since then, and tries the next one. In P08, `Redo: parent(tom,liz)` is followed by `Exit: parent(tom,bob)`, which gives answer 2. The popped frame for `parent(tom,liz)` couldn't return that, because only the choice point knew the bob fact hadn't been tried yet.

- Why does swapping the two clauses of `reach/2` change whether the search
  ever answers, while swapping the two clauses of `ancestor/2` only changes
  the order of the answers?

The clauses of `reach/2` are `reach(X, Y) :- reach(X, Z), edge(Z, Y).` and `reach(X, Y) :- edge(X, Y)` which is naturally recursive because Prolog reads *left-to-right* and clause 1 keeps going to itself (`reach(a, Z)`) before checking an `edge` *fact*. Meanwhile `ancestor`'s clauses `ancestor(A, D) :- parent(A, D).` and  `ancestor(A, D) :- parent(A, X), ancestor(X, D).` have a finite list to base it's recursion so when swapped $\to$ `ancestor\_r(A, D) :- parent(A, X), ancestor\_r(X, D).` and   `ancestor\_r(A, D) :- parent(A, D).` it simply reverses the answer order.