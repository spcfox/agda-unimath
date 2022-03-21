# Edge-coloured undirected graphs

```agda
{-# OPTIONS --without-K --exact-split #-}

module graph-theory.edge-coloured-undirected-graphs where

open import foundation.dependent-pair-types using (Σ; pair; pr1; pr2)
open import foundation.embeddings using (is-emb)
open import foundation.universe-levels using (Level; UU; _⊔_; lsuc; lzero)
open import foundation.unordered-pairs using (standard-unordered-pair)

open import graph-theory.incidence-undirected-graphs using
  ( incidence-Undirected-Graph)
open import graph-theory.undirected-graphs using
  ( Undirected-Graph; unordered-pair-vertices-Undirected-Graph;
    vertex-Undirected-Graph; edge-Undirected-Graph)

open import univalent-combinatorics.finite-types using (𝔽; type-𝔽)
```

## Idea

An edge-coloured undirected graph is an undirected graph equipped with a family of maps `E p → X` from the edges at unordered pairs `p` into a type `C` of colours, such that the induced map `incident-Undirected-Graph G x → C` is injective for each vertex `x`.

## Definition

```agda
module _
  {l1 l2 l3 : Level} (C : UU l1) (G : Undirected-Graph l2 l3)
  where

  incidence-edge-colouring-Undirected-Graph :
    ( (p : unordered-pair-vertices-Undirected-Graph G) →
      edge-Undirected-Graph G p → C) →
    (x : vertex-Undirected-Graph G) → incidence-Undirected-Graph G x → C
  incidence-edge-colouring-Undirected-Graph f x (pair y e) =
    f (standard-unordered-pair x y) e
  
  edge-colouring-Undirected-Graph : UU (lsuc lzero ⊔ l1 ⊔ l2 ⊔ l3)
  edge-colouring-Undirected-Graph =
    Σ ( (p : unordered-pair-vertices-Undirected-Graph G) →
        edge-Undirected-Graph G p → C)
      ( λ f →
        (x : vertex-Undirected-Graph G) →
        is-emb (incidence-edge-colouring-Undirected-Graph f x))

Edge-Coloured-Undirected-Graph :
  {l : Level} (l1 l2 : Level) (C : UU l) → UU (l ⊔ lsuc l1 ⊔ lsuc l2)
Edge-Coloured-Undirected-Graph l1 l2 C =
  Σ ( Undirected-Graph l1 l2)
    ( edge-colouring-Undirected-Graph C)
```