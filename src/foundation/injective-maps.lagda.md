# Injective maps

```agda
module foundation.injective-maps where

open import foundation-core.injective-maps public
```

<details><summary>Imports</summary>

```agda
open import foundation.decidable-types
open import foundation.dependent-pair-types
open import foundation.functoriality-propositional-truncation
open import foundation.inhabited-types
open import foundation.logical-equivalences
open import foundation.surjective-maps
open import foundation.universe-levels

open import foundation-core.coproduct-types
open import foundation-core.embeddings
open import foundation-core.empty-types
open import foundation-core.fibers-of-maps
open import foundation-core.identity-types
open import foundation-core.negation
open import foundation-core.propositional-maps
open import foundation-core.propositions
open import foundation-core.retractions
open import foundation-core.sets
```

</details>

## Idea

A map `f : A → B` is **injective** if `f x ＝ f y` implies `x ＝ y`.

## Warning

The notion of injective map is, however, not homotopically coherent. It is fine
to use injectivity for maps between [sets](foundation-core.sets.md), but for
maps between general types it is recommended to use the notion of
[embedding](foundation-core.embeddings.md).

## Definitions

### Noninjective maps

```agda
module _
  {l1 l2 : Level} {A : UU l1} {B : UU l2}
  where

  is-not-injective : (A → B) → UU (l1 ⊔ l2)
  is-not-injective f = ¬ (is-injective f)
```

### Any map out of an empty type is injective

```agda
is-injective-is-empty :
  {l1 l2 : Level} {A : UU l1} {B : UU l2} (f : A → B) →
  is-empty A → is-injective f
is-injective-is-empty f is-empty-A {x} = ex-falso (is-empty-A x)
```

### Any injective map between sets is an embedding

```agda
module _
  {l1 l2 : Level} {A : UU l1} {B : UU l2}
  where

  abstract
    is-emb-is-injective' :
      (is-set-A : is-set A) (is-set-B : is-set B) (f : A → B) →
      is-injective f → is-emb f
    is-emb-is-injective' is-set-A is-set-B f is-injective-f x y =
      is-equiv-has-converse-is-prop
        ( is-set-A x y)
        ( is-set-B (f x) (f y))
        ( is-injective-f)

    is-emb-is-injective :
      {f : A → B} → is-set B → is-injective f → is-emb f
    is-emb-is-injective {f} H I =
      is-emb-is-injective' (is-set-is-injective H I) H f I

    is-prop-map-is-injective :
      {f : A → B} → is-set B → is-injective f → is-prop-map f
    is-prop-map-is-injective {f} H I =
      is-prop-map-is-emb (is-emb-is-injective H I)
```

### For a map between sets, being injective is a property

```agda
module _
  {l1 l2 : Level} {A : UU l1} {B : UU l2}
  where

  is-prop-is-injective :
    is-set A → (f : A → B) → is-prop (is-injective f)
  is-prop-is-injective H f =
    is-prop-implicit-Π
      ( λ x →
        is-prop-implicit-Π
          ( λ y → is-prop-function-type (H x y)))

  is-injective-Prop : is-set A → (A → B) → Prop (l1 ⊔ l2)
  pr1 (is-injective-Prop H f) = is-injective f
  pr2 (is-injective-Prop H f) = is-prop-is-injective H f
```

### TODO: Title

```agda
module _
  {l1 l2 : Level} {A : UU l1} {B : UU l2}
  (f : A → B)
  (is-injective-f : is-injective f)
  (dec : (b : B) → is-decidable (fiber f b))
  where

  retraction-is-injective : A → retraction f
  pr1 (retraction-is-injective a0) b with dec b
  ... | inl (a , _) = a
  ... | inr _ = a0
  pr2 (retraction-is-injective _) a with dec (f a)
  ... | inl (a , eq) = is-injective-f eq
  ... | inr contra = ex-falso (contra (a , refl))

  surjective-is-injective : A → B ↠ A
  pr1 (surjective-is-injective a) =
    map-retraction f (retraction-is-injective a)
  pr2 (surjective-is-injective a) =
    is-surjective-has-section
      ( pair f
        ( is-retraction-map-retraction f
          ( retraction-is-injective a)))

  is-inhabited-inv-surjections : is-inhabited A → is-inhabited (B ↠ A)
  is-inhabited-inv-surjections = map-trunc-Prop surjective-is-injective
```
