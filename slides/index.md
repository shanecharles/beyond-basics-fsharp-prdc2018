- title : Beyond the Basics with F#
- description : Now where do I go?
- author : Shane Charles
- theme : night
- transition : default

***

### Beyond the Basics with F#

![F#](images/fsharp512.png)

***

<!-- .slide: class="two-floating-elements" -->
### About me

<div class="two-floating-elements">
<img src="images/mvp_logo_vertical.png" alt="mvp" style="height:145px;border:0;margin: auto;"/>
    <ul>
     <li>Shane Charles</li>
<li>White Light Computing, Inc.</li>
<li>Functional programming enthusiast</li>
<li>Board member for Winnipeg .Net UG</li>
</ul>
</div>




    type ContactType = | Twitter | Blog | GitHub

    let getContactInfo = function
      | Twitter -> "@dead_stroke"
      | Blog    -> "https://geekeh.com"
      | GitHub  -> "shanecharles"

***
### Path

- Whirlwind Tour of F#
- Recursion
- Active Patterns
- Domain Models
- Computation Expressions
- Azure Function
- Summary

***
### Why Functional

- Step outside our known world
- Immutability
 - Different way of thinking
 - Parallel processing
 - Predictability
- smaller pieces for a larger solution

***
### F#

- General purpose language on the CLR
- Functional First
- Immutable by default
- Structural equality by default
- Type Inference

---
### Keywords

- `let`
- `use`
- `rec`
- `type`

---
### Hello, Operator?

    // (=) equality with value binding
    let name = "F#" 
    
    // (=) function definition
    let sayHello n = printfn "Hello %s" n 
    
    // (|>) Forward pipe for left to right readability
    name |> sayHello  

    // (>>) Compose functions
    let add1mult2 = ((+) 1) >> ((*) 2)

    // (::) Cons operator, append at beginning of list
    let xs = 42 :: [ 2; 24 ]

---
### Necessary Evil

    // (<-) Mutation!
    let req = System.Net.WebRequest.Create "https://google.ca"
    req.Timeout <- 30000

---
### Reading F#

![Arrows](images/follow_the_arrows.png)

---
### Tuples

> Grouping of seemingly unrelated values.

    let success = (true, 5)
    let (isSuccess, value) = success

---
### Tuples Don'ts

- No more than 3 values
- Don't pass them as data

---
### Records

> Grouping of read-only named values.

    type Order = { OrderId    : int
                   CustomerId : int
                   OrderItems : string list }
    
---
### Discrimanted Unions

> A choice of one or the other.


    type ValidationResult = 
      | OK
      | Fail of (string * string) list
      | Error of System.Exception

---
### Option

- Avoid nulls


    type Option<'a> = 
        | Some of 'a
        | None

---
### Currying

> Functions accepting multiple arguments are converted to multiple functions accepting one argument.

---
### Higher Order Functions

> A function that returns a function or takes a function as an argument.

***
### Recursion

> A function that calls itself to apply the same rules repeatedly.

---
### Fibonacci

- It works, performant?


    let fib (n : int64) =
      let rec loop x =
        if x <= 1L then x
        else
          loop (x-1L) + loop (x-2L)
      loop n

---
### Recursive Optimizations

- Tail Call Optimized
- Memoization

---
### Recursion Alternatives

- Seq.map
- Seq.filter
- Seq.scan
- Seq.chunkBySize
- List.partition
- Seq.fold
- Seq.unfold

***
### Pattern Matching

> Deconstruct all the things.

    match System.Decimal.TryParse "3.14159" with
    | true, v  -> v
    | false, _ -> failwith "Womp womp" 

---
### Matching Lists

    let printFirstItems (order : Order) = 
      match order.OrderItems with
      | []          -> printfn "No Items"
      | [x]         -> printfn "Only Item: %s" x
      | x :: y :: _ -> printfn "First 2: %s and %s" x y

---
### Matching Discriminated Unions

    let printResult result =
      match result with
      | OK      -> printfn "Everything Worked"
      | Fail [] -> printfn "It's fine"
      | Fail es -> printfn "Errors were reported:" 
                       es |> List.iter 
                            (fun (p,m) -> printfn "- %s: %s" p m)
      | Error e -> printfn "!!System crashed... Reboot!!"
                       printfn "%A" e

***
### Active Patterns

> Active patterns allow us to extend the default matching capabilities in F#.

- Hide some of the ugly code.

---
### 4 Types of Active Patterns

- Single Case Transform
- Partial Pattern
- Parameterized Pattern
- Complete Pattern

---
### Partial Pattern / Parameterized


    open System.Text.RegularExpressions

    let (|RegexMatch|_|) (pattern : string) v =
      let m = Regex.Match (v, pattern)
      if m.Success
      then [ for x in m.Groups do 
                 yield x.Value ] 
           |> List.skip 1   
           |> Some
      else None

---
### Active Pattern Warning

- Don't go to the database
- No long running processes
- Maximum 7 choices for Complete Pattern

***
### Domain Modelling

- Single case discriminated unions
- Make illegal states unrepresentable

---
### Ensure Correctness

    type EmailAddress = string 

    type VerifiedAddress = VerifiedAddress of EmailAddress

    type EmailContact =
        | Unverified of EmailAddress
        | Verified of VerifiedAddress

    let verifyEmailAddress (emailAddress * isVerified) =
        if isVerified then Some (VerifiedAddress emailAddress)
        else None

    let sendPasswordReset (VerifiedAddress email) = 
        printfn "Password reset sent to: %s" email


***
### Computation Expressions

> a convenient syntax for writing computations that can be sequenced and combined
> using control flow constructs and bindings.

---
### Wait?

![what](images/what.gif)

---

### Tough to Explain not Too Difficult to Use

- Builders or workflows
- `async { }`
- `seq { }`
- `!` bang notation

---
### Writing Our Own

- Create a class
- Minimum required methods
 - `bind`
 - `return`

***
### F# Azure Function

- Azure Function v1: 
 - super easy to get started
- Azure Function v2: 
 - Current Status: bring the pain

---
### No More F# Script Support

- Issues with dotnetcore 2 and FSI
- Precompiled Only
- No templates as of September 2018
- Lot's of steps
- Tears
- It will get better

---
### Brief Summary of Pain (MacOS)

- Install 
 - dotnetcore
 - azure-functions-core-tools
 - azure-cli
 - vs code
- dotnet new F# dll
- Manual configuration
- azure command line publish bug supporting only C#
 - 5 stages of grief
- dotnet publish
- Deploy via VS Code
- Profit?

***
### Extra Resources

- F# for Fun and Profit 
 - https://fsharpforfunandprofit.com/posts/property-based-testing/
- Precompiled Azure Functions
 - https://discardchanges.com/post/building-azure-functions-with-precompiled-fsharp/
- FSharpx
 - https://github.com/fsprojects/FSharpx.Extras
  - Recommend: Prelude.fs

***

### Thank You


    type ContactType = | Twitter | Blog | GitHub

    let getContactInfo = function
      | Twitter -> "@dead_stroke"
      | Blog    -> "https://geekeh.com"
      | GitHub  -> "shanecharles"

***
