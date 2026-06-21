# Glossary

Plain-language definitions for every class, object property, and data property
in the doping ontology. Each entry reflects the *current* schema — if a class
or property is renamed, removed, or restructured, update its entry here in the
same commit.

---

## Classes

### ProhibitedItem
The general category for anything banned under the WADA Prohibited List —
either a substance or a method. Exists so that properties shared by both
(`belongsToClass`, `hasUsageException`, `hasProhibitionContext`,
`hasSportSpecificRule`) only need to be defined once.

### ProhibitedSubstance
A chemical substance that is used to enhance performance in sports
(e.g. testosterone, salbutamol). Subclass of `ProhibitedItem`.
Disjoint with `ProhibitedMethod` — nothing can be both.

### ProhibitedMethod
A type of doping that involves some sort of manipulation with the health,
rather than the administration of a chemical substance (e.g. blood
transfusion, gene doping). Subclass of `ProhibitedItem`.
Disjoint with `ProhibitedSubstance`.

### WADAClass
A regulatory category defined by WADA for grouping prohibited items
(e.g. "S1 Anabolic Agents", "M1 Blood Manipulation"). Represented as a
class whose members are individuals (e.g. `S1_AnabolicAgents`), not as
subclasses of `ProhibitedItem` — this keeps the structure stable even
when WADA renames or reorganises categories from year to year.

### SportSpecificRule
A rule that overrides the general prohibition context of an item for one
particular sport. Exists because some substances (notably beta-blockers)
are not prohibited everywhere — they may be in-competition-only in some
sports and prohibited at all times in others. Each rule links one
`ProhibitedItem` to one `Sport` and one `ProhibitionContext`.

### UsageException
A condition under which an otherwise prohibited item is permitted —
covers therapeutic use exemptions (TUEs), permitted thresholds, and
sport-specific carve-outs. Exists as its own class (rather than a
property directly on `ProhibitedItem`) because the exception itself
carries metadata — type, approval body, whether formal approval is
required — that doesn't belong on the substance.

### HealthCondition
A medical condition that may justify a `UsageException` for an
otherwise-prohibited item (e.g. asthma, hypogonadism).

### Sport
An Olympic sport or discipline, used to scope `SportSpecificRule`
entries (e.g. Archery, Shooting).

### PhysiologicalSystem
A bodily system affected by a prohibited substance (e.g. cardiovascular
system, central nervous system). Only meaningfully connected to
substances, not methods — a method's effect on the body is indirect and
mediated by a technique, not a direct pharmacological action.

### ProhibitionContext
The scope of a prohibition: `InCompetition`, `OutOfCompetition`, or
`AllTimes`. Defined as a closed set via `owl:oneOf` — these three
individuals are the *only* possible members; nothing else can ever be
asserted as a `ProhibitionContext`.

---

## Object Properties

### belongsToClass
*Domain:* `ProhibitedItem` &nbsp;→&nbsp; *Range:* `WADAClass`

Links an item to the regulatory class(es) it falls under. Not functional
— an item may belong to more than one WADA class.

### hasSportSpecificRule
*Domain:* `ProhibitedItem` &nbsp;→&nbsp; *Range:* `SportSpecificRule`

Links an item to a sport-specific override of its general prohibition
context. Absence of this relation means the item's prohibition context
applies uniformly across all sports.

### appliesToSport
*Domain:* `SportSpecificRule` &nbsp;→&nbsp; *Range:* `Sport`

Identifies which sport a given `SportSpecificRule` applies to.
Functional — each rule applies to exactly one sport (create a separate
rule individual per sport if the same context applies to several).

### hasProhibitionContext
*Domain:* `ProhibitedItem` or `SportSpecificRule` &nbsp;→&nbsp;
*Range:* `ProhibitionContext`

States whether an item (or a sport-specific rule) is prohibited
in-competition, out-of-competition, or at all times. Functional in both
uses — one context per item, or per rule.

### hasUsageException
*Domain:* `ProhibitedItem` &nbsp;→&nbsp; *Range:* `UsageException`

Links an item to an exception that permits its use under defined
conditions. Not functional — an item may have multiple distinct
exceptions (e.g. both a threshold and a TUE pathway).

### appliesToCondition
*Domain:* `UsageException` &nbsp;→&nbsp; *Range:* `HealthCondition`

States which health condition justifies a given usage exception.
Inverse of `permitsUseOf`.

### permitsUseOf
*Domain:* `HealthCondition` &nbsp;→&nbsp; *Range:* `UsageException`
*(inferred via inverseOf — not independently asserted)*

The reverse of `appliesToCondition`: given a health condition, which
usage exceptions does it justify.

### affectsPhysiologicalSystem
*Domain:* `ProhibitedItem`* &nbsp;→&nbsp; *Range:* `PhysiologicalSystem`

States which bodily system a substance affects, and therefore why it
enhances performance. *In practice, only meaningfully populated for
`ProhibitedSubstance` individuals.

---

## Data Properties

### wadaCode
*Domain:* `ProhibitedItem` &nbsp;→&nbsp; *Range:* `xsd:string`

The primary WADA code most closely identifying this item (e.g. "S1").
A convenience shortcut — full classification detail lives in
`belongsToClass`.

### isSpecified
*Domain:* `ProhibitedItem` &nbsp;→&nbsp; *Range:* `xsd:boolean`

Whether the item is classified by WADA as a "specified" substance/method
(generally subject to more lenient sanctioning) as opposed to
"non-specified."

### hasZeroTolerance
*Domain:* `ProhibitedItem` &nbsp;→&nbsp; *Range:* `xsd:boolean`

Whether any detected presence of the item constitutes a violation, as
opposed to being subject to a permitted threshold.

### casNumber
*Domain:* `ProhibitedSubstance` &nbsp;→&nbsp; *Range:* `xsd:string`

The CAS Registry Number — a universal chemical identifier. Retained
specifically as the key for future alignment with external databases
such as ChEBI, via `owl:sameAs`.

### permittedThreshold
*Domain:* `ProhibitedSubstance` &nbsp;→&nbsp; *Range:* `xsd:float`

The maximum concentration permitted before a result is treated as an
anti-doping rule violation. Absent for substances with zero tolerance.
Stored as a number (not a string) so it can be filtered, compared, and
sorted; always paired with `thresholdUnit`.

### thresholdUnit
*Domain:* `ProhibitedSubstance` &nbsp;→&nbsp; *Range:* `xsd:string`

The unit of measurement for `permittedThreshold` (e.g. "ng/mL").

### sampleType
*Domain:* `ProhibitedSubstance` &nbsp;→&nbsp; *Range:* `xsd:string`

Which biological sample type the threshold or detection applies to —
"urine", "blood", or "both".

### classCode
*Domain:* `WADAClass` &nbsp;→&nbsp; *Range:* `xsd:string`

The official WADA code for the class (e.g. "S1", "M1", "P1").

### exceptionType
*Domain:* `UsageException` &nbsp;→&nbsp; *Range:* `xsd:string`

The kind of exception this is — e.g. "TUE", "threshold",
"sport-specific".

### requiresFormalApproval
*Domain:* `UsageException` &nbsp;→&nbsp; *Range:* `xsd:boolean`

Whether this exception requires a formal application and approval
(as with a TUE), as opposed to applying automatically (as with a
permitted threshold).

### approvalBody
*Domain:* `UsageException` &nbsp;→&nbsp; *Range:* `xsd:string`

The body responsible for granting the exception, where formal approval
is required (e.g. "WADA", "NADO", "IF").

### icdCode
*Domain:* `HealthCondition` &nbsp;→&nbsp; *Range:* `xsd:string`

External identifier for the condition, for future alignment with
ICD-11 or SNOMED CT.

---

## Deliberately excluded

These were considered during design but removed — listed here so the
decision isn't accidentally re-introduced or re-litigated without reason.
See `design-decisions.md` for the full reasoning behind each.

| Term | Why it was cut |
|---|---|
| `SubstanceClass` (as subclasses like `AnabolicAgent`) | Duplicated `WADAClass`; WADA categories change yearly, so they belong as individuals, not structural classes |
| `allowedIn` / `BANNED_IN` | Redundant pairing; replaced by `hasSportSpecificRule` + `appliesToSport` |
| `restrictedToSport` (standalone) | Superseded by `SportSpecificRule`, which also carries context |
| `SportScope` | An empty/non-empty `hasSportSpecificRule` already conveys this; no separate flag needed |
| `hasMetabolite` | Answers a detection/testing question, which is explicitly out of scope |
| `hasMember` (inverse of `belongsToClass`) | No competency question needs the reverse direction |
| `chemicalFormula` | Not required by any competency question; not a unique identifier |
| `commonSynonyms`, exception `description`, method `mechanism`/`biologicalTarget` | Free-text convenience fields with no competency question behind them |
| `officialName`, `className`, `conditionName`, `sportName` | Duplicated the individual's own name/IRI |
| `governingBody` | Out of scope — no competency question asks about governing bodies |
| `NonSpecifiedSubstance` (defined class) | A one-line query already answers competency question 3; not worth the added schema complexity |