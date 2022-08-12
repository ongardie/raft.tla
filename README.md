## Purpose
Try to model check some raft conditions in finite time

## Strategy
1. Eliminate unnecessary variables
    - eliminated variables: allLogs, voterLog, elections, behaviorLength, mlog
2. Add state constraint to make the number of state finite
    - constraints for all servers: currentTerm (T), log length (L), duplicate message length (M)
    - Note: you could either add the constraints in Next or specify in model spec options. Do not add in invariances; otherwise the model check will terminate as long as it find one state that does not meet the constraint

## upper bound
From @kmsalem
```
currentTerm:  T*S
state: 3*S
votedFor:  S^2
log: S*L   (assuming a single value)
commitIndex: S*L
votesResponded: S*2^S
votesGranted: S*2^S
nextIndex: S*L
matchIndex: S*L

Overall state space size: S^14 * L^4 * T^1
(not counting the "messages" variable) 
```

However, we find that with small bound on the state constraints, the model check still run inifinite time.

The problem is that we don't bound masgs[m] to be bigger or equal to 0.

Solution: we should only reply if there is message
```
WithoutMessage(m, msgs) ==
    IF m \in DOMAIN msgs /\ msgs[m] > 0 THEN
        [msgs EXCEPT ![m] = msgs[m] - 1]
    ELSE
        msgs
```