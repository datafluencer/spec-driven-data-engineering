# Snowflake Engineering

## Purpose

This Skill defines reusable organizational standards for implementing
governed analytical data products on Snowflake.

This is a **platform-specific Skill**. It defines how an engineering agent
should use Snowflake capabilities to implement requirements derived from:

**Business Specification + Source Context + Applicable Skills**

It does not contain project-specific implementation instructions for any
specific demonstration, and it does not reference any specific source
system, specific project business concepts, or specific project database
names.

## Required Dependencies

This Skill requires application of the existing Skills where applicable:

* `skills/naming-standards/SKILL.md`
* `skills/dimensional-modeling/SKILL.md`
* `skills/semantic-modeling/SKILL.md`
* `skills/data-quality-testing/SKILL.md`
* `skills/dbt-engineering/SKILL.md`, when dbt is used

Standards already owned by those Skills are not duplicated here. This
Skill defines how those standards are realized on the Snowflake platform.

## Core Principle

Snowflake is the target engineering platform, not the source of business
meaning. An engineering agent must not infer business requirements merely
from databases, schemas, tables, views, columns, Snowflake metadata,
source-system objects, existing SQL, or existing Snowflake objects.
Business meaning originates from approved specifications and context.

The expected reasoning chain is:

**Business Specification → Source Context → Applicable Skills → Snowflake
Implementation → Validation**

## Inspect Before Changing

Before creating or modifying Snowflake objects, the engineering agent must
inspect the available environment and determine: current database context,
available schemas, available warehouses, available roles and privileges
where visible, existing objects relevant to the implementation, source
objects required by Source Context, target environment constraints, and
existing dbt project or Snowflake project configuration where applicable.

Do not assume an object does not exist without checking. Do not overwrite
or replace existing objects unless the implementation explicitly requires
it and the action is safe within the approved scope.

## Scope Discipline

Create only Snowflake artifacts necessary to satisfy the Business
Specification and applicable acceptance criteria. Do not create
speculative schemas, tables, views, stages, procedures, functions, tasks,
streams, dynamic tables, or semantic objects simply because Snowflake
supports them.

Prefer the smallest coherent Snowflake implementation that satisfies the
approved requirements.

## Database and Schema Context

Use the project-provided target database and environment context when one
has been explicitly established. Do not invent database or schema names
when required context is missing. Create schemas only when they have a
clear engineering purpose. Where multiple schemas are used, their
responsibilities should be understandable and intentional. Avoid
unnecessary schema proliferation for small data products.

Specific project database names and environment names belong to project
context, not this reusable Skill.

### Delivered Business Data-Product Schema

The schema hosting the delivered, consumer-facing business data product
must not be the Snowflake `PUBLIC` schema. `PUBLIC` is a default system
schema, not a governed business-domain location, and must not be used as
the destination for governed analytical data-product objects.

When an approved target schema for the data product has not been
explicitly established by project context, the engineering agent must
derive a business-domain-oriented schema name from the approved Business
Specification (and Source Context where necessary), create that schema if
it does not already exist, and name it according to
`skills/naming-standards/SKILL.md`. The derived name must be reasonably
traceable to the business domain described by the approved Business
Specification. Do not invent a business domain that cannot reasonably be
derived from approved artifacts, and do not default to `PUBLIC` merely
because a schema name was not explicitly provided.

## Naming

Apply `skills/naming-standards/SKILL.md`. Physical Snowflake objects should
follow the approved Snowflake-compatible naming conventions defined there.
Do not create a separate or conflicting Snowflake naming philosophy in this
Skill. Business-facing Snowflake objects must preserve approved business
terminology.

## Snowflake dbt Projects

When the implementation uses dbt Projects on Snowflake, apply
`skills/dbt-engineering/SKILL.md`. Snowflake dbt Projects should be
treated as the dbt execution and development mechanism, not as a source of
business requirements.

The engineering agent should use the approved Business Specification and
Source Context to determine required transformations, apply the dbt
Engineering Skill, preserve understandable dbt lineage, use appropriate
dbt source and model dependency mechanisms, apply organizational testing
standards, and avoid unnecessary models, layers, packages, macros, and
materializations.

Snowflake-specific project/environment configuration should be kept
minimal and intentional. Do not create a dbt project merely because dbt is
available unless the project requirements or approved implementation
approach call for dbt.

## SQL and Object Creation

Snowflake SQL should be explicit, readable, maintainable, appropriately
qualified, safe within the approved target scope, and traceable to the
requirements it implements.

Avoid unnecessary use of platform-specific features when straightforward
relational transformations satisfy the requirement. Use Snowflake-specific
capabilities when they provide clear value to the approved solution.

## Fully Qualified References

Use sufficiently qualified object references where necessary to prevent
ambiguity between environments or databases. Do not hard-code
environment-specific names throughout reusable transformation logic when
configuration or project context provides a better mechanism. Physical
source references may use fully qualified Snowflake object names where
Source Context requires them.

## Data Types

Choose Snowflake data types deliberately based on the semantic meaning and
expected use of the data. Do not blindly inherit inappropriate source
datatypes when transformation into an analytical structure requires a more
suitable representation. Avoid unnecessary precision or oversized string
definitions without justification. Do not change the meaning of data
through implicit type assumptions.

## Keys and Identifiers

Apply the applicable Naming and Dimensional Modeling Skills. Where
analytical surrogate identifiers are required, implement them consistently
and deterministically where practical. Do not confuse Snowflake-generated
or technical identifiers with business identifiers. Preserve required
source/business identifiers for lineage and reconciliation.

## Dimensional Implementation

When the required analytical solution uses dimensional modeling, apply
`skills/dimensional-modeling/SKILL.md`. Snowflake physical structures must
preserve declared fact grain, dimension identity, required relationships,
measure compatibility with grain, and necessary traceability. Do not allow
Snowflake implementation convenience to override approved dimensional
design principles.

## Views, Tables, and Other Physical Structures

Choose physical object types deliberately. Use tables, views, dynamic
tables, or other Snowflake capabilities based on actual requirements such
as persistence, performance, freshness, transformation ownership, and
operational complexity. Do not introduce advanced Snowflake capabilities
when a simpler structure satisfies the requirement. For small or
demonstration workloads, prefer simplicity unless requirements dictate
otherwise.

## Performance

Design for reasonable Snowflake performance without premature
optimization. Do not introduce clustering, specialized physical
optimization, materialized views, search optimization, or other
performance features without an identified need.

First prioritize correct grain, correct relationships, appropriate
transformations, understandable SQL, and appropriate model structure.
Optimize based on demonstrated or expected requirements rather than
speculation.

## Compute and Cost Awareness

Use Snowflake compute deliberately. Avoid unnecessary repeated execution of
expensive transformations. Use appropriately scoped warehouses and
existing project compute context where provided. Do not create warehouses
or alter account-level compute settings unless explicitly required and
within approved scope. For demonstrations and small workloads, favor
simplicity and controlled compute use.

## Security and Privileges

Operate with least privilege. Do not broaden privileges unnecessarily,
change account-level security configuration, grant elevated roles without
explicit authorization, modify unrelated objects, expose restricted data,
or weaken governance controls to make an implementation work.

If required privileges are unavailable, identify the implementation as
BLOCKED rather than attempting to bypass controls.

## Descriptions and Metadata

Where Snowflake supports descriptive metadata on created analytical
objects, provide meaningful business-oriented descriptions where
appropriate. Descriptions should use approved business terminology,
explain business meaning, support discoverability, and remain consistent
with the Business Specification. Do not substitute technical column names
for meaningful descriptions.

Detailed Horizon Catalog governance standards belong to
`skills/horizon-governance/SKILL.md` when that Skill is available. Do not
duplicate Horizon governance standards here.

## Semantic Views

When the Business Specification requires a Snowflake Semantic View, apply
`skills/semantic-modeling/SKILL.md`. The Snowflake Semantic View is the
platform implementation of the approved semantic model.

The engineering agent must derive the Semantic View from approved business
concepts, the implemented analytical model, approved measures and metrics,
approved relationships, and semantic modeling standards. Do not simply
expose every analytical table and column. The Semantic View should expose
the minimum coherent semantic representation required to satisfy the
Business Specification.

## Semantic View Business Meaning

Snowflake Semantic View objects should use approved business terminology
and meaningful descriptions. Where supported and appropriate, semantic
metadata may include business concepts, dimensions, measures, metrics,
relationships, hierarchies, descriptions, and approved synonyms.

Do not invent semantic definitions solely to improve natural-language
query behavior. Business meaning remains governed by the Business
Specification and applicable Semantic Modeling Skill.

## Snowflake Intelligence Readiness

When the Business Specification requires consumption through Snowflake
Intelligence, the engineering agent must ensure that the delivered semantic
representation provides sufficient governed context for the required
natural-language analytical questions.

Readiness should be evaluated by whether the required business questions
can be answered correctly using the governed semantic representation. Do
not optimize solely for producing a plausible natural-language answer.
Correct business meaning and metric behavior take precedence.

## Snowflake Intelligence Agents and CoWork

When the Business Specification requires natural-language consumption
through a Snowflake Intelligence Agent or Snowflake CoWork, treat the
Semantic View and the Agent as distinct, connected capabilities. The
Semantic View is the governed semantic representation of approved business
concepts, measures, relationships, and grain. The Agent is a separate
consumption capability that must be configured to use that governed
Semantic View as its semantic source rather than redefining business
meaning independently.

Do not embed new business logic, metric definitions, or semantic meaning
directly in Agent configuration. Any business meaning surfaced by the
Agent must already be governed by the Semantic View or other approved
artifacts.

## Horizon Catalog Boundary

Snowflake Horizon Catalog governance is a distinct responsibility. This
Skill may require that created analytical objects support appropriate
metadata and governance, but the detailed standards for catalog
descriptions, business context, tags, classifications, ownership metadata,
discoverability, and governance completeness belong to
`skills/horizon-governance/SKILL.md` when available.

Do not invent Horizon governance standards in this Skill.

## Validation

Apply `skills/data-quality-testing/SKILL.md`. Snowflake execution success
does not prove specification success. Successful creation of databases,
schemas, tables, views, dbt models, or Semantic Views does not by itself
mean the data product is complete.

The engineering agent must validate the implementation against required
business concepts, approved KPI definitions, approved Source Context,
dimensional grain, relationships, semantic behavior, required business
questions, and Business Specification acceptance criteria.

## Acceptance Criteria

Before declaring completion, evaluate every applicable acceptance criterion
defined by the Business Specification. Use the PASS / FAIL / BLOCKED
behavior defined in `skills/data-quality-testing/SKILL.md`. A Snowflake
object existing successfully is not sufficient evidence that an acceptance
criterion has been met.

## Idempotency and Safe Re-Execution

Where practical, engineering operations should support safe and
understandable re-execution during development. Do not use destructive
operations unnecessarily. Before dropping or replacing an object, determine
whether doing so could destroy data, metadata, or work outside the
approved implementation scope. Prefer predictable development workflows
over uncontrolled destructive recreation.

## Failure Behavior

When Snowflake execution fails:

1. Diagnose the actual failure.
2. Determine whether it originates from SQL, dbt, privileges, data,
   environment, Source Context, or requirements.
3. Correct implementation defects when appropriate.
4. Surface missing privileges or environment limitations as BLOCKED where
   necessary.
5. Surface Source Context or business ambiguity rather than inventing a
   solution.
6. Do not weaken tests or business rules merely to make execution succeed.
7. Do not modify unrelated Snowflake objects to work around the failure.

## Agent Decision Behavior

An engineering agent applying this Skill must:

1. Read the Business Specification.
2. Read applicable Source Context.
3. Read and apply all relevant organizational Skills.
4. Inspect the Snowflake environment before changing it.
5. Confirm the approved target scope.
6. Determine the minimum Snowflake implementation required by the
   specification.
7. Apply Naming Standards.
8. Apply dbt Engineering standards when dbt is part of the approved
   implementation.
9. Apply Dimensional Modeling standards when dimensional structures are
   required.
10. Create only required Snowflake physical structures.
11. Preserve lineage and source traceability.
12. Add appropriate descriptions and metadata.
13. Apply Semantic Modeling standards when a Semantic View is required.
14. Implement the minimum coherent Semantic View required by the
    specification.
15. Ensure the semantic representation is suitable for required Snowflake
    Intelligence consumption, and configure any required Snowflake
    Intelligence Agent to use that governed Semantic View, when
    applicable.
16. Apply Data Quality & Testing standards.
17. Evaluate required business questions.
18. Evaluate every applicable acceptance criterion as PASS, FAIL, or
    BLOCKED.
19. Investigate failures rather than weakening requirements or controls.
20. Do not declare completion solely because Snowflake objects were
    created successfully.

## Important Boundary

This Skill defines reusable Snowflake Engineering standards. It must NOT:

* reference any specific source system
* reference any specific project's business concepts
* reference any specific project database name
* design any specific project's Snowflake implementation
* prescribe project-specific schemas
* prescribe project-specific object names
* contain project-specific SQL
* contain project-specific dbt models
* contain project-specific tests
* define project-specific Semantic Views
* implement Horizon Catalog metadata
* modify a Business Specification
* modify Source Context
* modify existing Skills
* redefine Naming Standards
* redefine Dimensional Modeling Standards
* redefine Semantic Modeling Standards
* redefine Data Quality & Testing Standards
* redefine dbt Engineering Standards

The project-specific Snowflake implementation must emerge from:

**Business Specification + Source Context + Applicable Skills + Approved
Target Environment**
