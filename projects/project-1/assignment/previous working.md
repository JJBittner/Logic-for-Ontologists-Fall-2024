## Assignment Part 2

J Bittner, Tim Coleman & Matilde Miroir

Let us focus now on combinations of role constraints that do not result in inconsistency or undecidability. Moreover, let us focus on combinations of role constraints spread across parent-child object property relationships. To illustrate, suppose `B` is an owl:subPropertyOf `A`, `Ai` is the inverse of `A`, and `Bi` is the inverse of `B`. Keep in mind that: 
- For any object properties `A`, `B`: if `B` owl:subPropertyOf `A` then for any  `<x,y>` in `B` that `<x,y>` is also in `A`
- For any object properties `R`, `Ri`: if `R` inverse of `Ri` then for any `<x,y>` in `R` then `<y,x>` in `Ri`

|            |**Trans**|                |                |                |
|------------|----------------|----------------|----------------|----------------|
|**Irr**| **A** | **Ai** | **B** | **Bi** |
| **A**      | X     | X      | X    | X     |
| **Ai**     | X     | X      | X    | X     |
| **B**      | OK     | OK      | X     | X      |
| **Bi**     | OK     | OK      | X     | X      |


For the cells with "X", where irreflexivity and transitivity were applied to the same object property, OWL reasoners throw errors or ignore role constraints. For example, the above "X" combinations resulted in Fact++ and HermiT generating internal errors, while Pellet ignored the transitivity constraint but kept the irreflexivity constraint.

Suppose `B` owl:subPropertyOf of `A`, `A` is irreflexive and `B` is transitive. This corresponds to the first row of letters and and the third column of values, which in this case is "X". Because `B` is transitive, if `<x,y>` and `<y,z>`, then `<x,z>`. Because `A` is irreflexive, it is not the case for any `x` that `<x,x>` is in `A`. Because `B` owl:subPropertyOf of `A`, it follows that if `<x,y>` and `<y,z>`, in then `<x,z>` is in `A` as well. The result of such chaining and checking whether `<x,x>` is in `A` is however forbidden in many OWL 2 reasoners, as it involves a non-simple property exhibiting an irreflexive role constraint. 

### Variable Constraints

- `B` owl:subPropertyOf of `A`
- `Bi` owl:subPropertyOf of `Ai`
- `Ai` is the inverse of `A`
- `Bi` is the inverse of `B`

### (A) Transitive and Irreflexive Trials 
|              | A Trans | Ai Trans | B Trans  | Bi Trans |
|--------------|---------|----------|----------|----------|
| **A  Irr**   | N<sub>1 | N<sub>2  | N<sub>3  | N<sub>4  |
| **Ai Irr**   | N<sub>5 | N<sub>6  | N<sub>7  | N<sub>8  |
| **B  Irr**   | Y<sub>9 | Y<sub>10 | N<sub>11 | N<sub>12 |
| **Bi Irr**   | Y<sub>13| Y<sub>14 | N<sub>15 | N<sub>16 |

*_N denotes an error while Y denotes a successful run_

**_Subscripts denote corresponding explantion below_


### [Object Property Expression Axioms](https://www.w3.org/TR/owl2-direct-semantics/)

SubObjectPropertyOf( OPE1 OPE2 )
- (OPE1)<sup>OP</sup> ⊆ (OPE2)<sup>OP</sup>

InverseObjectProperties( OPE1 OPE2 )
- (OPE1)<sup>OP</sup> = { ( x , y ) | ( y , x ) ∈ (OPE2)<sup>OP</sup> } 

IrreflexiveObjectProperty( OPE ) 
- ∀ x : x ∈ ΔI implies ( x , x ) ∉ (OPE)<sup>OP</sup>

TransitiveObjectProperty( OPE ) 
- ∀ x , y , z : ( x , y ) ∈ (OPE)<sup>OP</sup> and ( y , z ) ∈ (OPE)<sup>OP</sup> imply ( x , z ) ∈ (OPE)<sup>OP</sup>

### (B) Transitive and Functional Trials
|             | A Trans | B Trans | Ai Trans | Bi Trans |
|-------------|---------|---------|----------|----------|
| **A  Func** | N<sub>1 | N<sub>2 | N<sub>3  | N<sub>4  |
| **B  Func** | N<sub>5 | N<sub>6 | N<sub>7  | N<sub>8  |
| **Ai Func** | Y<sub>9 | Y<sub>10| N<sub>11 | N<sub>12 |
| **Bi Func** | Y<sub>13| Y<sub>14| N<sub>15 | N<sub>16 |

*_N denotes an error while Y denotes a successful run_

**_Subscripts denote corresponding explantion below_

## Conflict: Functional (A) and Transitive (B)

(1) Suppose `A` is functional, and `B` is transitive. This corresponds to the first row (`A`) and the third column (`B`) of the table, marked with "N". Because `B` is transitive, if `<x,y>` and `<y,z>` hold, then `<x,z>` must also hold due to transitivity.

This creates a conflict since `A` is functional; `x` can only relate to a single `y`. Since `B` is transitive, this implies that `x` could relate to multiple elements (both `y` and `z`), which violates the functionality constraint of `A`, and OWL reasoners struggle to reconcile this.

## Conflict: Inverse Functional (Ai) and Transitive (B)

Suppose `Ai` is inverse functional and `B` is transitive. This corresponds to the second row (`Ai`) and the third column (`B`), marked "N". For `B`, if `<x,y>` and `<y,z>`, then `<x,z>` by transitivity. Since `Ai` is inverse functional, each `z` should relate to only one `x`.

This creates a conflict because, due to transitivity in `B`, `z` could relate to both `x` and `y`, which violates the inverse functionality of `Ai`. This contradiction makes it incompatible for OWL reasoners.

## Conflict: Functional (A) and Inverse Transitive (Ai)

Suppose `A` is functional and `Ai` is transitive with `B` being `owl:subPropertyOf` `A`. This matches the first row (`A`) and the second column (`Ai`), marked “N”. With `Ai` being transitive, if `<x,y>` and `<z,y>`, then `<x,z>`. This creates a conflict because if `A` is functional, then `<x,y>` and `<x,z>` imply that `y = z`. 

This creates a conflict when attempting to satisfy both properties simultaneously, leading to a situation where `x` and `z` must be both equal and distinct at the same time, which is logically impossible. This contradiction makes it incompatible for OWL reasoners.

## Conflict: Functional (A) and Inverse Transitive (Bi)

Suppose `A` is functional and `Bi` is inverse transitive with `B` being `owl:subPropertyOf` `A`. If `Bi` is inverse transitive, then if `xR⁻¹y` and `yR⁻¹z`, inverse transitivity implies `xR⁻¹z`.

This creates a conflict because if `A` is functional, then `<x,y>` and `<x,z>` imply that `y = z`. However, the inverse transitivity of `Bi` suggests that `z` could be different from `y` but still related to `x`, which breaks the functional constraint and makes it incompatible for OWL reasoners.

## Conflict: Inverse Functional (Ai) and Transitive (A)

Suppose Ai is inverse functional and A is transitive, where B is owl:subPropertyOf of A. This corresponds to the second row (Ai) and the first column (A) of the table marked with X. Since Ai is inverse functional, each z should relate to only one x. For A, if <x,y> and <y,z>, then <x,z> by transitivity. Transitivity relates to more things, so the OWL reasoner states it is incompatible.

Suppose `Ai` is inverse functional and `A` is transitive, where `B` is `owl:subPropertyOf` `A`. This corresponds to the second row (`Ai`) and the first column (`A`) of the table, marked with "X". Since `Ai` is inverse functional, each `z` should relate to only one `x`. For `A`, if `<x,y>` and `<y,z>`, then `<x,z>` by transitivity.

This creates a conflict because transitivity allows `x` to relate to multiple elements (`y` and `z`), which violates the inverse functional constraint of `Ai` where each `z` should only relate to one `x`. This contradiction makes it incompatible for OWL reasoners.

## Conflict: Inverse Functional (Ai) and Inverse Transitive (Bi)

Suppose Ai is inverse functional and Bi is inverse transitive. This corresponds to the third row (Ai) and the fourth column (Bi), marked "N<sub>12</sub>". Since Ai is inverse functional, for any <x,y> and <z,y>, it must hold that <x = z>. However, because Bi is inverse transitive, if <xRy> and <yRz>, then by inverse transitivity, <xRz>.

This creates a conflict because if Ai is inverse functional, <xRy> and <zRy> should imply that <x = z>. But due to the inverse transitivity of Bi, it's possible for <x> and <z> to both relate to <y>, which contradicts the inverse functionality constraint that requires <x> and <z> to be the same. This inconsistency makes it incompatible for OWL reasoners.

Suppose `Ai` is inverse functional and `Bi` is inverse transitive. This corresponds to the third row (`Ai`) and the fourth column (`Bi`), marked "N<sub>12</sub>". Since `Ai` is inverse functional, for any `<x,y>` and `<z,y>`, it must hold that `<x = z>`. However, because `Bi` is inverse transitive, if `<xRy>` and `<yRz>`, then by inverse transitivity, `<xRz>`.

This creates a conflict because if `Ai` is inverse functional, `<xRy>` and `<zRy>` should imply that `<x = z>`. But due to the inverse transitivity of `Bi`, it's possible for `<x>` and `<z>` to both relate to `<y>`, which contradicts the inverse functionality constraint that requires `<x>` and `<z>` to be the same. This inconsistency makes it incompatible for OWL reasoners.

## Conflict: Functional (B) and Inverse Transitive (Bi)

Suppose `B` is functional and `Bi` is inverse transitive. This corresponds to the second row (`B`) and the fourth column (`Bi`) of the table, marked with "N" (N<sub>8</sub>). If `B` is functional, for any `x`, `x` relates to at most one `y`. If `Bi` is inverse transitive, then if `xR⁻¹y` and `yR⁻¹z`, inverse transitivity implies `xR⁻¹z`.

This creates a conflict because `B`’s functionality requires `x` to relate to only one `y`, but the inverse transitivity of `Bi` suggests that `z` could relate back to `x` in multiple ways through different `y` values. This scenario breaks the "one-to-one" restriction of functionality, making it logically incompatible for OWL reasoners.

## Conflict: Inverse Functional (Bi) and Transitive (B)

Suppose `Bi` is inverse functional and `B` is transitive. This corresponds to the fourth row (`Bi`) and the second column (`B`) of the table, marked with "N" (N<sub>14</sub>). If `Bi` is inverse functional, for any `y`, `y` relates to at most one `x`. If `B` is transitive, then if `<x,y>` and `<y,z>` hold, then `<x,z>` must also hold due to transitivity.

This creates a conflict because the inverse functionality of `Bi` means that each `y` should relate to only one `x`, ensuring a "one-to-one" correspondence. However, `B` being transitive allows `x` to relate to multiple `z` values via different `y` values. This situation violates the inverse functionality constraint, as it implies that a single `y` could relate back to multiple `x` values through `B`'s transitivity. This conflict makes the properties incompatible for OWL reasoners.

### (C) Transitive and Inverse Functional
|              | A Trans | B Trans | Ai Trans | Bi Trans |
|--------------|---------|---------|----------|----------|
| **A iFunc**  | A<sub>1 | A<sub>2 | A<sub>3  | A<sub>4  |
| **B iFunc**  | A<sub>5 | A<sub>6 | A<sub>7  | A<sub>8  |
| **Ai iFunc** | A<sub>9 | A<sub>10| A<sub>11 | A<sub>12 |
| **Bi iFUnc** | A<sub>13| A<sub>14| A<sub>15 | A<sub>16 |

*_N denotes an error while Y denotes a successful run_

**_Subscripts denote corresponding explantion below_

## (D) Transitive and Asymmetric Trials
|              | A Trans | B Trans | Ai Trans | Bi Trans |
|--------------|---------|---------|----------|----------|
| **A  Asymm** | N<sub>1 | N<sub>2 | N<sub>3  | N<sub>4  |
| **B  Asymm** | N<sub>5 | N<sub>6 | N<sub>7  | N<sub>8  |
| **Ai Asymm** | Y<sub>9 | Y<sub>10| N<sub>11 | N<sub>12 |
| **Bi Asymm** | Y<sub>13| Y<sub>14| N<sub>15 | N<sub>16 |

*_N denotes an error while Y denotes a successful run_

**_Subscripts denote corresponding explantion below_

- **Conflict: Asymmetric Ai and Transitive A**  

  Suppose `Ai` is asymmetric, and `A` is transitive. This corresponds to the second row (Ai) and the second column (A) of the table, marked with "No". Since `A` is transitive, if `<x,y>` and `<y,z>` hold, then `<x,z>` must also hold due to transitivity. Since `Ai` is asymmetric, if `xRy`, then it is not the case that `yRx`.  

  This creates a conflict because `Ai` is asymmetric, meaning if `xRy`, then `yRx` must not hold. Since `A` is transitive, this implies that `x` could relate to multiple elements, which violates the asymmetric constraint of `Ai`. This contradiction makes it incompatible for OWL reasoners.

- **Conflict: Asymmetric A and Transitive Ai**  
  
  Suppose `A` is asymmetric, and `Ai` is transitive. This corresponds to the first row (A) and the third column (Ai) of the table, marked with "No". Since `A` is asymmetric, if `xRy` holds, then `yRx` cannot hold because `y` cannot be related to `x`.  

  This creates a conflict because `Ai` is transitive; if `<x,y>` and `<y,z>` hold, then `<x,z>` must also hold due to transitivity. However, asymmetry states that if `xRy`, then it is not the case that `yRx`. This contradiction makes it incompatible for OWL reasoners.

- **Conflict: Asymmetric A and Transitive B**  
  
  Suppose `A` is asymmetric, and `B` is transitive. This corresponds to the first row (A) and the fourth column (B) of the table, marked with "No". Since asymmetry holds, if `xRy`, then it is not the case that `yRx`.  
  
  This creates a conflict since `B` is transitive; because in transitivity, if `xRy` and `yRz`, then `xRz` must hold. However, asymmetry can’t hold because asymmetry prevents the case that `yRx`. This contradiction makes it incompatible for OWL reasoners.

- **Conflict: Asymmetric A and Transitive Bi**  
  
  Suppose `A` is asymmetric, and `Bi` is transitive. This corresponds to the first row (A) and the fifth column (Bi) of the table, marked with “N”. Since asymmetry holds, if `xRy`, then it is not the case that `yRx`.  
  
  This creates a conflict since `Bi` is transitive; because in transitivity, if `xRy` and `yRz`, then `xRz` must hold, but asymmetry can’t hold as asymmetry prevents the case that `yRx`. Since `xRy`, then `yRx` cannot hold because asymmetry prevents reciprocal relations. This contradiction makes it incompatible for OWL reasoners.

- **Conflict: Asymmetric Ai and Transitive B**  
  
  Suppose `Ai` is asymmetric, and `B` is transitive. This corresponds to the second row (Ai) and the second column (B) of the table, marked with “N”. Since asymmetry holds, if `xRy`, then it is not the case that `yRx`.  
  
  This creates a conflict since `B` is transitive; because in transitivity, if `xRy` and `yRz`, then `xRz` must hold, but asymmetry can’t hold as asymmetry prevents the case that `yRx`. Asymmetric `Ai` restricts reciprocal interactions, and transitive `B` extends relationships across elements to suggest connections that conflict with the asymmetry of `Ai`. This contradicti

- **Conflict: Asymmetric Ai and Transitive Bi**  
  
  Suppose `Ai` is asymmetric, and `Bi` is transitive. This corresponds to the second row (Ai) and the fourth column (Bi) of the table, marked with “N”. Since asymmetry holds, if `xRy`, then it is not the case that `yRx`.  
  
  This creates a conflict since `Bi` is transitive; because in transitivity, if `xRy` and `yRz`, then `xRz` must hold, but asymmetry can’t hold as asymmetry prevents the case that `yRx`. This contradiction makes it incompatible for OWL reasoners.

- **Conflict: Asymmetric B and Transitive Bi**  
  
  Suppose `B` is asymmetric, and `Bi` is transitive. This corresponds to the third row (B) and the fifth column (Bi) of the table, marked with “N”.  
  
  This creates a conflict since `Bi` is transitive; because in transitivity, if `xRy` and `yRz`, then `xRz` must hold, but asymmetry can’t hold as asymmetry prevents the case that `yRx`. This contradiction makes it incompatible for OWL

- **Conflict: Asymmetric Bi and Transitive Bi**  
  
  Suppose `Bi` is asymmetric, and `Bi` is transitive. This corresponds to the fourth row (Bi) and the fourth column (Bi) of the table, marked with “N”.  
  
  This creates a conflict since `Bi` is transitive; because in transitivity, if `xRy` and `yRz`, then `xRz` must hold, but asymmetry can’t hold as asymmetry prevents the case that `yRx`. This contradiction makes it incompatible for OWL reasoners.

### (E) Assymetric and Reflexive Trials
|              |A Reflex |B Reflex |Ai Reflex |Bi Reflex |
|--------------|---------|---------|----------|----------|
| **A Asymm**  | A<sub>1 | A<sub>2 | A<sub>3  | A<sub>4  |
| **B Asymm**  | A<sub>5 | A<sub>6 | A<sub>7  | A<sub>8  |
| **Ai Asymm** | A<sub>9 | A<sub>10| A<sub>11 | A<sub>12 |
| **Bi Asymm** | A<sub>13| A<sub>14| A<sub>15 | A<sub>16 |

*_N denotes an error while Y denotes a successful run_

**_Subscripts denote corresponding explantion below_