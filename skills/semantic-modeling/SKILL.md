# Semantic Modeling

## Purpose

This Skill defines reusable organizational standards for designing a
semantic layer that translates analytical data structures into consistent,
understandable business concepts for consumption.

This Skill is:

* platform-independent
* reusable across business domains
* reusable across analytical technologies
* independent of any specific demonstration or data product

This Skill defines **how semantic models should be designed**. It does not
define how to implement a Snowflake Semantic View or any other
platform-specific semantic implementation — those standards belong to
platform-specific Skills.

## Required Dependencies

This Skill requires application of:

* `skills/naming-standards/SKILL.md`
* `skills/dimensional-modeling/SKILL.md`, when the semantic layer is built
  over a dimensional model

Standards already defined by those Skills are not duplicated here. Wherever
this Skill produces a naming decision, apply the Naming Standards Skill.
Wherever this Skill's semantic layer sits atop a dimensional analytical
model, apply the Dimensional Modeling Skill for the underlying structure.

## Core Principle

The semantic layer is a **business-facing representation of governed
analytical meaning**. It should make analytical data understandable and
consistently usable without requiring consumers to understand physical
source systems, transformation logic, dimensional implementation details,
physical joins, technical keys, or other database-specific implementation
details.

Semantic models must be derived from approved business meaning rather than
reverse-engineered solely from physical analytical structures.

The engineering agent should reason through:

**Business Specification → Source Context → Analytical Model → Applicable
Skills → Semantic Model**

## Business Terminology

The Business Specification is authoritative for approved business
terminology. Semantic objects must use the terminology established by the
Business Specification. Apply `skills/naming-standards/SKILL.md`.

Do not expose source-system terminology simply because it exists
physically. Do not invent alternative synonyms for approved business
concepts unless synonyms are explicitly needed to improve natural-language
discovery and do not change business meaning.

## Business Concepts

Semantic models should expose the business concepts necessary to satisfy
the approved analytical requirements. A business concept may be represented
physically by one or more analytical objects, but the semantic layer should
present it coherently to consumers.

Do not expose every physical table or column simply because it exists.
Expose only concepts and attributes that have analytical or business value.

## Attributes and Dimensions

Business-facing attributes should:

* have understandable names
* have clear business meaning
* belong to the appropriate business concept
* support required filtering, grouping, and analysis
* hide unnecessary technical implementation details

Technical identifiers should not be exposed as primary consumer-facing
attributes unless they have legitimate analytical value.

## Measures

Measures represent quantifiable business values available for analysis.
Measures must:

* align with approved business definitions
* be calculated from appropriate underlying analytical data
* respect the grain of the underlying analytical model
* have clearly defined aggregation behavior
* avoid ambiguous or duplicate definitions

Where relevant, explicitly understand whether a measure is additive,
semi-additive, or non-additive. Do not assume that every numeric column is
a business measure.

## Metrics and Derived Business Calculations

Business metrics and derived calculations must be based on approved
definitions from the Business Specification or other approved business
context. Do not invent business metrics simply because the underlying data
makes them possible. Derived metrics must use approved component measures
consistently.

A semantic model must not create a second conflicting definition of a
metric already defined elsewhere in the governed data product.

## Metric Consistency

A business metric should have one governed meaning within the scope of a
data product unless an approved business requirement explicitly establishes
multiple contextual definitions. If multiple definitions of a business term
exist, do not silently choose one. Identify the ambiguity and require
explicit business context or approval.

## Relationships

Semantic relationships must reflect the approved business meaning of
relationships in the analytical model. Relationships must:

* have understood cardinality
* avoid unintended fan-out
* preserve correct measure aggregation
* support the analytical questions required by the Business Specification

Do not infer relationships solely because physical columns have similar
names. Do not expose consumers to physical join logic when the semantic
layer can represent the relationship in business terms.

## Grain and Aggregation Awareness

The semantic layer must respect the grain of underlying analytical
structures. Measures from different grains must not be combined in ways
that produce incorrect aggregation. The semantic model should make it
possible for consuming tools or agents to correctly aggregate approved
measures. Where ambiguity exists about aggregation behavior, identify it
rather than selecting behavior arbitrarily.

## Hierarchies

Where approved business concepts have meaningful analytical hierarchies,
expose those hierarchies consistently. Examples of hierarchy categories may
include time, geography, organization, and product classification.

Do not invent hierarchy levels not established by approved business
meaning. Semantic hierarchy design should make common business navigation
intuitive without simply mirroring physical foreign-key structures.

## Time Semantics

Time-based semantic attributes must clearly communicate the business event
represented by a date or timestamp. Do not expose multiple dates without
sufficient business meaning for consumers to understand their purpose.
Where multiple valid business dates exist, their roles must be explicit.

Do not invent fiscal-calendar semantics or other time definitions that are
not established by approved requirements or context.

## Descriptions and Business Definitions

Semantic objects should include meaningful descriptions where supported by
the target platform. Descriptions should communicate what the concept
represents, how a measure or metric is defined, important usage context,
and relevant interpretation necessary for correct analysis.

Descriptions should use business language. Do not simply repeat physical
object or column names as descriptions.

## Synonyms

Synonyms may be used to improve discoverability and natural-language
interaction when appropriate. Synonyms must:

* preserve the approved meaning of the business concept
* not introduce conflicting terminology
* not redefine a governed metric or business concept
* be understandable to intended consumers

Do not invent speculative synonyms solely to increase the number of terms
recognized by an AI system.

## Consumer Simplicity

The semantic model should reduce complexity for analytical consumers. Hide
implementation details that consumers do not need, including where
appropriate: surrogate keys, source-system keys, technical audit columns,
transformation-specific attributes, staging terminology, and physical join
mechanics.

Do not hide information required to correctly interpret the business data.

## Traceability

Semantic concepts, measures, and metrics must remain traceable to approved
Business Specification definitions, the analytical model, and relevant
Source Context where necessary. Traceability must allow an engineer or
reviewer to understand how a business concept is ultimately represented
without requiring consumers to work with physical source terminology.

## Semantic Layer and Physical Model Separation

The semantic model must not be treated as a replacement for sound physical
data modeling. A poorly designed physical analytical model should not be
compensated for by excessive semantic-layer complexity. Semantic design and
physical analytical design must work together. Where semantic requirements
expose a deficiency in the analytical model, identify the modeling issue
rather than masking it with unnecessary semantic logic.

## Avoid Semantic Sprawl

Do not create multiple semantic representations of the same business
concepts without an approved reason. Avoid duplicate metrics, conflicting
definitions, overlapping semantic models with unclear ownership, multiple
names for the same concept, and unnecessary exposure of physical objects.

Prefer the smallest coherent semantic model that satisfies the Business
Specification.

## Natural-Language Consumption

When the semantic layer will support natural-language analytical tools or
AI agents, it must provide sufficient business context for those tools to
interpret user questions correctly. This may include approved business
terminology, clear descriptions, meaningful relationships, governed
measures, governed metrics, useful approved synonyms, and understandable
dimensional attributes.

Do not optimize solely for natural-language convenience at the expense of
governed business meaning.

## Validation Expectations

A semantic model should be validated against the Business Specification.
Validation should determine whether:

* required business concepts are exposed
* required measures and metrics use approved definitions
* required relationships behave correctly
* required analytical questions can be answered
* business terminology is consistent
* aggregation behavior produces valid results
* semantic definitions do not conflict with the underlying analytical model
* unnecessary physical implementation details are not exposed to consumers

Specific testing and validation implementation belongs to applicable Data
Quality/Testing and platform-specific Skills.

## Agent Decision Behavior

An engineering agent applying this Skill must:

1. Read the Business Specification.
2. Read applicable Source Context.
3. Understand the analytical model that will support the semantic layer.
4. Apply `skills/naming-standards/SKILL.md`.
5. Apply `skills/dimensional-modeling/SKILL.md` when the underlying
   analytical model is dimensional.
6. Identify the business concepts required for consumption.
7. Identify approved measures and metrics.
8. Establish semantic relationships based on business meaning and correct
   cardinality.
9. Determine required attributes, hierarchies, descriptions, and approved
   synonyms.
10. Hide unnecessary technical implementation details.
11. Check for duplicate or conflicting business definitions.
12. Identify ambiguity rather than inventing semantic meaning.
13. Validate the resulting semantic model against the Business
    Specification's required analytical questions and acceptance criteria.

## Important Boundary

This Skill defines reusable semantic modeling standards. It must NOT:

* reference any specific source system
* reference project-specific business concepts
* design the semantic model for any specific demonstration
* create Snowflake Semantic View syntax
* prescribe Snowflake-specific semantic implementation
* create SQL
* create dbt artifacts
* create Snowflake objects
* implement Horizon Catalog metadata
* modify a Business Specification
* modify Source Context
* modify existing Skills
* duplicate Naming Standards
* duplicate Dimensional Modeling Standards

The project-specific semantic model must emerge from:

**Business Specification + Source Context + Analytical Model + Applicable
Skills**
