# Glossary

Plain-language definitions for every class, object property, and data property
in the doping ontology. Each entry reflects the **current** schema.

---

## Classes

### Doping
A substance or a method banned under the WADA Prohibited List. This is a general category for a doping.
It exists so that properties shared by both
(`belongsToClass`, `hasUsageException`, `appliesToSport`, `isSpecified`)
only need to be defined once.

### ProhibitedSubstance
A chemical substance that is used to enhance performance in sports
(e.g. testosterone, salbutamol). Subclass of `Doping`.
Disjoint with `ProhibitedMethod`.

### ProhibitedMethod
Type of manipulation that can be used to gain unfair advantage.
For example, blood transfusion, gene doping.
Subclass of `Doping`. Disjoint with `ProhibitedSubstance`.

### WADAClass
A category defined by WADA for groupping prohibited items.

### UsageException
A condition under which the prohibited Item is allowed in competition. This covers therapeutic use exemptions and permitted thresholds (its two subclasses). Exists as its own class (rather than a
property directly on `Doping`) because the exception itself
carries metadata that does not belong to the substance (approval body, whether formal approval is required).

### TherapeuticUseExemption
A usage exception granted on the basis of a documented health condition, permitting an athlete to use an otherwise-prohibited substance or method for legitimate medical treatment. Subclass of `UsageException`.

### PermittedThreshold
A usage exception under which a `ProhibitedSubstance` is permitted for use up to a defined concentration limit in a biological sample (blood or urine), rather than being subject to zero tolerance. Subclass of `UsageException`.

### HealthCondition
A medical condition under which the use of a prohibited item is allowed. Justifies the `UsageException`.

### Sport
An Olympic sport or discipline, used to scope prohibition via `hasProhibitionScope` (e.g. Archery, Shooting).

### PhysiologicalSystem
A physiological system within the body that is affected by a prohibited substance. This is only connected to substances because the method's effect on the body is indirect.

### ProhibitionScope
The scope of a prohibition. Defined as a closed set via `owl:oneOf`
containing exactly two individuals: `AllTimes` and `InCompetition`.
These are the *only* possible members; nothing else can ever be
asserted as a `ProhibitionScope`.

---

## Object Properties

### belongsToClass
*Domain:* `Doping`

*Range:* `WADAClass`

*Not functional*: An item may belong to more than one WADA class.

Links an item to the regulatory class(es) it falls under.

### appliesToSport
*Domain:* `Doping`

*Range:* `Sport`

*Functional*: each item applies to exactly one sport.

Identifies which sport a given item applies to.

### hasProhibitionScope
*Domain:* `Sport`

*Range:* `ProhibitionScope`

States whether a sport-specific rule is prohibited in-competition, or at all times.

### hasUsageException
*Domain:* `Doping`

*Range:* `UsageException`

*Not functional*: an item might have multiple usage exceptions.

Links an item to an exception that permits its use under defined
conditions.

### appliesToCondition
*Domain:* `UsageException`

*Range:* `HealthCondition`

States which health condition justifies a given usage exception.
Inverse of `permitsUseOf`.

### permitsUseOf
*Domain:* `HealthCondition`

*Range:* `UsageException`

The reverse of `appliesToCondition`. States what usage exception is applied to a given health condition.

### affectsPhysiologicalSystem
*Domain:* `ProhibitedSubstance`

*Range:* `PhysiologicalSystem`

States which bodily system a substance affects, and therefore why it
enhances performance. Only populated for the `ProhibitedSubstance` individuals.

---

## Data Properties

### isSpecified
*Domain:* `Doping`

*Range:* `xsd:boolean`

Whether the item is classified by WADA as a "specified" substance/method
(generally subject to more lenient sanctioning) as opposed to
"non-specified."

### hasZeroTolerance
*Domain:* `ProhibitedSubstance`

*Range:* `xsd:boolean`

Whether any detected presence of the item constitutes a violation, as
opposed to being subject to a permitted threshold.

### casNumber
*Domain:* `ProhibitedSubstance`

*Range:* `xsd:string`

The CAS Registry Number. This is a universal chemical identifier. Retained
specifically as the key for future alignment with external databases.

### sampleType
*Domain:* `PermittedThreshold`

*Range:* enumerated datatype (`owl:oneOf`) restricted to `"blood"` or `"urine"`

Which biological sample type the threshold or detection applies to.

### thresholdUnit
*Domain:* `PermittedThreshold`

*Range:* `xsd:string`

The unit of measurement for `thresholdValue` (e.g. "ng/mL").

### thresholdValue
*Domain:* `PermittedThreshold`

*Range:* `xsd:float`

The numeric concentration limit for a permitted threshold, paired with
`thresholdUnit` and `sampleType`.

### classCode
*Domain:* `WADAClass`

*Range:* `xsd:string`

The official WADA code for the class (e.g. "S1", "M1", "P1").

### requiresFormalApproval
*Domain:* `UsageException`

*Range:* `xsd:boolean`

Whether this exception requires a formal application and approval.

### icdCode
*Domain:* `HealthCondition`

*Range:* `xsd:string`

External identifier for the Physiological condition, for future alignment with
ICD-11 or SNOMED CT.
