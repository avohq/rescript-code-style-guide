# The Avo Rescript Style Guide

At [Avo](https://www.avo.app/) we use [Rescript](https://rescript-lang.org/) as our primary programming language. To allow the development team to deliver better code and reviews, we've come up with a set of guidelines:

1. File names, just like modules, should be in PascalCase: `EventsList.res`
1. Define meaningful types for every variant and polymorphic variant you introduce. For example, when defining `MergeBranch` you would create `type branchName = string` and the definition would look like: `MergeBranch(branchName)` instead of `MergeBranch(string)`
1. Define type signatures explicitly unless you're sure that they should not be defined. Even when the compiler can infer the type it still is often useful to state it explicitly for readability of the code, especially outside of the IDE.
1. Use named parameters if a function has multiple parameters of same type. This will prevent accidentally mixing up the parameters when calling the function.
1. Tend to use named parameters more often. This is advised for readability.
1. Imperative code (i.e. the `ref` keyword) should be used only with a very strong reason.
1. Default to using non polymorphic variants (```type rgb = Red | Green | Blue``` over ```type rgb = [#Red | #Green | #Blue]```). We think of polymorphic variants as a lesser-typed approach and prefer to have stronger typing. 
1. Polymorphic variants should be lowercase. `#success` rather than `#Success`
1. Prefer using uncurried functions over curried (i. e. *`reduceU` over `reduce`* in Belt.List*)*, it will help the compiler to produce cleaner error messages in cases when there is a parameters mismatch, which happens quite often during refactoring. Another benefit is that it performs better on runtime.
1. Put multiline styles in the top of the file. 
1. When deciding between `thing->function->function` and `function(thing)->function` read it out and pick the one that makes more sense in a natural language, i.e. `Firebase.firestore(firebase)->Firebase.root("main")` and `maybeSomething->Belt.Option.getWithDefault("definitelySomething")`
1. When working with JSON create decoders and encoders with the `bs-json` lib
    ```bash
    Json.Decode.(field("token", string, "myToken"))

    Json.Encode.(object_([
                  ("token", string("myToken")),
                  ("returnSecureToken", bool(true)),
                ])
    ```
    see [more about using variants](https://stackoverflow.com/questions/50908342/convert-json-field-to-reasonml-variant) and [this blog post](https://itnext.io/decoding-nested-json-objects-in-reasonml-with-bs-json-4cab75fbe308) for more examples.

    We tried other approaches but they all tend to end up with similar amount of work and less constrol.
1. When defining functions keep the types definition in the function, not in the variable, to allow more freedom for type inferrence, i.e. `let a = (b: string, c: int): string => b === "ok"` over `let a: ((string, int) => boolean) = (b, c) => b === "ok"`
1. **ALWAYS** use `Js.Array2` and `Js.String2`. **NEVER** use `Js.Array` and `Js.String`. This is required to keep the `->` piping consistent. A nice suggestion of how to achieve that can be found here https://github.com/avohq/reasonml-code-style-guide/issues/2
1. Use `List`s for resursive data structures. Use `Array` otherwise. I.e.
  ```
  let rec len = (myList: list('a)) =>
    switch myList {
      | [] => 0
      | [_, ...tail] => 1 + len(tail)
    }
  ```
1. Do not overuse reduce. Consider other options, like `map` and `flatMap` to achieve the result. Prefer reduce over `ref` though.
1. Open `Belt` globally. It saves a lot of typing.
1. Prefer `Belt.Result` over throwing exceptions. This would make the execution flow more homogeneous. Exceptions are generally considered to be avoided nowadays.
1. Don't put more than 3 React components in a single file. Use separate files for big components or components that are used in multiple places.
1. Use [`rescript-promise`](https://github.com/ryyppy/rescript-promise#usage)(`Promise.then(…)`) over `Js.Promise`. The bindings are nicer, have stricter error handling and are recommended as of Rescript 10.1.

### Firebase Cloud Functions

When creating cloud functions, only define a single function in a file, defined as `let handle = {...}`

In `Index.re` (which relates to `index.js` required by google cloud functions)
refer to the function with 
`[Type of Trigger][Name of Function] = File.handle`

Types of Triggers
   - trigger (Firestore document trigger)
   - https
   - pubsub

Example:
```
let httpsGetCustomers = GetCustomersEndpoint.handle
let triggerOnCustomerAdded = OnCustomerAdded.handle
let pubsubDeleteUserData = DeleteUserData.handle
```
The name defined here will be the name of the cloud function itself and helps us to easily map code with function for debugging and logging.
