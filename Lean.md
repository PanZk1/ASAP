# Sketch of Lean

> LEAN has a small trusted kernel is based on "dependent type theory": 
> - automated tools and methods in a framework that supports user interaction.
> - the construction of fully specified axiomatic proofs.
> 
> LEAN has rich API 
> - can be used to embed it into other systems.
> - can be used to export Lean proofs to other systems based on similar foundations (e.g. Coq and Matita)
> 
> It is currently being used to formalize category theory, homotopy type theory, and abstract algebra.

---   
Automated theorem proving
1. provide means of establishing the validity of formulas in propositional and first-order logic: 
   [ connect note ]
>provers: Resolution theorem provers,tableau theorem provers, fast satisfiability solvers, and so on 
2. provide search procedures and decision procedures for specific languages and domains, such as linear or nonlinear expressions over the integers or the real numbers.
> Architectures like SMT(“satisfiability modulo theories”) combine domain-general search methods with domain specific procedures.
  
### kernel
* first layer: type checker and APIs for creating and manipulating terms,declarations, and the environment. 
* second layer: provides additional components such as inductive families.
  
### Terms: 
  The term language is a dependent λ-calculus.
> a free variable(a local constant), a bound variable
> 
> a constant,a function application f t, a lambda abstraction λx: A,t
> 
> a function space Πx: A,B
> 
> a sort Type u
> 
> a meta-variable
> 
> a macro $m[t_1,t_2,...,t_n]$
>
> Calculus of Inductive Constructions, or CIC
>
> dependent type theory
> 
Sorts: The sorts Type u are used to encode the infinite sequence of universes
Type0, Type1, Type2, . . . An explicit universe term is of the form $s^k z$(for k ≥ 0),where z denotes the base universe zero, and s denotes the successor universe operator.

Dependent type theory
  allowing us to express complex mathematical assertions, write complex hardware and software specifications, and reason about both of these in a natural and uniform way --Calculus of Inductive Constructions
"Introduction to the Calculus of Inductive Constructions"
https://inria.hal.science/hal-01094195/document


 Type theory have an infrastructure that helps us manage and keep track of the various kinds of mathematical objects we are working with.

 Lean allows users to provide definitions and theorems using a declarative style resembling Mizar and Isabelle/Isar. Lean also provides tactics as an alternative (more imperative) approach to constructing (proof) terms as in Coq, HOL-Light, Isabelle and PVS. Moreover, the declarative and tactic styles can be freely mixed together.

 ## setup
https://github.com/leanprover/lean4/blob/master/doc/setup.md


elan: Lean version manager
a small tool for managing your installations of the Lean theorem prover. It places lean and lake binaries in your PATH that automatically select 
Manual Installation : curl https://raw.githubusercontent.com/leanprover/elan/master/elan-init.sh -sSf | sh 

https://github.com/leanprover/elan
https://gist.github.com/jcommelin/1d45a0ea7a84a87db8a28a12e93cac32


>How to View the Directories in PATH
>echo $PATH
>How to Add a Directory to PATH in Linux
>https://phoenixnap.com/kb/linux-add-to-path

## the repository for Lean 4
https://github.com/leanprover/lean4


the book Theorem Proving in Lean:
Many Lean users consider it to be the Lean Bible
sketch:https://pp.ipd.kit.edu/uploads/publikationen/demoura21lean4.pdf
https://link.springer.com/chapter/10.1007/978-3-030-79876-5_37


## introduce the Lean language
the def keyword
  : define functions followed by its name, a parameter list, return type, and body
> def or (a b : Bool) :=
> match a with
>| true => true
>| false => b

#check <term> to inspect the type of term, and #eval <term> to evaluate it
#check or true false -- Bool
#eval or true false -- true

Lean has a hygienic macro system and comes equipped with many macros for
commonly used idioms. 
> define the function or 
> def or : Bool → Bool → Bool
>| true, _ => true
>| false, b => b

In Lean,a theorem is a definition whose result type is a proposition. 
The following simple theorem about the definition above
>theorem or_true (b : Bool) : or true b = true :=
>rfl

>The constant rfl has type ∀ {α : Sort u} {a : α}, a = a
>the inferred values for α and a
>are Bool and or true b
or true b is definitionally equal to b

Define the recursive datatype "BoolExpr" for representing Boolean expressions using the command inductive.(generates constructors BoolExpr.var, BoolExpr.val, BoolExpr.or, and BoolExpr.not)
>inductive BoolExpr where
>| var (name : String)
>| val (b : Bool)
>| or (p q : BoolExpr)
>| not (p : BoolExpr)
write a basic “simplifier” for Boolean expressions as follows
>def simplify : BoolExpr → BoolExpr

>| BoolExpr.or p q => mkOr (simplify p(simplify q)

>| BoolExpr.not p => mkNot (simplify p)
>| e => e
>where
>mkOr : BoolExpr → BoolExpr → BoolExpr
>| p, BoolExpr.val true => BoolExpr.val true
>| p, BoolExpr.val false => p
>| BoolExpr.val true, p => BoolExpr.val true
>| BoolExpr.val false, p => p
>| p, q => BoolExpr.or p q
>mkNot : BoolExpr → BoolExpr
>| BoolExpr.val b => BoolExpr.val (not b)
>| p => BoolExpr.not p

Given a context that maps variable names to Boolean values
>abbrev Context := AssocList String Bool
>
>def denote (ctx : Context) : BoolExpr → Bool
>
>| BoolExpr.or p q => denote ctx p || denote ctx q
>| BoolExpr.not p => !denote ctx p
>| BoolExpr.val b => b
>| BoolExpr.var x => if let some b := ctx.find? x then b else false

if let p := t then a else b is a macro that expands into match t with | p => a | _
=> b. The term ctx.find? x is syntax sugar for AssocList.find? ctx x.

Use tactics for constructing proofs and terms.(use the keyword "by" to switch into tactic mode)
The "simp" tactic implements an extensible simplifier, and is one of the most popular tactics in "mathlib"
>def denote_simplify (ctx : Context) (p : BoolExpr)
>: denote ctx (simplify p) = denote ctx p :=
>by induction p with
>| or p q ih1 ih2 => simp [ih1, ih2]
>| not p ih => simp [ih]
>| _ => rfl


 Next I will describe "Typeclass Resolution" of lean4,and describe various methods in dependent type theory such as discussing term rewriting, and Lean's automated methods for simplifying terms and expressions automatically ; discussing methods of elaboration and type inference, which can be used to support flexible forms of algebraic reasoning.