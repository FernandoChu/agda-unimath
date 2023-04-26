# Epimorphisms

```agda
module foundation.epimorphisms where
```

<details><summary>Imports</summary>

```agda
open import foundation.embeddings
open import foundation.functions
open import foundation.universe-levels
```

</details>

## Idea

A map `f : A → B` is said to be an **epimorphism** if the precomposition
function

```md
  - ∘ f : (B → X) → (A → X)
```

is an embedding for every type `X`.

## Definitions

```agda
is-epimorphism :
  {l1 l2 : Level} (l : Level) {A : UU l1} {B : UU l2} →
  (A → B) → UU (l1 ⊔ l2 ⊔ lsuc l)
is-epimorphism l f = (X : UU l) → is-emb (precomp f X)
```