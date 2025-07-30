---
title   : Summer 2025 REU
date    : 2025-07-30
tags    :
  - lean
  - formalization
  - linear-algebra
summary : |
  Description of REU work for Summer 2025.
---

I supervised a summer REU project. The project used the [Lean
proof-assistant / programming language](https://leanprover-community.github.io/) to 
formalize some statements in mathematics.

We used tools found in
[mathlib](https://github.com/leanprover-community/mathlib4/); here is
how mathlib describes itself:

> Mathlib is a user maintained library for the Lean theorem prover. It
> contains both programming infrastructure and mathematics, as well as
> tactics that use the former and allow to develop the latter.

The full output of the project can be found in the [project's github
repository](https://github.com/gmcninch-tufts/VERSEIM-2025).


Here are some examples of results formalized during the summer program:

The students gave a formalized proof (see the theorem
`equiv_via_matrices` below) of the result showing the following:

> if $(V_1,\beta_1)$ and $(V_2,\beta_2)$ are pairs consisting of a
> finite dimensional vector space together with a bilinear form, then
> there is an isometry between $V_1$ and $V_2$ if and only if there
> are bases $b_1$ of $V_1$ and $b_2$ of $V_2$ such that the matrix of
> $\beta_1$ with respect to $b_1$ is the same as the matrix of
> $\beta_2$ with respect to $b_2$.

(I'm eliding the proof here -- that is what the statement `sorry` is
all about...)

``` haskell
import Mathlib.Tactic

open Module
open LinearMap
open LinearMap (BilinForm)

variable  {k V₁ V₂ :Type} [Field k] 
  [AddCommGroup V₁] [Module k V₁]
  [AddCommGroup V₂] [Module k V₂]

structure equiv_of_spaces_with_form
  (β₁:BilinForm k V₁) 
  (β₂:BilinForm k V₂)
  where
    equiv : V₁ ≃ₗ[k] V₂
    compat : ∀ (x y : V₁), β₁ x y = β₂ (equiv v₁) (equiv v₂) 

notation:100 lhs:100 "≃[" field:100 "," lhb:100 "," rhb:100 "]" rhs:100 => 
  equiv_of_spaces_with_form (k:= field) (V₁ := lhs) (V₂ := rhs) lhb rhb

theorem equiv_via_matrices {ι:Type} [Fintype ι] [DecidableEq ι]
  (β₁ : BilinForm k V₁)   (β₂ : BilinForm k V₂) 
  (b₁ : Basis ι k V₁)
  :  Nonempty (V₁ ≃[k,β₁,β₂] V₂)  ↔  ∃ b₂:Basis ι k V₂,
    (BilinForm.toMatrix b₁ β₁) =  (BilinForm.toMatrix b₂ β₂) := by sorry

```

The students also formalized the proof of the statement that any two
non-degenerate alternating bilinear forms on spaces of the same
dimension are equivalent.

``` haskell
noncomputable def alternate_iso {B₁: BilinForm k V₁} (B₂: BilinForm k V₂) 
  (balt₁: IsAlt B₁) (balt₂: IsAlt B₂)
  (hd₁: B₁.Nondegenerate) (hd₂: B₂.Nondegenerate) 
  [FiniteDimensional k V₁] [FiniteDimensional k V₂]
  (h: Module.finrank k V₁ = Module.finrank k V₂) : V₁ ≃[k,B₁,B₂] V₂ := by 
  sorry

```

A bilinear form $β$ is *reflexive* if $β(x,y) = 0 \Rightarrow β(y,x) =
0$ for every $x,y \in V$.  The students formalized the proof that any
reflexive form is either alternating (i.e. $β(x,x) = 0$ for every $x$)
or symmetric (i.e. $β(x,y) = β(y,x)$ for all $x,y$):

``` haskell
theorem refl_is_alt_or_symm {B: BilinForm k V₁} (h: B.IsRefl) 
  [FiniteDimensional k V₁] :
  B.IsAlt ∨ B.IsSymm := by sorry
```

Finally, there is also a formalized proof of the Cassels-Pfister
Theorem (see Elman, Karpenko & Merkurjev, "The algebraic and geometric
theory of quadratic forms" Theorem 17.3). This can be found in [the
repository,
here](https://github.com/gmcninch-tufts/VERSEIM-2025/tree/main/VERSEIM2025/Forms/RationalFunctionFields).
