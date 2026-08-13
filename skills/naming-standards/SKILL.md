# Naming Standards

## Purpose

This Skill defines reusable naming standards for data engineering and data
modeling artifacts. It captures organizational engineering standards, not
requirements specific to any single business domain, source system,
Snowflake project, dimensional model, or other data product.

This Skill is intended to be applied by an engineering agent (or an
engineer) whenever objects, attributes, measures, or metrics are named as
part of implementing a data product from an approved Business Specification
and its associated Source Context.

## Core Naming Principle

Names must communicate **business meaning**, not merely reproduce physical
source-system terminology.

When a Business Specification establishes an approved business term, that
terminology is authoritative for consumer-facing data products. Source
Context may map physical source terminology to a different, approved
business term. Where a source system uses one term but the Business
Specification defines another, consumer-facing artifacts must use the
approved business term, not the source-system term.

## General Standards

Names must be:

* Clear
* Consistent
* Descriptive
* Business meaningful
* Unambiguous
* Predictable
* Appropriate to the object's purpose and grain

Avoid:

* Unexplained abbreviations
* Cryptic source-system codes
* Physical source names used solely because they exist in the source
* Inconsistent synonyms for the same business concept
* Technical implementation terminology exposed unnecessarily to business
  consumers

## Business Terminology

The Business Specification is authoritative for approved business
terminology. When naming business-facing entities, dimensions, facts,
attributes, measures, metrics, semantic objects, and catalog descriptions,
use the terminology established by the Business Specification.

Use Source Context to translate physical source-system terminology into
approved business terminology. Do not infer that a source-system object
name is the correct business term merely because it exists in the source.

## Naming Structure

Use meaningful noun-based names for business concepts. Names should
communicate the subject represented by the object or attribute.

Object names representing a business concept must use the singular form of
the approved business term, even when the object contains multiple
instances of that concept. For dimensional modeling, this means the
business-concept portion of a dimension or fact name must be singular —
for example, a pattern such as `DIM_<singular business concept>` or
`FACT_<singular business process or concept>`, rather than a pluralized
form of that concept.

Use consistent class words where they improve clarity and predictability.
Useful class-word concepts include:

* Identifier
* Name
* Description
* Code
* Type
* Category
* Status
* Date
* Timestamp
* Amount
* Quantity
* Count
* Percentage
* Indicator

A class word describes the nature of an attribute; it does not by itself
constitute a complete, contextually clear column name. Generic class words
must not normally be used alone as a complete column name — for example, a
monetary attribute should combine its business subject with an appropriate
monetary class word (business subject + `Amount`), and a quantity attribute
should combine its business subject with `Quantity` (business subject +
`Quantity`), rather than using the class word alone. The general pattern is
`<business subject>_<attribute or class word>`. An object or table name
that supplies the missing business subject does not satisfy this
requirement — a column name must remain understandable on its own, without
depending solely on the object that contains it.

Where an approved business concept appears within a column name, use the
singular form of the approved business term, consistent with the Business
Specification's authoritative terminology. Do not invent alternate
singular terminology.

Do not force a class word, and do not repeat a business-subject qualifier,
where doing so would create meaningless redundancy (for example, restating
the same word twice) rather than added clarity. This narrow exception does
not permit omitting the business subject from a column name; the intent is
contextual clarity, not mechanical duplication.

## Identifiers

Business identifiers and technical identifiers must be distinguishable
where both exist. A name should make it clear whether an identifier
represents:

* a business/source identifier
* an analytical surrogate identifier
* another technical identifier

Do not expose implementation-specific surrogate keys as meaningful business
identifiers. Where surrogate keys are used internally, their naming should
be consistent and predictable.

## Boolean / Indicator Attributes

Boolean or indicator attributes should have names that clearly communicate
the condition represented. Avoid ambiguous names where the meaning of
true/false cannot be understood from the attribute name alone. Prefer names
that communicate a business state or condition rather than a raw technical
flag.

## Dates and Timestamps

Names must distinguish dates from timestamps where the distinction
matters. Use consistent class words such as `Date` and `Timestamp`. Do not
use vague names such as `Time` when the attribute actually represents a
timestamp.

Names should communicate the business event represented by the date or
timestamp (for example, the business moment it captures), rather than
simply identifying its datatype.

## Measures and Metrics

Measures and metrics should use business terminology established in the
Business Specification. Do not rename business-defined metrics merely to
match physical source-system columns. A metric name must represent the
business meaning of the calculation, not the physical expression used to
derive it.

## Dimensional Modeling

When dimensional modeling conventions require technical prefixes or naming
patterns, apply them consistently. Where appropriate, recognizable patterns
such as `DIM_` for dimensions and `FACT_` for facts may be used for
physical analytical objects. However, the business concept portion of the
name must use approved business terminology.

This Skill defines the naming principles; a Dimensional Modeling Skill
determines when dimensional object patterns apply.

## Semantic and Consumer-Facing Naming

Semantic-layer names should prioritize business readability. Avoid
exposing technical prefixes, source-system abbreviations, physical key
names, or transformation terminology when they do not provide value to the
business consumer.

Semantic names should align with the Business Specification and the
business definitions used throughout the data product.

## Snowflake Object Naming

For Snowflake implementations, all physical Snowflake identifiers must use
`UPPER_SNAKE_CASE`. This applies, where relevant, to databases, schemas,
tables, views, columns, Semantic Views, and other named Snowflake objects.

Physical Snowflake identifiers must:

* use uppercase names
* separate words with underscores
* contain no spaces
* remain understandable without requiring source-system knowledge

Do not introduce mixed-case or lowercase physical Snowflake identifiers.
Quoted mixed-case identifiers must not be introduced merely to preserve
alternative casing. Avoid unnecessary quoted identifiers generally.

Do not establish project-specific database or schema names in this
reusable Skill. Specific database/schema naming requirements may be defined
by project context or a Snowflake Engineering Skill.

## Naming Consistency Across Layers

Business terminology should remain traceable and consistent across the
engineering lifecycle. Where appropriate, the same approved business
concept should be recognizable across: Business Specification → dimensional
model → catalog metadata → semantic layer → analytical consumption.

Physical implementation conventions may add technical structure, but they
must not change the underlying business meaning.

## Source-System Names

Physical source-system names should be preserved where required for source
references, lineage, traceability, staging, ingestion, and technical
mappings. They should not automatically become consumer-facing names.
Source Context determines how physical source terminology maps to business
concepts.

## Avoid Unnecessary Renaming

Do not rename objects merely for stylistic preference. A naming
transformation should have a reason, such as:

* alignment with approved business terminology
* compliance with organizational naming standards
* removal of cryptic source terminology
* consistency across the analytical data product

## Agent Decision Behavior

An engineering agent applying this Skill must:

1. Read the Business Specification to identify approved business
   terminology.
2. Read applicable Source Context to understand differences between
   physical source terminology and business terminology.
3. Preserve physical source names where necessary for source access and
   traceability.
4. Use approved business terminology for consumer-facing analytical
   objects.
5. Apply naming conventions consistently across the implementation.
6. Avoid inventing synonyms for business concepts already defined by the
   specification.
7. Identify terminology ambiguity rather than choosing a new business term
   without approval.
8. Validate the resulting implementation for naming consistency before
   considering the work complete.

## Important Boundary

This Skill defines **reusable naming standards**. It must not:

* define project-specific object names
* reference any specific source system
* reference any specific business domain's terminology
* design a dimensional model
* create SQL
* create dbt artifacts
* create Snowflake objects
* create a Semantic View
* implement Horizon Catalog metadata
* modify a Business Specification
* modify Source Context
* introduce new business terminology

Project-specific names must emerge from:

**Business Specification + Source Context + applicable Skills**
