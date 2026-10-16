# Cut

Cut is the intermediate representation every front end hands Meadow's back end: a sequent calculus, typed only by how values are represented, which `meadow cut` lowers to AxCut and runs on Glade or compiles natively through Silo. Its definition is `docs/CUT.md` in [meadow](https://github.com/meadow-lang/meadow).

This package is Cut as a [Lingua](https://github.com/meadow-lang/Lingua) language. It holds what a front end written in Meadow builds a program with, and the text `meadow cut` reads. It knows no front end: MeadowBoot lowers Meadow's core to it, and Idyll's front end its own.

```meadow
use Cut as C

fun hello () =
  runSt (\() ->
    let out = C.builder () in
    let body = C.give out C.nowhere (C.str out C.nowhere "hi") (C.cont out C.nowhere "k") in
    let main = C.defDecl out C.nowhere "app:Main/main" [] [C.param out C.nowhere "k" "ptr"] body in
    C.text (C.finished out (C.file out C.nowhere "app:Main/main" "str" [main])))
```

`hello ()` is:

```
cut 0
entry app:Main/main
answer str

def app:Main/main (; k: ptr) =
  <"hi" | k>
```

and `meadow cut hello.cut` prints `hi`.

## Using it

```toml
[dependencies]
Cut = { git = "https://github.com/meadow-lang/Cut.git", rev = "<commit>" }
```

The builders take the program being written (`out`, from `builder` to `finished`) and where in the source each piece came from (`nowhere` where it came from nowhere). `text` writes a whole program; `textOf` writes some of its declarations, for a compiler that writes a unit at a time. The text format is versioned by its first line, `cut 0`, and may change with meadow until 1.0.
