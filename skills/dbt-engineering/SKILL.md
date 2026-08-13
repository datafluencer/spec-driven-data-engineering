# dbt Engineering

## Purpose

This Skill defines reusable organizational standards for implementing
governed data transformations and analytical data products using dbt.

This is an **implementation-technology-specific Skill**. It defines how an
engineering agent should use dbt to implement requirements derived from:

**Business Specification + Source Context + Applicable Skills**

It does not contain project-specific implementation instructions for any
specific demonstration, and it does not design any specific project's dbt
models.

## Required Dependencies

This Skill requires application of the existing Skills where applicable:

* `skills/naming-standards/SKILL.md`
* `skills/dimensional-modeling/SKILL.md`
* `skills/data-quality-testing/SKILL.md`
* `skills/semantic-modeling/SKILL.md`, where dbt artifacts contribute to
  semantic consumption

Standards already owned by those Skills are not duplicated here. This Skill
defines how dbt realizes those standards.

## Core Principle

dbt is an implementation mechanism, not the source of business
requirements. An engineering agent must not derive business meaning solely
from source tables, existing SQL, model names, dbt project structure, or
database metadata. Business meaning originates from approved specifications
and context.

The expected reasoning chain is:

**Business Specification → Source Context → Applicable Skills → dbt
Implementation → Validation**

## Specification-Driven Implementation

Before creating dbt artifacts, the engineering agent must:

1. Read the Business Specification.
2. Read all applicable Source Context.
3. Read all applicable organizational Skills.
4. Identify required business outcomes and acceptance criteria.
5. Determine the minimum dbt implementation necessary to satisfy those
   requirements.
6. Identify unresolved ambiguity before implementation begins.

Do not create models simply because corresponding source tables exist. Do
not create transformations for data that is not required by the
specification.

## dbt Project Structure

Organize dbt projects so the transformation flow is understandable and
maintainable. Where appropriate, separate transformation responsibilities
into logical layers such as source declarations, staging, intermediate
transformations when genuinely necessary, and business/analytical models.

Do not create layers merely to satisfy a template. Every layer must have a
clear purpose. Prefer the simplest project structure that preserves
readability, lineage, testability, reuse, and maintainability.

## Source Declarations

Physical source objects should be declared through dbt source definitions
where supported by the dbt environment. Source declarations should:

* identify the physical source clearly
* preserve physical source naming for lineage and traceability
* include useful descriptions where appropriate
* provide source-level tests where meaningful

Source declarations are technical representations of Source Context. They
must not redefine business meaning.

## Source Context and Staging

Staging models should provide a controlled boundary between physical
source structures and downstream transformation logic. Where appropriate,
staging models may select required source attributes, perform safe
datatype normalization, apply technically necessary normalization, expose
source lineage clearly, and prepare source data for downstream modeling.

Staging must not become an uncontrolled location for inventing business
rules. Business transformations must remain traceable to approved
requirements and Source Context. Do not mechanically create one staging
model for every source object if the source object is not required by the
specification.

## Model References and Lineage

Use dbt dependency mechanisms such as `source()` and `ref()` rather than
hard-coded object references where applicable. Dependencies should make
transformation lineage explicit. Avoid bypassing the dbt dependency graph
with unnecessary hard-coded database object references between
dbt-managed models. Physical source references may remain
platform-specific where required for source declarations.

## SQL Engineering

dbt model SQL should be readable, maintainable, deterministic where
practical, explicit about business transformations, and traceable to
approved requirements. Prefer understandable transformations over
unnecessarily clever SQL. Avoid duplicated transformation logic when a
reusable modeled dependency is appropriate. Do not bury significant
business rules inside opaque SQL expressions without documentation or
traceability.

## Model Grain

Every important analytical dbt model must have a clearly understood grain.
For dimensional implementations, apply
`skills/dimensional-modeling/SKILL.md`. The engineering agent must ensure
that dbt transformations preserve the intended grain and do not
accidentally introduce duplication through joins. Where practical, grain
should be documented and validated.

## Naming

Apply `skills/naming-standards/SKILL.md`. Do not create an independent dbt
naming philosophy that conflicts with organizational naming standards.
dbt-specific technical naming patterns may be used where they improve
project organization, provided they do not change approved business
terminology.

## Materializations

Choose dbt materializations deliberately based on the role of the model and
the needs of the implementation. Possible materializations may include
view, table, incremental, and ephemeral. Do not default every model to the
same materialization without considering purpose. Do not introduce
incremental processing merely because dbt supports it — incremental models
should be used only when requirements, data volume, performance, or
operational needs justify the added complexity. For small or demonstration
workloads, prefer simplicity unless requirements dictate otherwise.

## Intermediate Models

Create intermediate models only when they provide meaningful value, such as
isolating reusable transformation logic, resolving complex transformations,
simplifying downstream models, or making business logic easier to
understand or test. Do not create intermediate models simply to increase
architectural layering.

## Reuse and DRY Principles

Avoid unnecessary duplication of transformation logic. Where the same
governed logic is required by multiple downstream models, centralize it
appropriately. However, do not create abstractions or macros merely for
theoretical reuse. Prefer understandable implementation over premature
abstraction.

## Macros

Use dbt macros when they provide clear value for reusable technical logic.
Do not hide important business definitions inside generic macros where
doing so makes lineage or interpretation difficult. Business logic must
remain understandable and traceable to approved requirements.

## Documentation

dbt artifacts should include meaningful documentation where supported.
Document important models, business-facing attributes, keys, measures,
transformation assumptions, grain, and relationships. Descriptions should
use approved business terminology where artifacts are business-facing. Do
not simply repeat model or column names as descriptions.

Documentation should complement, not replace, the Business Specification,
Source Context, and organizational Skills.

## Testing

Apply `skills/data-quality-testing/SKILL.md`. dbt testing should be used to
implement applicable validation requirements. Where appropriate, use dbt
capabilities to validate not-null expectations, uniqueness, relationships,
accepted values, grain, business rules, measure calculations, and
reconciliation.

Use the most appropriate dbt testing mechanism for the requirement — this
may include framework-provided generic tests or custom tests where
necessary. Do not create tests solely because they are easy to generate.
Every meaningful test should correspond to a quality expectation, modeling
standard, business rule, or acceptance requirement.

## Business Rule Tests

When an approved business rule requires validation beyond generic
structural tests, create an appropriate explicit test. Do not force complex
business validation into a generic test when a clearer purpose-built test
would communicate intent better. Business-rule tests must remain traceable
to the approved rule being validated.

## Acceptance Criteria

dbt execution success does NOT mean the data product is complete. A
successful `dbt parse`, `dbt compile`, `dbt run`, `dbt build`, or `dbt test`
proves only the applicable technical portion of implementation and
validation. The engineering agent must still apply
`skills/data-quality-testing/SKILL.md` and evaluate the Business
Specification's acceptance criteria. Do not report the project complete
merely because `dbt build` succeeds.

## Documentation and Lineage Artifacts

Where supported by the dbt environment, maintain sufficient dbt metadata to
support understandable lineage and documentation. The dbt dependency graph
should accurately represent model dependencies. Do not create hidden
dependencies outside the graph when they can reasonably be represented
through dbt constructs.

## Semantic Consumption

When dbt models provide the physical analytical foundation for a semantic
layer, apply `skills/semantic-modeling/SKILL.md`. dbt model design should
make governed semantic consumption straightforward. Do not compensate for
an unnecessarily confusing physical model by pushing excessive complexity
into the semantic layer.

## Environment Separation

Do not hard-code environment-specific database or schema names throughout
transformation logic when the dbt environment provides a reasonable
configuration mechanism. Implementation should support appropriate
separation between development and production environments. Specific
Snowflake environment conventions belong to a Snowflake Engineering Skill.

## Configuration

Keep configuration understandable and intentional. Prefer project-level
configuration where it consistently applies and model-level configuration
where behavior genuinely differs. Avoid unnecessary configuration
overrides. Do not introduce configuration merely because an option exists.

## Packages and Dependencies

Introduce external dbt packages only when they provide clear implementation
value. Avoid adding packages for functionality that is not required by the
specification. Dependencies should be intentional, minimal, understandable,
and maintainable. Do not introduce a package solely to avoid writing a
small amount of straightforward project logic.

## Generated Code Behavior

When an AI engineering agent generates dbt artifacts, it must inspect
existing project conventions first, follow applicable organizational
Skills, generate only required artifacts, avoid speculative models, tests,
macros, and packages, preserve readable lineage, and validate generated
artifacts. Generated code is subject to the same engineering standards as
human-authored code.

## Failure Behavior

When dbt compilation, execution, or testing fails:

1. Diagnose the actual cause.
2. Determine whether the failure originates from implementation, data,
   context, environment, or requirements.
3. Correct implementation defects when appropriate.
4. Surface Source Context or business ambiguity when discovered.
5. Do not remove or weaken valid tests merely to achieve a successful run.
6. Do not silently filter problematic records unless an approved business
   rule requires that behavior.

## Agent Decision Behavior

An engineering agent applying this Skill must:

1. Read the Business Specification.
2. Read applicable Source Context.
3. Read and apply all relevant organizational Skills.
4. Inspect the existing dbt project and environment before changing it.
5. Determine the minimum dbt model set required by the specification.
6. Establish clear source declarations and lineage.
7. Apply staging only where useful.
8. Implement business transformations traceable to approved requirements.
9. Apply dimensional modeling standards where required.
10. Apply organizational naming standards.
11. Choose materializations deliberately.
12. Add meaningful documentation.
13. Implement applicable Data Quality & Testing standards using appropriate
    dbt mechanisms.
14. Validate model grain and relationships.
15. Execute the applicable dbt validation/build workflow.
16. Investigate failures rather than weakening requirements or tests.
17. Evaluate the delivered data product against the Business
    Specification's acceptance criteria.
18. Do not declare completion solely because dbt execution succeeds.

## Important Boundary

This Skill defines reusable dbt Engineering standards. It must NOT:

* reference any specific source system
* reference any specific project's business concepts
* design any specific project's dbt models
* prescribe project-specific model names
* contain project-specific SQL
* contain project-specific tests
* define project-specific materializations
* create Snowflake objects
* define Snowflake-specific database/schema conventions
* create a Snowflake Semantic View
* implement Horizon Catalog metadata
* modify a Business Specification
* modify Source Context
* modify existing Skills
* redefine Naming Standards
* redefine Dimensional Modeling Standards
* redefine Semantic Modeling Standards
* redefine Data Quality & Testing Standards

The project-specific dbt implementation must emerge from:

**Business Specification + Source Context + Applicable Skills + Target
Platform Context**
