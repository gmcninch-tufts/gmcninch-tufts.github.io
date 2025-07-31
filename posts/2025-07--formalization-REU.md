---
title   : Summer 2025 REU - Formalization 
date    : 2025-07-31
tags    :
  - lean
  - formalization
  - linear-algebra
summary : |
  Description of the work of the formalization project in the VERSEIM REU for Summer 2025.
---

I supervised a summer REU project in the [VERSEIM
REU](https://sites.tufts.edu/verseimreu/) at Tufts University. The
project aimed to learn about the use of the [Lean proof-assistant /
programming language](https://leanprover-community.github.io/) in
formalizing mathematics, especially in the context of linear algebra.

More precisely, we used tools found in
[mathlib](https://github.com/leanprover-community/mathlib4/); here is
how mathlib describes itself:

> Mathlib is a user maintained library for the Lean theorem prover. It
> contains both programming infrastructure and mathematics, as well as
> tactics that use the former and allow to develop the latter.

The group's work can be found in the [project's
github repository](https://github.com/gmcninch-tufts/VERSEIM-2025).


Here I'm going to give examples of results formalized by the students
during the summer program; (the actual proof(s) are elided here --
that is what the statement `sorry` is all about; they can be found in
the [repo](https://github.com/gmcninch-tufts/VERSEIM-2025)).

We spent much of the time formalizing statements in *linear algebra*,
for example in the following setting:

``` haskell
import Mathlib.Tactic

noncomputable section     

open Module
open LinearMap
open LinearMap (BilinForm)

--  V, V₁ and V₂ are vector spaces over the field k 
variable  {k V V₁ V₂ :Type} [Field k] 
  [AddCommGroup V] [Module k V]
  [AddCommGroup V₁] [Module k V₁]
  [AddCommGroup V₂] [Module k V₂]

-- an isometry between (V₁,β₁) and (V₂,β₂) is a linear equivalence
-- V₁ ≃ₗ[k] V₂ together with a proof that this equivalence is compatible 
-- with the given bilinear forms

structure Isometries
  (β₁:BilinForm k V₁) 
  (β₂:BilinForm k V₂)
  where
    equiv : V₁ ≃ₗ[k] V₂      -- the linear equivalence
    compat : ∀ (x y : V₁),  -- the compatibility assertion
	  β₁ x y = β₂ (equiv x) (equiv y) 

-- Notation so that `V₁ ≃[k,β₁,β₂] V₂` denotes `Isometries β₁ β₂` 
notation:100 lhs:100 "≃[" field:100 "," lhb:100 "," 
  rhb:100 "]" rhs:100 => 
  Isometries (k:= field) (V₁ := lhs) (V₂ := rhs) lhb rhb
```

- *isometries via bases*

  > If $(V_1,\beta_1)$ and $(V_2,\beta_2)$ are pairs consisting of a
  > finite dimensional vector space together with a bilinear form, then
  > there is an isometry between $V_1$ and $V_2$ if and only if there
  > are bases $b_1$ of $V_1$ and $b_2$ of $V_2$ such that the matrix of
  > $\beta_1$ with respect to $b_1$ is the same as the matrix of
  > $\beta_2$ with respect to $b_2$.

  Here is the formal statement:

  ``` haskell
  theorem equiv_via_matrices {ι:Type} [Fintype ι] [DecidableEq ι]
    (β₁ : BilinForm k V₁)   (β₂ : BilinForm k V₂) 
    (b₁ : Basis ι k V₁)
    :  Nonempty (V₁ ≃[k,β₁,β₂] V₂)  ↔  ∃ b₂:Basis ι k V₂,
      (BilinForm.toMatrix b₁ β₁) =  (BilinForm.toMatrix b₂ β₂) := by sorry

  ```

- *characterization of non-degenerate alternating forms*

  > Any two non-degenerate alternating bilinear forms on spaces of the
  > same dimension are equivalent.


  ``` haskell
  def alternate_iso 
    {β₁: BilinForm k V₁} {β₂: BilinForm k V₂} 
    (balt₁: IsAlt β₁) (balt₂: IsAlt β₂)
    (hd₁: β₁.Nondegenerate) (hd₂: β₂.Nondegenerate) 
    [FiniteDimensional k V₁] [FiniteDimensional k V₂]
    (h: Module.finrank k V₁ = Module.finrank k V₂) : 
	V₁ ≃[k,β₁,β₂] V₂ := by 
    sorry
  
  ```

- *characterization of reflexive forms*
   
  > A bilinear form $β$ is *reflexive* if $β(x,y) = 0 \Rightarrow
  > β(y,x) = 0$ for every $x,y \in V$.
  >
  > Any reflexive form is either alternating -- i.e. $β(x,x) = 0$ for
  > every $x$ -- or symmetric  -- i.e. $β(x,y) = β(y,x)$ for all $x,y$.

  ``` haskell
  theorem refl_is_alt_or_symm {β: BilinForm k V} (h: β₁.IsRefl) 
    [FiniteDimensional k V] :
    β.IsAlt ∨ β.IsSymm := by sorry
  ```

- *Cassels-Pfister Theorem*

  Finally, there is also a formalized proof of the Cassels-Pfister
  Theorem (see [Elman, Karpenko & Merkurjev, "The algebraic and
  geometric theory of quadratic forms", Theorem 17.3]). This can be
  found in [the repository,
  here](https://github.com/gmcninch-tufts/VERSEIM-2025/tree/main/VERSEIM2025/Forms/RationalFunctionFields).
