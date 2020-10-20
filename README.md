# The Style Guide

1. Define meaningful types for every variant and polymorphic variant you introduce. For example, when defining `MergeBranch` you would create `type branchName = string;` and the definition would look like: `MergeBranch(branchName)` instead of `MergeBranch(string)`
2. Define type signatures explicitly unless you're sure that they should not be defined.
3. Use named parameters if a function has multiple parameters of same type.
4. Tend to use named parameters more often.
5. Imperative code (i.e. the `ref` keyword) should be used only with a very strong reason.
6. Default to using non polymorphic variants. (`type rgb = Red | Green | Blue;` over `type rgb = [`Red | `Green | `Blue];`)
7. Prefer using uncurried functions over curried (i. e. *`reduceU` over `reduce`* in Belt.List*)*, it will help the compiler to produce cleaner error messages in cases when there is a parameters mismatch, which happens quite often during refactoring.
8. All new components should be Jsx3 components.
9. Unlike Javascript, Reason has great equality checking. Use `things == []` over `Belt.List.length(things) == 0` and similar.
10. Put multiline styles in the top of the file. 
11. When deciding between `thing->function->function` and `function(thing)->function` read it out and pick the one that makes more sense in a natural language, i.e. `Firebase.firestore(firebase)->Firebase.root("main")` and `maybeSomething->Belt.Option.getWithDefault("definitelySomething")`
12. Avoid switch-like funs 

    ```jsx
    ~~let map = f =>
      fun
      | Some(v) => Some(f(v))
      | None => None;~~
    ```
13. Clean up TODOs before merging the pull request. Either do them, rephrase with a generic comment or create an asana task. If none of those 3 solutions suites your case use FIXME instead.
14. When working with JSON create decoders and encoders with the `bs-json` lib

    ```bash
    Json.Decode.(field("token", string, "myToken"));

    Json.Encode.(object_([
                  ("token", string("myToken")),
                  ("returnSecureToken", bool(true)),
                ]);
    ```

    see [more about using variants](https://stackoverflow.com/questions/50908342/convert-json-field-to-reasonml-variant) and [this blog post](https://itnext.io/decoding-nested-json-objects-in-reasonml-with-bs-json-4cab75fbe308) for more examples.
