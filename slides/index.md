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

- F# Refresh
- Curry Power
- Higher Order Functions
- Composition
- Active Patterns
- Computation Expressions
- Cloud
- Summary

***

### F#

- General purpose language on the CLR
- Functional First
- Immutable by default
- Structural equality by default

---

### Keywords

- `let`
- `use`
- `rec`

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

### Tuples

> Grouping of seemingly unrelated values.

    let success = (true, 5)
    let (isSuccess, value) = success

    let isOk, digit = System.Int32.TryParse "2"

---

### Records

> Grouping of read-only named values.

    type Order = { OrderId    : int
                   CustomerId : int
                   OrderItems : string list }
    
    let ord1 = { OrderId=1
                 CustomerId=1
                 OrderItems=["Fries"] }
---

### Record Update Shorthand

    let ord2 = { ord1 with 
                   OrderItems = "Car" :: ord1.OrderItems }

---
### Discrimanted Unions

> A choice of one or the other.


    type ValidationResult<'a> = 
      | OK of 'a
      | Fail of (string * string) list
      | Error of System.Exception

***
### Currying

> translating the evaluation of a function with multiple arguments into 
> a sequence of functions, each with a single argument.

- Evaluation occurs once ALL arguments are satisfied
- Create new functions through Partial Application

---
### Basic Example
    
    // int -> int -> int
    let add x y = x + y

    // int -> int
    let increment x = add 1

    // true
    6 = increment 5

---
### Higher Order Functions

> A function that returns a function or takes a function as an argument.

---
### Inject Dependencies


    let validator (rules: (Order -> string option) seq) (o: Order) =
        rules |> Seq.choose (fun rule -> rule item)

---
### Composition

- Combine functions to build new functions using: `>>`
 - Think lego blocks


***
### Pattern Matching

> Deconstruct all the things.

    match System.Decimal.TryParse "3.14159" with
    | true, v  -> v
    | false, _ -> failwith "Womp womp" 

---

### Matching Records and Lists

    let printFirstItems {Order.OrderItems=orderItems} = 
      match orderItems with
      | []          -> printfn "No Items"
      | [x]         -> printfn "Only Item: %s" x
      | x :: y :: _ -> printfn "First 2: %s and %s" x y

---

### Matching Discriminated Unions

    let printResult result =
      match result with
      | OK data -> printfn "Everything Worked for: %A" data
      | Fail [] -> printfn "It's fine"
      | Fail es -> printfn "Errors were reported:" 
                       es |> List.iter 
                            (fun (p,m) -> printfn "- %s: %s" p m)
      | Error e -> printfn "!!System crashed... Reboot!!"
                       printfn "%A" e

---
### Matching Types


    let crash () = 
      try
        failwith "This crash was intentional"
      with
      | :? System.DivideByZeroException as e 
            -> printfn "Div Zero: %s" e.Message
      | :? System.Exception as e             
            -> printfn "Exception: %s" e.Message



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
### Transform Pattern

    let (|DayMonth|) (date : System.DateTime) = 
      date.Day, date.Month

    let printSpecial = function
      | DayMonth (29, 2) -> printfn "Leap Day"
      | DayMonth (1, 1)  -> printfn "Infosec Birthdays"
      | _                -> ()

---
### Partial Pattern / Parameterized

    let (|StringsMatch|_|) (patterns : string seq) v =
      let c = v, StringComparison.CurrentCultureIgnoreCase
      if patterns |> Seq.exists (fun p -> p.Equals c)
      then Some (v.ToUpper())
      else None

---
### Complete Pattern

    let (|Shipping|Holiday|Weekend|) date =
      let holidays = [(1,1)]
      let (DayMonth dm) = date
      if holidays |> Seq.exists ((=) dm) then Holiday
      elif [DayOfWeek.Saturday; DayOfWeek.Sunday]
           |> Seq.exists ((=) date.DayOfWeek)
      then Weekend
      else Shipping

---
### Active Pattern Warning

- Don't go to the database
- No long running processes
- Maximum 7 choices for Complete Pattern

***
### Advanced Domain Modelling

- Single case discriminated unions
- Make illegal states unrepresentable

---
### 

    type Address = {
          Address1 : string
          City     : string 
        }

    type VerifiedAddress = VerifiedAddress of Address

    let verifiedAddressOnly (VerifiedAddress address) = 
        printfn "Verified Addresses: %A" address

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
### Asynchronous Computation

    let req = System.Net.HttpWebRequest.Create("https://google.ca")
    async { 
       use! resp = req.AsyncGetResponse ()
       printfn "Downloaded %0" resp.ResponseUri
    }

---
### Sequence

    let fib = 
       let rec fib' x y =
          seq {
             yield  y
             yield! fib' y (x + y)
          }
       fib' 0 1

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
- Cursing
- azure command line publish bug supporting only C#
- dotnet publish
- Deploy via VS Code
- Profit?

***

### Honourable Mentions

- Type Providers
- Property Based Testing
  - FsCheck

***
### Extra Resources

- F# for Fun and Profit 
 - https://fsharpforfunandprofit.com/posts/property-based-testing/
- Precompiled Azure Functions
 - https://discardchanges.com/post/building-azure-functions-with-precompiled-fsharp/

***

### Thank You


    type ContactType = | Twitter | Blog | GitHub

    let getContactInfo = function
      | Twitter -> "@dead_stroke"
      | Blog    -> "https://geekeh.com"
      | GitHub  -> "shanecharles"

***
