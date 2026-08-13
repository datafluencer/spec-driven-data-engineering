# Data Quality & Testing

## Purpose

This Skill defines reusable organizational standards for validating data
engineering solutions and proving that implementations satisfy approved
specifications.

This Skill is:

* technology-independent
* platform-independent
* reusable across business domains
* reusable across source systems and analytical architectures
* independent of any specific demonstration or data product

This Skill defines **what must be validated and why**. It does not
prescribe dbt-specific tests, Snowflake-specific implementation, or
project-specific validation logic. Those belong to applicable engineering
and platform Skills.

## Core Principle

Testing must do more than prove that code executes successfully. A
successful data engineering implementation must demonstrate that:

1. The data structures are technically valid.
2. The data conforms to approved quality expectations.
3. Business rules and metric definitions are implemented correctly.
4. Relationships and grain behave as designed.
5. The analytical solution supports the outcomes defined by the Business
   Specification.
6. The implementation satisfies the Business Specification's acceptance
   criteria.

The engineering agent should reason through:

**Business Specification → Source Context → Applicable Skills →
Implementation → Validation Evidence**

Passing technical tests alone does not prove that the specification has
been satisfied.

## Required Dependencies

Where applicable, this Skill must work with and respect:

* `skills/naming-standards/SKILL.md`
* `skills/dimensional-modeling/SKILL.md`
* `skills/semantic-modeling/SKILL.md`

Standards already owned by those Skills are not duplicated here. This Skill
validates compliance with those standards where appropriate.

## Specification-Driven Validation

Testing and validation must originate from approved requirements. Before
defining tests, the engineering agent must identify:

* required business concepts
* approved business rules
* KPI and metric definitions
* required analytical questions
* Source Context interpretations
* dimensional grain and relationships
* semantic requirements
* explicit acceptance criteria

Tests should provide evidence that these requirements were implemented
correctly. Do not create arbitrary tests solely because a testing framework
makes them easy to create.

## Structural Data Quality

Where applicable, validate structural expectations including:

* required identifiers are not null
* expected identifiers are unique
* records conform to the declared grain
* required relationships resolve correctly
* required attributes are populated
* accepted-value constraints are respected when approved rules define them
* duplicate records do not violate the intended grain

Structural tests must reflect the semantics of the modeled data rather than
being applied mechanically to every column.

## Grain Validation

The declared grain of analytical structures must be testable. Validation
should determine whether the implementation contains exactly the level of
detail it claims to represent. Where appropriate, test:

* uniqueness of the business key or key combination representing the grain
* absence of unintended duplication
* compatibility of measures with the grain
* relationship cardinality

A model that violates its declared grain must not be considered valid even
if its SQL executes successfully.

## Relationship Integrity

Validate important relationships between analytical objects. Where
applicable, test:

* fact-to-dimension relationships
* required parent-child relationships
* unresolved references
* unexpected orphan records
* unintended many-to-many relationships
* conditions that could cause measure fan-out

Do not silently discard records simply to make referential-integrity tests
pass. Unresolved relationships should be visible and measurable.

## Business Rule Validation

Approved business rules must be translated into explicit validation where
practical. Tests should verify that implemented transformations behave
consistently with:

* Business Specification definitions
* approved Source Context interpretations
* approved business decisions

Do not invent business rules in order to create tests. If a required rule
is ambiguous, identify the ambiguity instead of encoding an assumption into
a test.

## Measures and KPI Validation

Measures and KPIs require validation beyond checking that their columns
contain values. Where appropriate, validate:

* calculation logic
* filters and exclusions
* aggregation behavior
* numerator and denominator behavior
* grain compatibility
* treatment of nulls
* treatment of approved business statuses or conditions
* reconciliation to underlying component measures

Derived metrics should be validated against their approved component
definitions. A metric must not be considered correct merely because it
produces a plausible number.

## Source-to-Target Reconciliation

Where practical and appropriate, validate important transformations against
Source Context. Reconciliation may include:

* record counts
* distinct business identifiers
* aggregate measures
* expected inclusions and exclusions
* source-to-target key coverage

Reconciliation must respect intentional transformation and filtering rules.
Do not assume source and target record counts must always be identical.

## Boundary and Edge-Case Validation

Where approved requirements imply meaningful boundaries or special
conditions, validate them. Examples may include:

* null or missing identifiers
* zero values
* negative values where relevant
* unknown dimensional references
* boundary dates
* status transitions
* excluded records
* duplicate business identifiers

Do not invent edge-case behavior that is not supported by approved
requirements or Source Context.

## Dimensional Model Validation

When validating a dimensional model, apply
`skills/dimensional-modeling/SKILL.md`. Validation should include, where
applicable:

* declared fact grain
* dimension identifier uniqueness
* fact-to-dimension integrity
* unknown/unresolved-member handling
* measure compatibility with grain
* required dimensional coverage
* required time-analysis capability

Do not redefine dimensional modeling standards in this Skill.

## Semantic Model Validation

When validating a semantic model, apply
`skills/semantic-modeling/SKILL.md`. Validation should include, where
applicable:

* required business concepts are exposed
* approved measures and metrics are represented
* aggregation behavior is correct
* semantic relationships produce valid analytical results
* terminology matches approved business language
* required business questions can be answered
* duplicate or conflicting metric definitions are absent
* unnecessary implementation details are not exposed

## Analytical Question Validation

Required business questions from the Business Specification are executable
acceptance tests of the data product's analytical capability. For each
required business question, validation should establish that:

1. The necessary business concepts are represented.
2. The required measures or metrics exist.
3. Required relationships are available.
4. The question can be answered from the delivered analytical/semantic
   solution.
5. The resulting answer is consistent with approved business definitions.

Do not substitute "the required objects exist" for proving that the
required analytical question can actually be answered.

## Acceptance Criteria Validation

Before an implementation is considered complete, every applicable
acceptance criterion in the Business Specification must be evaluated. For
each criterion, determine one of:

* PASS
* FAIL
* BLOCKED

A BLOCKED criterion means required information, capability, or context is
unavailable. Do not report a blocked criterion as passed. Do not report
overall success while required acceptance criteria remain failed or
blocked.

## Validation Evidence

Testing should produce understandable evidence of completion. Where
appropriate, validation evidence should identify:

* what was tested
* why it was tested
* expected behavior
* actual result
* pass/fail/blocked status
* relevant implementation artifact
* applicable specification requirement or acceptance criterion

The specific evidence format may depend on the implementation technology.

## Test Failure Behavior

A failing test is information, not an instruction to weaken the test. When
a test fails, the engineering agent must determine whether the cause is:

* an implementation defect
* a source-data issue
* an incorrect assumption
* incomplete Source Context
* an ambiguous business requirement
* an invalid test

Do not automatically change thresholds, remove tests, filter failing
records, or alter business logic merely to make tests pass.

## Ambiguity Behavior

If validation reveals ambiguity in business definitions, Source Context,
expected relationships, metric behavior, or acceptance criteria, the
engineering agent must surface the ambiguity. Do not invent a business
decision during implementation or testing. Where necessary, validation
should remain BLOCKED until the ambiguity is resolved.

## Testing Proportionality

Testing should be proportional to the requirements and risks of the data
product. Do not generate hundreds of low-value tests simply to maximize
test count. Prefer a smaller set of meaningful tests that demonstrate
structural integrity, business correctness, analytical correctness, and
satisfaction of acceptance criteria.

## Technology Independence

This Skill must not prescribe a testing framework. For example, it must
not require dbt generic tests, dbt singular tests, Snowflake SQL tests,
Python tests, or a specific CI/CD platform. Platform/tool-specific Skills
may determine the best implementation mechanism while applying the
standards defined here.

## Agent Decision Behavior

An engineering agent applying this Skill must:

1. Read the Business Specification and its acceptance criteria.
2. Read applicable Source Context.
3. Read applicable modeling and engineering Skills.
4. Identify the implementation's declared grains, relationships, measures,
   metrics, and business rules.
5. Determine the minimum meaningful set of tests required to validate the
   implementation.
6. Include structural, relationship, business-rule, measure/KPI, and
   analytical validation where applicable.
7. Map important tests to the requirement or acceptance criterion they
   help validate.
8. Execute the tests using the applicable implementation technology.
9. Investigate failures rather than weakening tests to obtain a passing
   result.
10. Surface ambiguity rather than inventing business behavior.
11. Validate required analytical questions against the delivered
    analytical/semantic solution.
12. Evaluate every applicable acceptance criterion as PASS, FAIL, or
    BLOCKED.
13. Produce understandable validation evidence.
14. Do not consider implementation complete while required criteria remain
    FAIL or BLOCKED.

## Important Boundary

This Skill defines reusable Data Quality & Testing standards. It must NOT:

* reference any specific source system
* reference project-specific business concepts
* contain project-specific KPI calculations
* create project-specific tests
* prescribe dbt test syntax
* prescribe Snowflake-specific testing
* create SQL
* create dbt artifacts
* create Snowflake objects
* design a dimensional model
* design a semantic model
* implement Horizon Catalog metadata
* modify a Business Specification
* modify Source Context
* modify existing Skills

Project-specific validation must emerge from:

**Business Specification + Source Context + Implementation + Applicable
Skills**
