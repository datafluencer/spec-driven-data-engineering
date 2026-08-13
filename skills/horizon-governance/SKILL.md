# Snowflake Horizon Governance

## Purpose

This Skill defines reusable organizational standards for carrying approved
business context, meaning, and governance metadata into Snowflake Horizon
Catalog as part of delivering a governed analytical data product.

This is a **Snowflake governance-specific Skill**. It is reusable across
business domains and Snowflake data products, and it does not contain
project-specific implementation instructions for any specific
demonstration.

## Core Principle

Governance metadata is part of the delivered data product. It must not be
treated as optional documentation added after engineering is complete.

Approved business meaning should remain traceable through:

**Business Specification → Source Context → Analytical Model → Snowflake
Objects → Horizon Catalog → Semantic Layer → Consumption**

Horizon metadata must reflect approved business meaning rather than
independently inventing or redefining it.

## Required Dependencies

This Skill requires application of:

* `skills/naming-standards/SKILL.md`
* `skills/semantic-modeling/SKILL.md`
* `skills/data-quality-testing/SKILL.md`
* `skills/snowflake-engineering/SKILL.md`

Apply `skills/dimensional-modeling/SKILL.md` when governed objects are part
of a dimensional analytical model. Apply `skills/dbt-engineering/SKILL.md`
when dbt-generated artifacts contribute metadata or documentation to the
Snowflake implementation. Standards already owned by those Skills are not
duplicated here.

## Business Specification as Authority

The Business Specification is authoritative for approved business
terminology, business concepts, business definitions, KPIs and metrics,
analytical outcomes, and acceptance criteria. Horizon metadata must remain
consistent with those definitions.

Do not introduce new business definitions solely in catalog metadata. If
required business meaning is missing or ambiguous, surface the ambiguity
rather than creating a catalog definition that has not been approved.

## Source Context

Source Context is authoritative for how physical source structures relate
to approved business concepts. Use Source Context for lineage
interpretation, physical-to-business mappings, source meaning, and
approved source-specific semantics.

Do not expose cryptic physical source terminology as the primary business
description merely because it exists in the source.

## Governed Object Descriptions

Business-facing analytical objects should have meaningful descriptions
where supported. Descriptions should explain what an object represents in
business language. Descriptions must use approved business terminology,
communicate business purpose, be understandable without source-system
knowledge, remain consistent with the Business Specification, and avoid
simply repeating the physical object name.

For dimensional models, descriptions should make the represented business
concept or measurable business process understandable.

## Column and Attribute Descriptions

Important business-facing attributes should have meaningful descriptions
where supported. Prioritize descriptions for business identifiers,
important descriptive attributes, status fields, business dates, measures,
and fields necessary to interpret the analytical data correctly.

Do not generate meaningless descriptions solely to achieve metadata
coverage. For example, a description that merely restates an attribute
name without adding meaning does not satisfy this standard.

## Measures and Metrics

Governed measures and metrics must use definitions consistent with the
Business Specification and Semantic Modeling Skill. Metadata should make
clear, where appropriate, what the measure represents, how it should be
interpreted, important inclusions or exclusions, and relevant aggregation
behavior.

Do not create an alternative catalog definition of a metric that conflicts
with its approved business definition.

## Business Terminology Consistency

Apply `skills/naming-standards/SKILL.md`. The same approved business
concept should remain recognizable across analytical objects,
descriptions, catalog metadata, Semantic Views, and Snowflake Intelligence
consumption. Do not introduce unnecessary synonyms or alternative
terminology into governance metadata.

## Tags and Classification

Use Snowflake tags, classification, or related governance capabilities
only when there is an approved governance purpose. Examples of legitimate
purposes may include sensitivity classification, domain classification,
ownership categorization, lifecycle categorization, and governance status.

Do not create speculative classifications merely because Snowflake
supports them. Do not invent sensitivity levels, data classifications,
regulatory designations, ownership assignments, or retention policies that
are not established by approved requirements, organizational policy, or
available context.

## Sensitive Data

When approved context establishes that data requires sensitivity or
classification handling, apply the appropriate Snowflake governance
capability. Do not independently classify data based solely on assumptions
about column names. If a potentially sensitive attribute exists but
classification requirements are not established, surface the governance
question rather than inventing organizational policy.

## Ownership

Where ownership metadata is required, use explicitly established
ownership context. Do not invent business owners, technical owners, data
stewards, or organizational assignments. If ownership is required by
policy but no approved owner is available, identify the requirement as
BLOCKED.

## Discoverability

Horizon metadata should make governed analytical data easier to discover
and understand. Useful discoverability context may include clear object
names, business descriptions, attribute descriptions, governed
terminology, approved tags/classifications, semantic relationships, and
approved synonyms where supported and appropriate.

Discoverability must not come at the expense of correctness. Do not add
speculative keywords or synonyms simply to improve search results.

## Metadata Proportionality

Apply governance metadata proportionally. Not every technical object
requires the same level of business documentation. Prioritize
business-facing analytical and semantic objects. Technical staging or
intermediate objects may require sufficient metadata for engineering
lineage and maintainability without being presented as primary
business-consumption assets.

Avoid generating large quantities of low-value metadata merely to maximize
catalog coverage.

## Technical vs. Business Metadata

Maintain a distinction between **technical metadata** (physical object
names, data types, lineage, dependencies, technical ownership where
established) and **business metadata** (business definitions, business
terminology, KPI meaning, business descriptions, business relationships,
approved governance classifications).

Technical metadata must not substitute for missing business context.

## Lineage

Preserve and use lineage information where supported by Snowflake and the
implementation approach. Lineage should help consumers and engineers
understand how governed analytical objects relate to upstream data.

Lineage does not eliminate the need for Source Context. Physical lineage
answers where data came from. Source Context and business metadata explain
what that data means.

## Semantic Alignment

Apply `skills/semantic-modeling/SKILL.md`. Horizon metadata and
semantic-layer definitions must not conflict. Where the same business
concept, measure, metric, or definition appears in both governance
metadata and a Semantic View, the meaning must remain consistent. Do not
create parallel definitions that can drift independently.

## Snowflake Intelligence

When Snowflake Intelligence is part of the required consumption
experience, Horizon business context and the Semantic View should
reinforce the same governed meaning. Natural-language consumers should
encounter consistent business terminology, clear definitions, governed
measures, and understandable business concepts.

Do not optimize catalog metadata solely to influence AI-generated
responses. Governed business meaning takes precedence.

## Metadata Implementation

Apply `skills/snowflake-engineering/SKILL.md`. Use appropriate supported
Snowflake mechanisms for implementing approved metadata. The specific
mechanism may depend on the type of metadata and Snowflake capability
available. Do not assume that every governance requirement must be
implemented using the same metadata feature. Do not introduce
account-level governance objects or configurations beyond the approved
implementation scope.

## dbt Documentation Alignment

When dbt is used, apply `skills/dbt-engineering/SKILL.md`. dbt
documentation and Snowflake/Horizon metadata should reinforce the same
approved business meaning. Avoid maintaining unnecessarily conflicting
definitions in separate locations. Where metadata can be propagated or
reused appropriately, prefer consistency over manually creating divergent
descriptions. Do not assume dbt documentation alone satisfies Horizon
governance requirements.

## Governance Validation

Apply `skills/data-quality-testing/SKILL.md`. Governance requirements must
be validated as part of the definition of done. Validation should
determine, where applicable:

* required business-facing objects have meaningful descriptions
* important business attributes are documented
* business terminology matches the Business Specification
* KPI and measure definitions remain consistent
* required governance metadata exists
* required classifications/tags exist when explicitly required
* required ownership metadata exists when explicitly established
* Horizon metadata aligns with Semantic View definitions
* required business context is discoverable
* no unsupported business definitions were invented

## Acceptance Criteria

If the Business Specification requires Horizon Catalog business context or
governance metadata, that requirement must be evaluated explicitly. The
existence of Snowflake objects does not prove Horizon governance
acceptance criteria have been satisfied.

Use PASS / FAIL / BLOCKED behavior from
`skills/data-quality-testing/SKILL.md`. If required governance information
cannot be established because approved context is missing, report the
criterion as BLOCKED rather than inventing metadata.

## Governance Drift

Business meaning must not drift independently across the Business
Specification, analytical objects, dbt documentation, Horizon metadata,
and Semantic Views. When conflicting definitions are discovered, do not
silently reconcile them. Identify the conflict and determine which
approved artifact is authoritative.

The Business Specification remains authoritative for approved business
definitions unless governance explicitly establishes another approved
source of truth.

## Avoid Governance Theater

Do not treat metadata volume as evidence of governance quality. A governed
data product is not improved by descriptions that merely repeat object
names, speculative tags, invented ownership, excessive classifications,
duplicated definitions, or metadata that no consumer can interpret.

Prefer meaningful, accurate, traceable governance metadata over maximum
metadata coverage.

## Agent Decision Behavior

An engineering agent applying this Skill must:

1. Read the Business Specification.
2. Read applicable Source Context.
3. Read and apply applicable organizational Skills.
4. Identify which delivered Snowflake objects are business-facing.
5. Identify approved business terminology and definitions applicable to
   those objects.
6. Determine the minimum meaningful Horizon metadata required by the
   specification and governance standards.
7. Add meaningful business descriptions to appropriate analytical objects.
8. Add meaningful descriptions to important business-facing attributes
   where supported.
9. Apply approved measures and metric definitions consistently.
10. Apply tags, classifications, or ownership metadata only when approved
    context supports them.
11. Preserve lineage and source traceability.
12. Align Horizon metadata with the semantic layer.
13. Avoid speculative metadata and unsupported governance policy.
14. Validate governance completeness against the Business Specification.
15. Evaluate applicable acceptance criteria as PASS, FAIL, or BLOCKED.
16. Surface missing governance context rather than inventing it.
17. Do not declare the governed data product complete solely because
    Snowflake objects exist.

## Important Boundary

This Skill defines reusable Snowflake Horizon Governance standards. It
must NOT:

* reference any specific source system
* reference any specific project's business concepts
* reference any specific project database name
* create project-specific descriptions
* create project-specific tags
* create project-specific classifications
* invent project ownership
* invent sensitivity classifications
* define project-specific Semantic Views
* create SQL
* create dbt artifacts
* create Snowflake objects
* modify a Business Specification
* modify Source Context
* modify existing Skills
* redefine Naming Standards
* redefine Dimensional Modeling Standards
* redefine Semantic Modeling Standards
* redefine Data Quality & Testing Standards
* redefine dbt Engineering Standards
* redefine Snowflake Engineering Standards

Project-specific Horizon implementation must emerge from:

**Business Specification + Source Context + Analytical Implementation +
Applicable Skills**
