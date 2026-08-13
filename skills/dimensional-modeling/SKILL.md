# Dimensional Modeling

## Purpose

This Skill defines reusable organizational standards for dimensional
modeling. It is intended to be applied by an engineering agent (or an
engineer) when deriving an analytical dimensional model from an approved
Business Specification and its associated Source Context.

This Skill is reusable across business domains, source systems, Snowflake
projects, and analytical data products. It does not describe or design the
dimensional model for any specific data product or demonstration.

## Required Dependency

This Skill requires application of `skills/naming-standards/SKILL.md` for
all naming decisions made while performing dimensional modeling. Naming
standards are defined there and are not duplicated here. Wherever this
Skill produces a naming decision — for facts, dimensions, attributes,
measures, keys, or any other named object — the engineering agent must
apply the Naming Standards Skill to make that decision.

## Core Principle

Dimensional models must be derived from business requirements and business
meaning, not mechanically generated from source-system structures.

The engineering agent should reason through:

**Business Specification → Source Context → Applicable Skills → Dimensional
Model**

A source table does not automatically become a dimension or fact. A
physical source relationship does not automatically dictate the analytical
model.

## Business Process

Before designing fact structures, identify the measurable business process
or processes required by the Business Specification. The model must
support the analytical outcomes, KPIs, and questions defined by the
specification. Do not model additional business processes simply because
source data exists for them.

## Grain

The grain of every fact must be explicitly determined before measures and
dimensional relationships are finalized. The grain must state, in business
language, what one row represents. All measures and dimensional
relationships associated with the fact must be compatible with its declared
grain. Do not mix incompatible grains without an explicit requirement and
modeling justification.

## Fact Modeling

Facts should represent measurable business processes. Fact structures
should:

* contain measures appropriate to their declared grain
* contain dimensional relationships necessary to analyze the process
* preserve required business/source identifiers for traceability where
  appropriate
* avoid unnecessary duplication of descriptive dimensional attributes

Where relevant, understand measures according to their aggregation
behavior: additive, semi-additive, non-additive, or derived. Derived
business metrics should generally be calculated from their approved
component measures rather than independently persisted when doing so could
create inconsistent definitions.

## Dimension Modeling

Dimensions represent descriptive business concepts used to filter, group,
categorize, label, or provide analytical context to measurable business
processes.

Dimensions must originate from approved business concepts. Do not create a
dimension merely because a source table exists. Use the Business
Specification to determine required concepts and Source Context to
understand how those concepts are physically represented.

## Surrogate Keys and Business Identifiers

Where appropriate, dimensional models should use surrogate keys to separate
analytical identity from operational/source-system identity. Retain
necessary business/source identifiers for traceability and reconciliation.
Generated surrogate keys must not be treated as business identifiers. Apply
`skills/naming-standards/SKILL.md` for naming.

## Conformed Dimensions

When the same business concept participates in multiple measurable
business processes, prefer a consistent/conformed dimensional
representation unless approved requirements require otherwise. Avoid
multiple conflicting representations of the same business concept merely
because different physical source structures provide it.

## Date Dimensions

When requirements include time-based analysis, provide an appropriate Date
dimension. At minimum, a Date dimension should be capable of supporting
commonly required calendar attributes such as calendar date, day, month,
month name, quarter, and year.

Only implement fiscal-calendar attributes when approved requirements or
context define the fiscal calendar. Never invent fiscal-calendar rules.

Where multiple business dates are analytically required, the same Date
dimension may participate through different roles. Do not create
role-playing date relationships that are not required.

## Hierarchies

Where approved business concepts participate in analytical hierarchies,
preserve those relationships in a form suitable for analysis. Hierarchy
design must follow approved business meaning rather than simply
reproducing physical source foreign-key chains. Do not invent
organizational, geographic, product, or other hierarchies.

## Fact-to-Dimension Relationships

Relationships must:

* reflect approved business meaning
* be compatible with the fact grain
* support the analytical questions defined by the Business Specification
* avoid accidental many-to-many relationships and measure fan-out

If a many-to-many relationship is genuinely required, model it
deliberately.

## Unknown and Unresolved Members

Missing or unresolved dimensional references must be handled deliberately.
Do not silently discard valid fact records solely because a corresponding
dimension member cannot be resolved. Use a consistent unknown/unresolved-
member strategy where appropriate. Unresolved relationships should be
detectable through data-quality validation.

## Slowly Changing Dimensions

Do not automatically implement Slowly Changing Dimension Type 2. Determine
dimensional history requirements from approved business requirements,
required analytical behavior, and available source history. If historical
attribute tracking is required but the source cannot provide sufficient
history, identify the gap rather than manufacturing historical state.

## Degenerate Dimensions

Business identifiers useful for analysis that do not require their own
descriptive dimension may remain on a fact as degenerate dimensions where
appropriate. Use this deliberately rather than as a default replacement for
dimensional modeling.

## Advanced Dimensional Patterns

Do not introduce patterns such as bridge tables, junk dimensions,
mini-dimensions, accumulating snapshots, periodic snapshots, factless
facts, or speculative aggregate tables unless they are required by the
approved business requirements and grain. Prefer the simplest dimensional
structure that correctly satisfies the specification.

## Source Traceability

The implementation must preserve sufficient traceability to understand how
important dimensions, identifiers, measures, and relationships relate to
Source Context. Traceability does not require exposing physical source
terminology as consumer-facing terminology.

## Data Quality Expectations

The dimensional design must support validation of at least:

* uniqueness of dimension identifiers
* uniqueness at the declared fact grain
* required/not-null keys
* valid fact-to-dimension relationships
* detection of unresolved dimensional references
* accepted values where approved business rules establish them
* consistency of measures with approved business definitions

Do not prescribe a specific testing technology in this Skill. Testing
implementation belongs to Data Quality/Testing and applicable
engineering-tool Skills.

## Avoid Over-Modeling

Build the smallest coherent dimensional model that satisfies the Business
Specification. Do not create structures merely because they might someday
be useful. The existence of source data does not constitute a requirement
to model it.

## Agent Decision Behavior

An engineering agent applying this Skill must:

1. Read the Business Specification.
2. Read all applicable Source Context.
3. Apply `skills/naming-standards/SKILL.md`.
4. Identify the measurable business process or processes required by the
   specification.
5. Determine and explicitly document fact grain before finalizing facts.
6. Identify dimensions from approved business concepts rather than source
   tables.
7. Identify measures from approved business KPI definitions.
8. Establish fact-to-dimension relationships consistent with grain and
   business meaning.
9. Determine whether surrogate keys, conformed dimensions, Date
   dimensions, or other dimensional patterns are required.
10. Avoid structures unnecessary to satisfying the specification.
11. Identify unresolved business or Source Context ambiguity rather than
    inventing rules.
12. Validate the proposed and implemented dimensional model against the
    required KPIs, business questions, and acceptance criteria.

## Important Boundary

This Skill defines reusable dimensional modeling standards. It must NOT:

* design a current or specific dimensional model
* reference any specific source system
* reference project-specific business concepts
* prescribe project-specific fact or dimension names
* contain project-specific KPI definitions
* create SQL
* create dbt artifacts
* create Snowflake objects
* create a Semantic View
* implement Horizon Catalog metadata
* modify a Business Specification
* modify Source Context
* modify the Naming Standards Skill
* duplicate Naming Standards

The project-specific dimensional model must emerge from:

**Business Specification + Source Context + Applicable Skills**
