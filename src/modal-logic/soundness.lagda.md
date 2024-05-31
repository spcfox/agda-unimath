# Modal logic soundness

```agda
module modal-logic.soundness where
```

<details><summary>Imports</summary>

```agda
open import foundation.dependent-pair-types
open import foundation.inhabited-types
open import foundation.intersections-subtypes
open import foundation.propositional-truncations
open import foundation.propositions
open import foundation.sets
open import foundation.subtypes
open import foundation.transport-along-identifications
open import foundation.unions-subtypes
open import foundation.universe-levels

open import foundation-core.coproduct-types

open import modal-logic.deduction
open import modal-logic.formulas
open import modal-logic.kripke-semantics
```

</details>

## Idea

TODO

## Definition

```agda
soundness :
  {l1 l2 l3 l4 l5 l6 : Level} {i : Set l1} →
  modal-theory l2 i →
  model-class l3 l4 i l5 l6 →
  UU (l1 ⊔ l2 ⊔ lsuc l3 ⊔ lsuc l4 ⊔ lsuc l5 ⊔ l6)
soundness logic C = logic ⊆ class-modal-logic C
```

## Properties

```agda
module _
  {l1 l2 l3 l4 l5 l6 : Level}
  {i : Set l1} {axioms : modal-theory l2 i}
  (C : model-class l3 l4 i l5 l6)
  where

  soundness-axioms :
    soundness axioms C →
    {a : modal-formula i} →
    axioms ⊢ₘ a →
    type-Prop (C ⊨Cₘ a)
  soundness-axioms H (modal-ax x) = H _ x
  soundness-axioms H (modal-mp dab da) M in-class x =
    soundness-axioms H dab M in-class x (soundness-axioms H da M in-class x)
  soundness-axioms H (modal-nec d) M in-class _ y _ =
    soundness-axioms H d M in-class y

  soundness-modal-logic :
    soundness axioms C → soundness (modal-logic-closure axioms) C
  soundness-modal-logic H a =
    map-universal-property-trunc-Prop (C ⊨Cₘ a) (soundness-axioms H)

module _
  {l1 l2 l3 l4 l5 l6 l7 : Level}
  {i : Set l1} (logic : modal-theory l2 i)
  (C₁ : model-class l3 l4 i l5 l6) (C₂ : model-class l3 l4 i l5 l7)
  where

  soundness-subclass : C₂ ⊆ C₁ → soundness logic C₁ → soundness logic C₂
  soundness-subclass sub =
    transitive-leq-subtype
      ( logic)
      ( class-modal-logic C₁)
      ( class-modal-logic C₂)
      ( class-modal-logic-monotic C₂ C₁ sub)

module _
  {l1 l2 l3 l4 l5 l6 l7 l8 : Level}
  {i : Set l1} (theory₁ : modal-theory l2 i) (theory₂ : modal-theory l3 i)
  (C₁ : model-class l4 l5 i l6 l7) (C₂ : model-class l4 l5 i l6 l8)
  (sound₁ : soundness theory₁ C₁) (sound₂ : soundness theory₂ C₂)
  where

  forces-in-intersection :
    (M : kripke-model l4 l5 i l6) →
    is-in-subtype (C₁ ∩ C₂) M →
    (a : modal-formula i) →
    is-in-subtype theory₁ a + is-in-subtype theory₂ a →
    type-Prop (M ⊨Mₘ a)
  forces-in-intersection M in-class a (inl d) =
    sound₁ a d M (subtype-intersection-left C₁ C₂ M in-class)
  forces-in-intersection M in-class a (inr d) =
    sound₂ a d M (subtype-intersection-right C₁ C₂ M in-class)

  soundness-union : soundness (theory₁ ∪ theory₂) (C₁ ∩ C₂)
  soundness-union a is-theory M in-class =
    apply-universal-property-trunc-Prop
      ( is-theory)
      ( M ⊨Mₘ a)
      ( forces-in-intersection M in-class a)

  soundness-modal-logic-union :
    soundness (modal-logic-closure (theory₁ ∪ theory₂)) (C₁ ∩ C₂)
  soundness-modal-logic-union =
    soundness-modal-logic (C₁ ∩ C₂) soundness-union

module _
  {l1 l2 l3 l4 l5 l6 l7 : Level}
  {i : Set l1} (ax₁ : modal-theory l2 i) (ax₂ : modal-theory l3 i)
  where

  soundness-union-subclass-left-sublevels :
    (l8 : Level)
    (C₁ : model-class l4 l5 i l6 (l7 ⊔ l8)) (C₂ : model-class l4 l5 i l6 l7)
    (sound₁ : soundness ax₁ C₁) (sound₂ : soundness ax₂ C₂) →
    C₁ ⊆ C₂ →
    soundness (ax₁ ∪ ax₂) C₁
  soundness-union-subclass-left-sublevels
    l8 C₁ C₂ sound₁ sound₂ C₁-sub-C₂ =
      tr
        ( soundness (ax₁ ∪ ax₂))
        ( intersection-subtype-left-sublevels l8 C₁ C₂ C₁-sub-C₂)
        ( soundness-union ax₁ ax₂ C₁ C₂ sound₁ sound₂)

  soundness-union-subclass-right-sublevels :
    (l8 : Level)
    (C₁ : model-class l4 l5 i l6 l7) (C₂ : model-class l4 l5 i l6 (l7 ⊔ l8))
    (sound₁ : soundness ax₁ C₁) (sound₂ : soundness ax₂ C₂) →
    C₂ ⊆ C₁ →
    soundness (ax₁ ∪ ax₂) C₂
  soundness-union-subclass-right-sublevels
    l8 C₁ C₂ sound₁ sound₂ C₂-sub-C₁ =
      tr
        ( soundness (ax₁ ∪ ax₂))
        ( intersection-subtype-right-sublevels l8 C₁ C₂ C₂-sub-C₁)
        ( soundness-union ax₁ ax₂ C₁ C₂ sound₁ sound₂)

  soundness-modal-logic-union-subclass-left-sublevels :
    (l8 : Level)
    (C₁ : model-class l4 l5 i l6 (l7 ⊔ l8)) (C₂ : model-class l4 l5 i l6 l7)
    (sound₁ : soundness ax₁ C₁) (sound₂ : soundness ax₂ C₂) →
    C₁ ⊆ C₂ →
    soundness (modal-logic-closure (ax₁ ∪ ax₂)) C₁
  soundness-modal-logic-union-subclass-left-sublevels
    l8 C₁ C₂ sound₁ sound₂ C₁-sub-C₂ =
      tr
        ( soundness (modal-logic-closure (ax₁ ∪ ax₂)))
        ( intersection-subtype-left-sublevels l8 C₁ C₂ C₁-sub-C₂)
        ( soundness-modal-logic-union ax₁ ax₂ C₁ C₂ sound₁ sound₂)

  soundness-modal-logic-union-subclass-right-sublevels :
    (l8 : Level)
    (C₁ : model-class l4 l5 i l6 l7) (C₂ : model-class l4 l5 i l6 (l7 ⊔ l8))
    (sound₁ : soundness ax₁ C₁) (sound₂ : soundness ax₂ C₂) →
    C₂ ⊆ C₁ →
    soundness (modal-logic-closure (ax₁ ∪ ax₂)) C₂
  soundness-modal-logic-union-subclass-right-sublevels
    l8 C₁ C₂ sound₁ sound₂ C₂-sub-C₁ =
      tr
        ( soundness (modal-logic-closure (ax₁ ∪ ax₂)))
        ( intersection-subtype-right-sublevels l8 C₁ C₂ C₂-sub-C₁)
        ( soundness-modal-logic-union ax₁ ax₂ C₁ C₂ sound₁ sound₂)

  soundness-modal-logic-union-subclass-left :
    (C₁ : model-class l4 l5 i l6 l7) (C₂ : model-class l4 l5 i l6 l7)
    (sound₁ : soundness ax₁ C₁) (sound₂ : soundness ax₂ C₂) →
    C₁ ⊆ C₂ →
    soundness (modal-logic-closure (ax₁ ∪ ax₂)) C₁
  soundness-modal-logic-union-subclass-left =
    soundness-modal-logic-union-subclass-left-sublevels lzero

  soundness-modal-logic-union-subclass-right :
    (C₁ : model-class l4 l5 i l6 l7) (C₂ : model-class l4 l5 i l6 l7)
    (sound₁ : soundness ax₁ C₁) (sound₂ : soundness ax₂ C₂) →
    C₂ ⊆ C₁ →
    soundness (modal-logic-closure (ax₁ ∪ ax₂)) C₂
  soundness-modal-logic-union-subclass-right =
    soundness-modal-logic-union-subclass-right-sublevels lzero

module _
  {l1 l2 l3 l4 l5 l6 l7 : Level}
  {i : Set l1} (ax₁ : modal-theory l2 i) (ax₂ : modal-theory l3 i)
  (C : model-class l4 l5 i l6 l7)
  (sound₁ : soundness ax₁ C) (sound₂ : soundness ax₂ C)
  where

  soundness-union-same-class : soundness (ax₁ ∪ ax₂) C
  soundness-union-same-class =
    tr
      ( soundness (ax₁ ∪ ax₂))
      ( is-reflexivity-intersection C)
      ( soundness-union ax₁ ax₂ C C sound₁ sound₂)

  soundness-modal-logic-union-same-class :
    soundness (modal-logic-closure (ax₁ ∪ ax₂)) C
  soundness-modal-logic-union-same-class =
    soundness-modal-logic C soundness-union-same-class
```
