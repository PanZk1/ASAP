Theorem Proving in Lean 4

## Type Classes
https://leanprover.github.io/theorem_proving_in_lean4/type_classes.html

#### ToString
The polymorphic method toString has type {α : Type u} → [ToString α] → α → String.
Implement the instance for your own types and use chaining to convert complex values into strings.
>structure Person where
>  name : String
>  age  : Nat
>
>instance : ToString Person >where
>  toString p := p.name ++ >"@" ++ toString p.age
>
>#eval toString { name := >"Leo", age := 542 : Person }

### Local Instances
you can use the local modifier to indicate that they only have effect until the current section or namespace is closed, or until the end of the current file.

### Scoped Instances
This kind of instance is only active when you are inside of the namespace or open the namespace.
You can use the command open scoped <namespace> to activate scoped attributes but will not "open" the names from the namespace.
>structure Point where
> x : Nat
> y : Nat
>
>namespace Point
>
>scoped instance : Add Point where
>  add a b := { x := a.x + b.x, y := a.y + b.y }
>
>def double (p : Point) :=
>  p + p
>
>end Point
>
>open scoped Point -- activates instance `Add Point`
>#check fun (p : Point) => p + p + p
>
>-- #check fun (p : Point) => double p -- Error: unknown identifier 'double'

### Decidable Propositions

The Decidable type class can be used to infer a procedure that effectively determines whether or not the proposition is true.
The expression if p then a else b.
>def ite {α : Sort u} (c : Prop) [h : Decidable c] (t e : α) : α :=
>  Decidable.casesOn (motive := fun _ => α) h (fun _ => e) (fun _ => t)
>
>end Hidden
>
>dite : if h : c then t else e

With the classical axioms, we can prove that every proposition is decidable.
>open Classical

If p is a true closed expression, decide p will reduce definitionally to the Boolean true. On the assumption that decide p = true holds,of_decide_eq_true produces a proof of p. The tactic decide puts it all together: to prove a target p. 

#### Type Class Inference

You can change the order that type classes instances are tried by assigning them a priority. When an instance is declared, it is assigned a default priority value.

## The Conversion Tactic Mode
 This mode allows to travel inside assumptions and goals, even inside function abstractions and dependent arrows, to apply rewriting or simplifying steps.

 Basic navigation and rewriting
 prove example (a b c : Nat) : a * (b * c) = a * (c * b)
 >example (a b c : Nat) : a * (b * c) = a * (c * b) := by
 > conv =>
      -- |- a * (b * c) = a * (c * b)
>lhs
    -- |- a * (b * c)
>congr
    -- 2 goals : |- a and |- b * c
>  rfl
    -- |- b * c
> rw  [Nat.mul_comm]
>
>lhs navigates to the left hand side of a relation
>congr creates as many targets as there are (nondependent and explicit) arguments to the current head function (here the head function is multiplication).
>rfl closes target using reflexivity.

Navigation using the above commands can be tedious. One can shortcut it using pattern matching as follows:
>example (a b c : Nat) : a * (b * c) = a * (c * b) := by
> conv =>
> pattern b * c
>  rw [Nat.mul_comm]


NEXT I will add topics about Axioms and Computation, give some examples and try to connect the tech with the project.