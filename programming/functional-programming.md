# Functional Programming

## Functional Programming - Basic Rules

1. No shared state - the function should only depend on its inputs
2. Pure function - included in 1\) - the function should not depend on anything outside of it
3. No side effects - included in 1\) - the function should not influence anything outside of it

```text
data = {} # wrong - the function should not have a 'shared state'
fn(x,y):
    ....
    ....
    data = ... # wrong - as above - code should not have a 'shared state'
    file.write() ... # wrong - it might fail or might already exist
    .... 
    return ...
```



