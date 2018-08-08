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
- Composition
- Active Patterns
- Railway Oriented Programming
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

    // (::) Cons operator, append at beginning of list
    let xs = 42 :: [ 2; 24 ]

    // Lambda
    let add1 = (fun x -> x + 1)

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

    type Order = { OrderId : int
                   CustomerId : int
                   OrderItems : string list }
    
    let ord1 = { OrderId=1
                 CustomerId=1
                 OrderItems=["Fries"] }
---

### Record Update Shorthand

    // False?
    ord1 = { ord1 with 
                OrderItems = "Car" :: ord1.OrderItems }


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

### Currying

> translating the evaluation of a function with multiple arguments into 
> a sequence of functions, each with a single argument.

- Evaluation occurs once ALL arguments are satisfied

---
### Basic Example
    
    // int -> int -> int
    let add x y = x + y

    // int -> int
    let increment x = add 1

    // true
    6 = increment 5

---
### Not so Basic Example


    
### Higher Order Functions 

> Receiving functions as arguments or returning functions.

- Dependency Injection?

---

### Partial Application

    type Order = { OrderId : int; CustomerId : int }

    let tryLookup (Orders : Order seq) key = 
      orders |> Seq.tryFind (fun ord -> ord.OrderId = key)

    let lookup = tryLookup [{OrderId=1; CustomerId=1}; {OrderId=2; CustomerId=1}]

---

    type Order = { OrderId : int; CustomerId : int }

    let tryLookup (orders : Order seq) key =
        let d = orders 
                |> Seq.map (fun ord -> ord.OrderId, ord) 
                |> dict
        d |> Seq.tryFind key
        

***

### 

### Extra Resources

- F# for Fun and Profit 
 - https://fsharpforfunandprofit.com/posts/property-based-testing/

***

### Thank You


    type ContactType = | Twitter | Blog | GitHub

    let getContactInfo = function
      | Twitter -> "@dead_stroke"
      | Blog    -> "https://geekeh.com"
      | GitHub  -> "shanecharles"

***
