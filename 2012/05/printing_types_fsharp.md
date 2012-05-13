Pretty-Printing Types in F#
===

This year I took a compilers course at my university - we implemented a compiler for a simply pascal like language using OCaml. Whilst I grew to like OCaml (if not its compiler) there was still something that bugged me; namely the `print.ml` which was used to format types into strings:

_print.ml_
```ocaml
type stmt = 
	| Skip 
  	| Seq of stmt list
  	| Assign of expr * expr
	| Print of expr
	| Newline
	| IfStmt of expr * stmt * stmt
	| WhileStmt of expr * stmt

let rec fStmt = 
  function
	| Skip -> 
		fStr "(SKIP)"
	| Seq ss -> 
		fMeta "(SEQ$)" [fTail(fStmt) ss]
	| Assign (e1, e2) -> 
		fMeta "(ASSIGN $ $)" [fExpr e1; fExpr e2]
	| Print e -> 
		fMeta "(PRINT $)" [fExpr e]
	| Newline -> 
		fStr "(NEWLINE)"
	| IfStmt (e, s1, s2) ->
		fMeta "(IF $ $ $)" [fExpr e; fStmt s1; fStmt s2]
	| WhileStmt (e, s) -> 
		fMeta "(WHILE $ $)" [fExpr e; fStmt s]
```

That's right, for every single type you have to have a formatter function that formats the type. Surely you can do better? Aparently in OCaml [you can't](http://stackoverflow.com/questions/7380755/ocaml-get-values-type-name) as type information is lost at runtime. Enter F#! F# is a .NET language also from the ML family that is based heavily on OCaml - it has runtime reflection so you can define a function that will give you a nicely formatted version of the type without having to suffer the above code explosion:

```fsharp
open Microsoft.FSharp.Reflection
open System

let rec fmt(x):string =
    let t = x.GetType()

    (* if it's a list just print it *)
    if t.Name.StartsWith("FSharpList") then
        x.ToString()

    (* else if union type print like so (NAME arg1 arg2 arg3) *)
    else if FSharpType.IsUnion(t) then
        let union, fields = FSharpValue.GetUnionFields(x, t)
        (* if no args don't include parens *)
        if fields.Length = 0 then
            union.Name
        else
            let fieldsStr = String.Join(" ", Array.map fmt fields)
            sprintf "(%s %s)" union.Name fieldsStr
    else
        x.ToString()
```

Isn't that better :)

---

**Tags:** ocaml, fsharp, reflection

**Date:** 13/05/2012 