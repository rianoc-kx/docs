# `exit`



_Terminate kdb+._

`Syntax`: `exit x`, `exit[x]`

where `x` is a positive integer, terminates the kdb+ process with `x` as the exit code.

```q
q)exit 0        / typical successful exit status
..

q)exit 42
```
```bash
$ echo $?
42
```

<i class="far fa-hand-point-right"></i> 
[Debugging](../basics/debug.md),
[`.z.exit`](dotz.md#zexit-action-on-exit) (action on exit)
