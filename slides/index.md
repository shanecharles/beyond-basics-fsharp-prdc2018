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

***

#### Value

    let hello = "hello"


#### Functions

    let hello () = printfn "Hello F#"


---

### Records

    type Wrestler = { Name : string; SignatureMove : string } 

    let w1 = { Name = "Zoya the Destroya"
               SignatureMove = "Hammer & Sickle" }

---

### Discrimanted Unions

    type WrestlerType = 
      | Face
      | Heel 
      | Neutral



***

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
