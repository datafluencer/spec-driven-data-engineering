# Sales Analytics — Business Specification

## Purpose

This specification defines the business requirements for a Sales Analytics data
product supporting a wholesale/distribution company. It describes the business
outcomes the data product must deliver. It does not describe or assume any
physical source-system implementation.

Physical source interpretation belongs in Source Context artifacts. Engineering
implementation standards belong in reusable Skills. Implementation decisions
belong to the engineering process that consumes this Specification, the Source
Context, and the Skills.

## Business Objective

Sales leadership needs a governed analytical data product for understanding
sales performance across Accounts, Products, Vendors, Countries, and Sales
Regions. The resulting data product must support analytical consumption and
natural-language business questions.

## Business Terminology

### Account
An organization purchasing products from the company.

### Sale
A commercial transaction placed by an Account.

### Sale Line
An individual product included within a Sale.

### Fulfilled Sale Line
A Sale Line is considered fulfilled when the product associated with that
Sale Line has completed fulfillment, as recognized by the source system's
approved fulfillment status.

### Product
A product offered for sale by the company.

### Vendor
An organization that supplies Products.

### Country
The country associated with an Account or Vendor.

### Sales Region
A geographic sales region containing one or more Countries. For this Sales
Analytics data product, Sales Region represents the geographic sales region
associated with the Account that placed the Sale.

## Required KPIs

### Revenue
The value of products sold after applicable discounts. Returned Sale Lines
are excluded.

### Sales
The number of distinct completed Sales. A Sale is considered completed when
all of its Sale Lines have been fulfilled.

### Units Sold
The total quantity of Products sold, excluding returned Sale Lines.

### Average Sale Value
Revenue divided by the number of Sales.

## Required Business Questions

At minimum, the resulting data product must enable users to answer:

1. What is Revenue by Sales Region?
2. Who are the top Accounts by Revenue?
3. Which Products generate the most Revenue?
4. How is Average Sale Value trending over time?

These questions represent required analytical capabilities rather than
prescribed queries or implementation instructions.

## Required Data Product Capabilities

The completed solution must:

* Provide an analytics-ready dimensional representation of the required
  business concepts.
* Support the defined KPIs and business questions.
* Apply appropriate data-quality validation.
* Expose understandable business terminology and definitions to consumers.
* Populate appropriate business context and metadata in Snowflake Horizon
  Catalog.
* Provide a Snowflake Semantic View suitable for analytical consumption and
  Snowflake Intelligence.
* Provide a Snowflake Intelligence Agent that uses the governed Semantic
  View as its semantic representation and enables natural-language
  interaction with the Sales Analytics data product through Snowflake
  CoWork.
* Preserve business terminology rather than exposing source-system
  terminology as the primary consumer-facing language.

## Acceptance Criteria

The implementation is considered successful when:

1. The required business concepts are represented in an analytics-ready
   data product.
2. Revenue, Sales, Units Sold, and Average Sale Value are implemented
   according to their business definitions.
3. The four required business questions can be answered from the resulting
   data product.
4. Appropriate automated data-quality tests execute successfully.
5. Business-facing objects and metadata use the terminology defined in this
   specification.
6. Required business descriptions and context are represented in Snowflake
   Horizon Catalog.
7. A Snowflake Semantic View exposes the required business concepts,
   relationships, dimensions, and measures.
8. A Snowflake Intelligence Agent exists, is configured to use the
   Semantic View from Criterion 7 as its governed semantic
   representation, and is usable through Snowflake CoWork.
9. The four required business questions can be answered through
   natural-language interaction with that Agent in Snowflake CoWork.
10. The completed implementation can be evaluated against these acceptance
    criteria without relying solely on subjective human inspection.

## Important Design Principle

This document defines **what the business requires and what constitutes
success**. It must not prescribe how the solution is physically implemented.

Physical source interpretation belongs in Source Context artifacts.
Engineering implementation standards belong in reusable Skills.
Implementation decisions belong to the engineering process that consumes the
Specification, Source Context, and Skills.
