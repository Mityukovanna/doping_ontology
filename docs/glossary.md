# Glossary

Plain-language definitions for every class, object property, and data property
in the doping ontology. Each entry reflects the **current** schema. 

---

## Classes

### PermittedThreshold
The maximum number of allowed units that can be found either in the blood or urine sample for a Prohibited Substance.

### ProhibitedItem
A substance or a method banned under the WADA Prohibited List. This is a general category for a doping.
It exists so that properties shared by both
(`belongsToClass`, `hasUsageException`, `hasProhibitionContext`,
`hasSportSpecificRule`) only need to be defined once.

### ProhibitedSubstance
A chemical substance that is used to enhance performance in sports
(e.g. testosterone, salbutamol). Subclass of `ProhibitedItem`.
Disjoint with `ProhibitedMethod`. 

### ProhibitedMethod
Type of physiological manipulation that can be used to gain unfair advantage. 
For example, blood transfusion, gene doping.
Subclass of `ProhibitedItem`. Disjoint with `ProhibitedSubstance`.

### WADAClass
A category defined by WADA for groupping prohibited items. 

### SportSpecificRule
A rule that explains why a specific banned item is allowed in a particular sport. Exists because for some sports a prohibited item might be banned in-competition only and for others it might be banned at all times. Each rule links one `ProhibitedItem` to one `Sport` and one `ProhibitionContext`.

### UsageException
A condition under which the prohibited Item is allowed in competition. This covers therapeutic use exemptions (TUEs), permitted thresholds, and
sport-specific rules. Exists as its own class (rather than a
property directly on `ProhibitedItem`) because the exception itself
carries metadata that does not belong to the substance (type, approval body, whether formal approval is required)

### HealthCondition
A medical condition under which the use of a prohibited item is allowed. Justifies the `UsageException` 

### Sport
An Olympic sport or discipline, used to scope `SportSpecificRule`
entries (e.g. Archery, Shooting).

### PhysiologicalSystem
A physiological system within the body that is affected by a prohibited substance. This only connected to substances because the method's effect on the body is indirect.

### ProhibitionContext
The scope of a prohibition: `InCompetition`, `OutOfCompetition`, or
`AllTimes`. Defined as a closed set via `owl:oneOf`. These three
individuals are the *only* possible members; nothing else can ever be
asserted as a `ProhibitionContext`.

---

## Object Properties

### belongsToClass
*Domain:* `ProhibitedItem`

*Range:* `WADAClass`

*Not fucntional*: An item may belong to more than one WADA class

Links an item to the regulatory class(es) it falls under. 

### hasSportSpecificRule
*Domain:* `ProhibitedItem`

*Range:* `SportSpecificRule`

Links an item to a sport-specific override of its general prohibition
context. Absence of this relation means the item's prohibition context
applies uniformly across all sports.

### appliesToSport
*Domain:* `SportSpecificRule`

*Range:* `Sport`

*Functional*: each rule applies exactly to one sport

Identifies which sport a given `SportSpecificRule` applies to.

### hasProhibitionContext
*Domain:* `SportSpecificRule`

*Range:* `ProhibitionContext`

*Functional*: provides one context per item, one per rule

States whether an item (or a sport-specific rule) is prohibited
in-competition, out-of-competition, or at all times.

### hasUsageException
*Domain:* `ProhibitedItem`

*Range:* `UsageException`

*Not functional*: an item might have multiple usage exceptions 

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


The reverse of `appliesToCondition`. States what usage exception is applied to a given health condition

### affectsPhysiologicalSystem
*Domain:* `ProhibitedItem`*

*Range:* `PhysiologicalSystem`

States which bodily system a substance affects, and therefore why it
enhances performance. Only populated for the `ProhibitedSubstance` individuals.

---

## Data Properties

### isSpecified
*Domain:* `ProhibitedItem`

*Range:* `xsd:boolean`

Whether the item is classified by WADA as a "specified" substance/method
(generally subject to more lenient sanctioning) as opposed to
"non-specified."

### hasZeroTolerance
*Domain:* `ProhibitedItem`

*Range:* `xsd:boolean`

Whether any detected presence of the item constitutes a violation, as
opposed to being subject to a permitted threshold.

### casNumber
*Domain:* `ProhibitedSubstance`

*Range:* `xsd:string`

The CAS Registry Number. This is a universal chemical identifier. Retained
specifically as the key for future alignment with external databases
such as ChEBI, via `owl:sameAs`.

### permittedThreshold
*Domain:* `ProhibitedSubstance`

*Range:* `xsd:float`

The maximum concentration permitted before a result is treated as an
anti-doping rule violation. Absent for substances with zero tolerance.
Stored as a number (not a string) so it can be filtered, compared, and
sorted; always paired with `thresholdUnit`.

### thresholdUnit
*Domain:* `ProhibitedSubstance`

*Range:* `xsd:string`

The unit of measurement for `permittedThreshold` (e.g. "ng/mL").

### sampleType
*Domain:* `ProhibitedSubstance`

*Range:* `xsd:string`

Which biological sample type the threshold or detection applies to —
"urine", "blood", or "both".

### classCode
*Domain:* `WADAClass`

*Range:* `xsd:string`

The official WADA code for the class (e.g. "S1", "M1", "P1").

### exceptionType
*Domain:* `UsageException`

*Range:* `xsd:string`

The kind of exception (e.g. "TUE", "threshold",
"sport-specific").

### requiresFormalApproval
*Domain:* `UsageException`

*Range:* `xsd:boolean`

Whether this exception requires a formal application and approval

### approvalBody
*Domain:* `UsageException`

*Range:* `xsd:string`

The body responsible for granting the exception, where formal approval
is required (e.g. "WADA", "NADO", "IF").

### icdCode
*Domain:* `HealthCondition`

*Range:* `xsd:string`

External identifier for the condition, for future alignment with
ICD-11 or SNOMED CT.
