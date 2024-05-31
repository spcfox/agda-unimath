# Modal logic formulas

```agda
module modal-logic.formulas where
```

<details><summary>Imports</summary>

```agda
open import foundation.action-on-identifications-functions
open import foundation.cartesian-product-types
open import foundation.contractible-types
open import foundation.dependent-pair-types
open import foundation.empty-types
open import foundation.equality-cartesian-product-types
open import foundation.equivalences
open import foundation.identity-types
open import foundation.propositions
open import foundation.raising-universe-levels
open import foundation.sets
open import foundation.transport-along-identifications
open import foundation.unit-type
open import foundation.universe-levels
```

</details>

## Idea

The formula of modal logic is defined inductively as follows:

- A variable is a formula.
- ⊥ is a formula.
- If `a` and `b` are formulas, then `a → b` is a formula.
- If `a` is a formula, then □`a` is a formula.

## Definition

### Inductive definition of formulas

```agda
module _
  {l : Level}
  where

  infixr 8 _→ₘ_
  infixr 25 □ₘ_

  data modal-formula (i : Set l) : UU l where
    varₘ : type-Set i → modal-formula i
    ⊥ₘ : modal-formula i
    _→ₘ_ : modal-formula i → modal-formula i → modal-formula i
    □ₘ_ : modal-formula i → modal-formula i
```

### Additional notations

```agda
module _
  {l : Level} {i : Set l}
  where

  infixr 25 ¬ₘ_
  infixr 25 ¬¬ₘ_
  infixl 10 _∨ₘ_
  infixl 15 _∧ₘ_
  infixr 25 ◇ₘ_

  ¬ₘ_ : modal-formula i → modal-formula i
  ¬ₘ a = a →ₘ ⊥ₘ

  ¬¬ₘ_ : modal-formula i → modal-formula i
  ¬¬ₘ a = ¬ₘ ¬ₘ a

  _∨ₘ_ : modal-formula i → modal-formula i → modal-formula i
  a ∨ₘ b = ¬ₘ a →ₘ b

  _∧ₘ_ : modal-formula i → modal-formula i → modal-formula i
  a ∧ₘ b = ¬ₘ (a →ₘ ¬ₘ b)

  ⊤ₘ : modal-formula i
  ⊤ₘ = ¬ₘ ⊥ₘ

  ◇ₘ_ : modal-formula i → modal-formula i
  ◇ₘ a = ¬ₘ □ₘ ¬ₘ a
```

### If formulas are equal, then their subformulas are equal

```agda
  eq-implication-left : {a b c d : modal-formula i} → a →ₘ b ＝ c →ₘ d → a ＝ c
  eq-implication-left refl = refl

  eq-implication-right : {a b c d : modal-formula i} → a →ₘ b ＝ c →ₘ d → b ＝ d
  eq-implication-right refl = refl

  eq-box : {a b : modal-formula i} → □ₘ a ＝ □ₘ b → a ＝ b
  eq-box refl = refl

  eq-diamond : {a b : modal-formula i} → ◇ₘ a ＝ ◇ₘ b → a ＝ b
  eq-diamond refl = refl
```

## Properties

### Characterizing the identity type of formulas

```agda
module _
  {l : Level} {i : Set l}
  where

  Eq-formula : modal-formula i → modal-formula i → UU l
  Eq-formula (varₘ n) (varₘ m) = n ＝ m
  Eq-formula (varₘ _) ⊥ₘ = raise-empty l
  Eq-formula (varₘ _) (_ →ₘ _) = raise-empty l
  Eq-formula (varₘ -) (□ₘ _) = raise-empty l
  Eq-formula ⊥ₘ (varₘ _) = raise-empty l
  Eq-formula ⊥ₘ ⊥ₘ = raise-unit l
  Eq-formula ⊥ₘ (_ →ₘ _) = raise-empty l
  Eq-formula ⊥ₘ (□ₘ _) = raise-empty l
  Eq-formula (_ →ₘ _) (varₘ _) = raise-empty l
  Eq-formula (_ →ₘ _) ⊥ₘ = raise-empty l
  Eq-formula (a →ₘ b) (c →ₘ d) = (Eq-formula a c) × (Eq-formula b d)
  Eq-formula (_ →ₘ _) (□ₘ _) = raise-empty l
  Eq-formula (□ₘ _) (varₘ _) = raise-empty l
  Eq-formula (□ₘ _) ⊥ₘ = raise-empty l
  Eq-formula (□ₘ _) (_ →ₘ _) = raise-empty l
  Eq-formula (□ₘ a) (□ₘ c) = Eq-formula a c

  refl-Eq-formula : (a : modal-formula i) → Eq-formula a a
  refl-Eq-formula (varₘ n) = refl
  refl-Eq-formula ⊥ₘ = raise-star
  refl-Eq-formula (a →ₘ b) = (refl-Eq-formula a) , (refl-Eq-formula b)
  refl-Eq-formula (□ₘ a) = refl-Eq-formula a

  Eq-eq-modal-formula : {a b : modal-formula i} → a ＝ b → Eq-formula a b
  Eq-eq-modal-formula {a} refl = refl-Eq-formula a

  eq-Eq-modal-formula : {a b : modal-formula i} → Eq-formula a b → a ＝ b
  eq-Eq-modal-formula {varₘ _} {varₘ _} refl = refl
  eq-Eq-modal-formula {varₘ _} {⊥ₘ} (map-raise ())
  eq-Eq-modal-formula {varₘ _} {_ →ₘ _} (map-raise ())
  eq-Eq-modal-formula {varₘ _} {□ₘ _} (map-raise ())
  eq-Eq-modal-formula {⊥ₘ} {varₘ _} (map-raise ())
  eq-Eq-modal-formula {⊥ₘ} {⊥ₘ} _ = refl
  eq-Eq-modal-formula {⊥ₘ} {_ →ₘ _} (map-raise ())
  eq-Eq-modal-formula {⊥ₘ} {□ₘ _} (map-raise ())
  eq-Eq-modal-formula {_ →ₘ _} {varₘ _} (map-raise ())
  eq-Eq-modal-formula {_ →ₘ _} {⊥ₘ} (map-raise ())
  eq-Eq-modal-formula {a →ₘ b} {c →ₘ d} (eq1 , eq2) =
    ap (λ (x , y) → x →ₘ y)
      ( eq-pair (eq-Eq-modal-formula eq1) (eq-Eq-modal-formula eq2))
  eq-Eq-modal-formula {_ →ₘ _} {□ₘ _} (map-raise ())
  eq-Eq-modal-formula {□ₘ _} {varₘ _} (map-raise ())
  eq-Eq-modal-formula {□ₘ _} {⊥ₘ} (map-raise ())
  eq-Eq-modal-formula {□ₘ _} {_ →ₘ _} (map-raise ())
  eq-Eq-modal-formula {□ₘ _} {□ₘ _} eq = ap □ₘ_ (eq-Eq-modal-formula eq)

  is-prop-Eq-modal-formula : (a b : modal-formula i) → is-prop (Eq-formula a b)
  is-prop-Eq-modal-formula (varₘ n) (varₘ m) = is-prop-type-Prop (Id-Prop i n m)
  is-prop-Eq-modal-formula (varₘ _) ⊥ₘ = is-prop-raise-empty
  is-prop-Eq-modal-formula (varₘ _) (_ →ₘ _) = is-prop-raise-empty
  is-prop-Eq-modal-formula (varₘ -) (□ₘ _) = is-prop-raise-empty
  is-prop-Eq-modal-formula ⊥ₘ (varₘ _) = is-prop-raise-empty
  is-prop-Eq-modal-formula ⊥ₘ ⊥ₘ = is-prop-raise-unit
  is-prop-Eq-modal-formula ⊥ₘ (_ →ₘ _) = is-prop-raise-empty
  is-prop-Eq-modal-formula ⊥ₘ (□ₘ _) = is-prop-raise-empty
  is-prop-Eq-modal-formula (_ →ₘ _) (varₘ _) = is-prop-raise-empty
  is-prop-Eq-modal-formula (_ →ₘ _) ⊥ₘ = is-prop-raise-empty
  is-prop-Eq-modal-formula (a →ₘ b) (c →ₘ d) =
    is-prop-product
    ( is-prop-Eq-modal-formula a c)
    ( is-prop-Eq-modal-formula b d)
  is-prop-Eq-modal-formula (_ →ₘ _) (□ₘ _) = is-prop-raise-empty
  is-prop-Eq-modal-formula (□ₘ _) (varₘ _) = is-prop-raise-empty
  is-prop-Eq-modal-formula (□ₘ _) ⊥ₘ = is-prop-raise-empty
  is-prop-Eq-modal-formula (□ₘ _) (_ →ₘ _) = is-prop-raise-empty
  is-prop-Eq-modal-formula (□ₘ a) (□ₘ c) = is-prop-Eq-modal-formula a c
```

### A formula is a set

```agda
  is-set-modal-formula : is-set (modal-formula i)
  is-set-modal-formula =
    is-set-prop-in-id
      ( Eq-formula)
      ( is-prop-Eq-modal-formula)
      ( refl-Eq-formula)
      ( λ _ _ → eq-Eq-modal-formula)

modal-formula-Set : {l : Level} (i : Set l) → Set l
pr1 (modal-formula-Set i) = modal-formula i
pr2 (modal-formula-Set i) = is-set-modal-formula

Id-modal-formula-Prop :
  {l : Level} {i : Set l} → modal-formula i → modal-formula i → Prop l
Id-modal-formula-Prop {i = i} = Id-Prop (modal-formula-Set i)
```
