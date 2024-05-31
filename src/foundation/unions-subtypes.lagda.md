# Unions of subtypes

```agda
module foundation.unions-subtypes where
```

<details><summary>Imports</summary>

```agda
open import foundation.decidable-subtypes
open import foundation.dependent-pair-types
open import foundation.disjunction
open import foundation.function-types
open import foundation.identity-types
open import foundation.large-locale-of-subtypes
open import foundation.logical-equivalences
open import foundation.powersets
open import foundation.propositional-truncations
open import foundation.propositions
open import foundation.subtypes
open import foundation.universe-levels

open import order-theory.least-upper-bounds-large-posets
```

</details>

## Idea

The **union** of two [subtypes](foundation-core.subtypes.md) `A` and `B` is the
subtype that contains the elements that are in `A` or in `B`.

## Definition

### Unions of subtypes

```agda
module _
  {l l1 l2 : Level} {X : UU l}
  where

  union-subtype : subtype l1 X → subtype l2 X → subtype (l1 ⊔ l2) X
  union-subtype P Q x = (P x) ∨ (Q x)

  infixr 10 _∪_
  _∪_ = union-subtype
```

### Unions of decidable subtypes

```agda
  union-decidable-subtype :
    decidable-subtype l1 X → decidable-subtype l2 X →
    decidable-subtype (l1 ⊔ l2) X
  union-decidable-subtype P Q x = disjunction-Decidable-Prop (P x) (Q x)
```

### Unions of families of subtypes

```agda
module _
  {l1 l2 l3 : Level} {X : UU l1}
  where

  union-family-of-subtypes :
    {I : UU l2} (A : I → subtype l3 X) → subtype (l2 ⊔ l3) X
  union-family-of-subtypes = sup-powerset-Large-Locale

  is-least-upper-bound-union-family-of-subtypes :
    {I : UU l2} (A : I → subtype l3 X) →
    is-least-upper-bound-family-of-elements-Large-Poset
      ( powerset-Large-Poset X)
      ( A)
      ( union-family-of-subtypes A)
  is-least-upper-bound-union-family-of-subtypes =
    is-least-upper-bound-sup-powerset-Large-Locale
```

## Properties

### The union of families of subtypes preserves indexed inclusion

```agda
module _
  {l1 l2 l3 l4 : Level} {X : UU l1} {I : UU l2}
  (A : I → subtype l3 X) (B : I → subtype l4 X)
  where

  preserves-order-union-family-of-subtypes :
    ((i : I) → A i ⊆ B i) →
    union-family-of-subtypes A ⊆ union-family-of-subtypes B
  preserves-order-union-family-of-subtypes H x p =
    apply-universal-property-trunc-Prop p
      ( union-family-of-subtypes B x)
      ( λ (i , q) → unit-trunc-Prop (i , H i x q))
```

## TODO: Change title

```agda
module _
  {l1 l2 l3 : Level} {X : UU l1} (P : subtype l2 X) (Q : subtype l3 X)
  where

  subtype-union-left : P ⊆ P ∪ Q
  -- subtype-union-left x = inl-disjunction-Prop (P x) (Q x)
  subtype-union-left x = inl-disjunction

  subtype-union-right : Q ⊆ P ∪ Q
  -- subtype-union-right x = inr-disjunction-Prop (P x) (Q x)
  subtype-union-right x = inr-disjunction

  elim-union-subtype :
    {l4 : Level} (f : X → Prop l4) →
    ((x : X) → is-in-subtype P x → type-Prop (f x)) →
    ((x : X) → is-in-subtype Q x → type-Prop (f x)) →
    (x : X) → is-in-subtype (P ∪ Q) x → type-Prop (f x)
  elim-union-subtype f H-P H-Q x = elim-disjunction (f x) (H-P x) (H-Q x)

  elim-union-subtype' :
    {l4 : Level} (R : Prop l4) →
    ((x : X) → is-in-subtype P x → type-Prop R) →
    ((x : X) → is-in-subtype Q x → type-Prop R) →
    (x : X) → is-in-subtype (P ∪ Q) x → type-Prop R
  elim-union-subtype' R = elim-union-subtype (λ _ → R)
    -- elim-disjunction-Prop (P x) (Q x) R (H-P x , H-Q x)

  subtype-union-both :
    {l4 : Level} (S : subtype l4 X) → P ⊆ S → Q ⊆ S → P ∪ Q ⊆ S
  subtype-union-both = elim-union-subtype

module _
  {l1 l2 l3 : Level} {X : UU l1} (P : subtype l2 X) (Q : subtype l3 X)
  where

  subset-union-comm :
    P ∪ Q ⊆ Q ∪ P
  subset-union-comm =
    subtype-union-both P Q (Q ∪ P)
      ( subtype-union-right Q P)
      ( subtype-union-left Q P)

module _
  {l1 l2 l3 l4 : Level} {X : UU l1}
  (P : subtype l2 X) (Q : subtype l3 X) (S : subtype l4 X)
  where

  forward-subset-union-assoc : P ∪ (Q ∪ S) ⊆ (P ∪ Q) ∪ S
  forward-subset-union-assoc =
    subtype-union-both P (Q ∪ S) ((P ∪ Q) ∪ S)
      ( transitive-leq-subtype P (P ∪ Q) ((P ∪ Q) ∪ S)
        ( subtype-union-left (P ∪ Q) S)
        ( subtype-union-left P Q))
      ( subtype-union-both Q S ((P ∪ Q) ∪ S)
        ( transitive-leq-subtype Q (P ∪ Q) ((P ∪ Q) ∪ S)
          ( subtype-union-left (P ∪ Q) S)
          ( subtype-union-right P Q))
        ( subtype-union-right (P ∪ Q) S))

  backward-subset-union-assoc : (P ∪ Q) ∪ S ⊆ P ∪ (Q ∪ S)
  backward-subset-union-assoc =
    subtype-union-both (P ∪ Q) S (P ∪ (Q ∪ S))
      ( subtype-union-both P Q (P ∪ (Q ∪ S))
        ( subtype-union-left P (Q ∪ S))
        ( transitive-leq-subtype Q (Q ∪ S) (P ∪ (Q ∪ S))
          ( subtype-union-right P (Q ∪ S))
          ( subtype-union-left Q S)))
      ( transitive-leq-subtype S (Q ∪ S) (P ∪ (Q ∪ S))
        ( subtype-union-right P (Q ∪ S))
        ( subtype-union-right Q S))

module _
  {l1 : Level} {X : UU l1}
  where

  subset-union-subsets :
    {l2 l3 l4 l5 : Level}
    (P1 : subtype l2 X) (Q1 : subtype l3 X)
    (P2 : subtype l4 X) (Q2 : subtype l5 X) →
    P1 ⊆ P2 → Q1 ⊆ Q2 →
    P1 ∪ Q1 ⊆ P2 ∪ Q2
  subset-union-subsets P1 Q1 P2 Q2 P1-sub-P2 Q1-sub-Q2 =
    subtype-union-both P1 Q1 (P2 ∪ Q2)
      ( transitive-leq-subtype P1 P2 (P2 ∪ Q2)
        ( subtype-union-left P2 Q2)
        ( P1-sub-P2))
      ( transitive-leq-subtype Q1 Q2 (P2 ∪ Q2)
        ( subtype-union-right P2 Q2)
        ( Q1-sub-Q2))

  subset-union-subset-left :
    {l2 l3 l4 : Level}
    (P1 : subtype l2 X) (P2 : subtype l3 X) (Q : subtype l4 X) →
    P1 ⊆ P2 →
    P1 ∪ Q ⊆ P2 ∪ Q
  subset-union-subset-left P1 P2 Q P1-sub-P2 =
    subset-union-subsets P1 Q P2 Q P1-sub-P2 (refl-leq-subtype Q)

  subset-union-subset-right :
    {l2 l3 l4 : Level}
    (P : subtype l2 X) (Q1 : subtype l3 X) (Q2 : subtype l4 X) →
    Q1 ⊆ Q2 →
    union-subtype P Q1 ⊆ union-subtype P Q2
  subset-union-subset-right P Q1 Q2 Q1-sub-Q2 =
    subset-union-subsets P Q1 P Q2 (refl-leq-subtype P) Q1-sub-Q2

module _
  {l1 l2 l3 l4 : Level} {X : UU l1}
  (P : subtype l2 X) (Q : subtype l3 X) (S : subtype l4 X)
  where

  union-swap-1-2 :
    P ∪ (Q ∪ S) ⊆ Q ∪ (P ∪ S)
  union-swap-1-2 =
    transitive-leq-subtype (P ∪ (Q ∪ S)) ((Q ∪ P) ∪ S) (Q ∪ (P ∪ S))
      ( backward-subset-union-assoc Q P S)
      ( transitive-leq-subtype (P ∪ (Q ∪ S)) ((P ∪ Q) ∪ S) ((Q ∪ P) ∪ S)
        ( subset-union-subset-left (P ∪ Q) (Q ∪ P) S
          ( subset-union-comm P Q))
        ( forward-subset-union-assoc P Q S))

module _
  {l1 l2 : Level} {X : UU l1} (P : subtype l2 X)
  where

  subtype-union-same : union-subtype P P ⊆ P
  subtype-union-same =
    subtype-union-both P P P (refl-leq-subtype P) (refl-leq-subtype P)

  eq-union-same : P ＝ union-subtype P P
  eq-union-same =
    antisymmetric-leq-subtype
    ( P)
    ( union-subtype P P)
    ( subtype-union-left P P)
    ( subtype-union-same)

module _
  {l1 l2 : Level} {X : UU l1} (P : subtype l2 X) (Q : subtype l2 X)
  where

  eq-union-subset-left : P ⊆ Q → P ∪ Q ＝ Q
  eq-union-subset-left P-sub-Q =
    antisymmetric-leq-subtype
      ( P ∪ Q)
      ( Q)
      ( subtype-union-both P Q Q P-sub-Q (refl-leq-subtype Q))
      ( subtype-union-right P Q)

  eq-union-subset-right : Q ⊆ P → P ∪ Q ＝ P
  eq-union-subset-right Q-sub-P =
    antisymmetric-leq-subtype
      ( P ∪ Q)
      ( P)
      ( subtype-union-both P Q P (refl-leq-subtype P) Q-sub-P)
      ( subtype-union-left P Q)
```
