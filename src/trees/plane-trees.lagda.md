# Plane trees

```agda
module trees.plane-trees where
```

<details><sumary>Imports</summary>

```agda
open import elementary-number-theory.natural-numbers

open import foundation.action-on-identifications-binary-functions
open import foundation.action-on-identifications-functions
open import foundation.dependent-pair-types
open import foundation.equivalences
open import foundation.function-types
open import foundation.identity-types
open import foundation.maybe
open import foundation.retractions
open import foundation.sections
open import foundation.universe-levels

open import lists.lists

open import trees.full-binary-trees
open import trees.w-types

open import univalent-combinatorics.standard-finite-types
```

</details>

## Idea

A {{#concept "plane tree" Agda=plane-tree WD="ordered tree" WDID=Q10396021}} is
a finite [directed tree](trees.directed-trees.md) that can be drawn on a plane
with the root at the bottom, and all branches directed upwards. More precisely,
a plane tree consists of a root and a family of plane trees indexed by a
[standard finite type](univalent-combinatorics.standard-finite-types.md). Plane
trees are also known as _ordered trees_.

The type of plane trees can be defined in several equivalent ways:

- The type of plane trees is the inductive type with constructor

  ```text
    (n : ℕ) → (Fin n → plane-tree) → plane-tree.
  ```

- The type of plane trees is the [W-type](trees.w-types.md)

  ```text
    𝕎 ℕ Fin.
  ```

- The type of plane trees is the inductive type with constructor

  ```text
    list plane-tree → plane-tree.
  ```

The type of plane trees is therefore the least fixed point of the
[list](lists.lists.md) functor `X ↦ list X`. In particular, `plane-tree` is the
least fixed point of the functor

```text
  X ↦ 1 + plane-tree × X.
```

The least fixed point for this functor coincides with the least fixed point of
the functor

```text
  X ↦ 1 + X².
```

Thus we obtain an equivalence

```text
  plane-tree ≃ full-binary-tree
```

from the type of plane trees to the type of
[full binary trees](trees.full-binary-trees.md).

## Definitions

### Plane trees

```agda
data plane-tree : UU lzero where
  make-plane-tree : {n : ℕ} → (Fin n → plane-tree) → plane-tree
```

### Plane trees as W-types

```agda
plane-tree-𝕎 : UU lzero
plane-tree-𝕎 = 𝕎 ℕ Fin
```

### Plane trees defined using lists

```agda
data listed-plane-tree : UU lzero where
  make-listed-plane-tree : list listed-plane-tree → listed-plane-tree
```

## Operations on plane trees

### The type of nodes, including leaves, of a plane tree

```agda
node-plane-tree : plane-tree → UU lzero
node-plane-tree (make-plane-tree {n} T) =
  Maybe (Σ (Fin n) (λ i → node-plane-tree (T i)))
```

### The root of a plane tree

```agda
root-plane-tree : (T : plane-tree) → node-plane-tree T
root-plane-tree (make-plane-tree T) = exception-Maybe
```

## Properties

### The type of listed plane trees is equivalent to the type of lists of listed plane trees

```agda
unpack-listed-plane-tree : listed-plane-tree → list listed-plane-tree
unpack-listed-plane-tree (make-listed-plane-tree t) = t

is-section-unpack-listed-plane-tree :
  is-section make-listed-plane-tree unpack-listed-plane-tree
is-section-unpack-listed-plane-tree (make-listed-plane-tree t) = refl

is-retraction-unpack-listed-plane-tree :
  is-retraction make-listed-plane-tree unpack-listed-plane-tree
is-retraction-unpack-listed-plane-tree l = refl

is-equiv-make-listed-plane-tree :
  is-equiv make-listed-plane-tree
is-equiv-make-listed-plane-tree =
  is-equiv-is-invertible
    ( unpack-listed-plane-tree)
    ( is-section-unpack-listed-plane-tree)
    ( is-retraction-unpack-listed-plane-tree)

equiv-make-listed-plane-tree : list listed-plane-tree ≃ listed-plane-tree
pr1 equiv-make-listed-plane-tree = make-listed-plane-tree
pr2 equiv-make-listed-plane-tree = is-equiv-make-listed-plane-tree
```

### The type of listed plane trees is equivalent to the type of full binary trees

Since `plane-tree` is inductively generated by a constructor
`list plane-tree → plane-tree`, we have an
[equivalence](foundation-core.equivalences.md)

```text
  list plane-tree ≃ plane-tree.
```

This description allows us to obtain an equivalence
`plane-tree ≃ full-binary-tree` from the type of plane trees to the type of
[full binary trees](trees.full-binary-trees.md). Indeed, by the above
equivalence we can compute

```text
  plane-tree ≃ list plane-tree
             ≃ 1 + plane-tree × list plane-tree
             ≃ 1 + plane-tree²
```

On the other hand, the type `full-binary-tree` is a fixed point for the
[polynomial endofunctor](trees.polynomial-endofunctors.md)
`X ↦ 1 + full-binary-tree × X`, since we have equivalences.

```text
  full-binary-tree ≃ 1 + full-binary-tree²
                   ≃ 1 + full-binary-tree × full-binary-tree
```

Since `full-binary-tree` is the least fixed point of the polynomial endofunctor
`X ↦ 1 + X²`, we obtain a `(1 + X²)`-structure preserving map

```text
  full-binary-tree → plane-tree.
```

Likewise, since `plane-tree` is the least fixed point of the endofunctor `list`,
we obtain a `list`-structure preserving map

```text
  plane-tree → full-binary-tree.
```

Initiality of both `full-binary-tree` and `plane-tree` can then be used to show
that these two maps are inverse to each other, i.e., that we obtain an
equivalence

```text
  plane-tree ≃ full-binary-tree.
```

```agda
full-binary-tree-listed-plane-tree : listed-plane-tree → full-binary-tree
full-binary-tree-listed-plane-tree (make-listed-plane-tree nil) =
  leaf-full-binary-tree
full-binary-tree-listed-plane-tree (make-listed-plane-tree (cons T l)) =
  join-full-binary-tree
    ( full-binary-tree-listed-plane-tree T)
    ( full-binary-tree-listed-plane-tree (make-listed-plane-tree l))

listed-plane-tree-full-binary-tree : full-binary-tree → listed-plane-tree
listed-plane-tree-full-binary-tree leaf-full-binary-tree =
  make-listed-plane-tree nil
listed-plane-tree-full-binary-tree (join-full-binary-tree S T) =
  make-listed-plane-tree
    ( cons
      ( listed-plane-tree-full-binary-tree S)
      ( unpack-listed-plane-tree (listed-plane-tree-full-binary-tree T)))

is-section-listed-plane-tree-full-binary-tree :
  is-section
    ( full-binary-tree-listed-plane-tree)
    ( listed-plane-tree-full-binary-tree)
is-section-listed-plane-tree-full-binary-tree leaf-full-binary-tree =
  refl
is-section-listed-plane-tree-full-binary-tree
  ( join-full-binary-tree S T) =
  ap-binary
    ( join-full-binary-tree)
    ( is-section-listed-plane-tree-full-binary-tree S)
    ( ( ap
        ( full-binary-tree-listed-plane-tree)
        ( is-section-unpack-listed-plane-tree _)) ∙
      ( is-section-listed-plane-tree-full-binary-tree T))

is-retraction-listed-plane-tree-full-binary-tree :
  is-retraction
    ( full-binary-tree-listed-plane-tree)
    ( listed-plane-tree-full-binary-tree)
is-retraction-listed-plane-tree-full-binary-tree (make-listed-plane-tree nil) =
  refl
is-retraction-listed-plane-tree-full-binary-tree
  ( make-listed-plane-tree (cons T l)) =
  ( ap
    ( make-listed-plane-tree)
    ( ap-binary
      ( cons)
      ( is-retraction-listed-plane-tree-full-binary-tree T)
      ( ap
        ( unpack-listed-plane-tree)
        ( is-retraction-listed-plane-tree-full-binary-tree
          ( make-listed-plane-tree l)))))

is-equiv-full-binary-tree-listed-plane-tree :
  is-equiv full-binary-tree-listed-plane-tree
is-equiv-full-binary-tree-listed-plane-tree =
  is-equiv-is-invertible
    ( listed-plane-tree-full-binary-tree)
    ( is-section-listed-plane-tree-full-binary-tree)
    ( is-retraction-listed-plane-tree-full-binary-tree)

equiv-full-binary-tree-listed-plane-tree :
  listed-plane-tree ≃ full-binary-tree
pr1 equiv-full-binary-tree-listed-plane-tree =
  full-binary-tree-listed-plane-tree
pr2 equiv-full-binary-tree-listed-plane-tree =
  is-equiv-full-binary-tree-listed-plane-tree
```