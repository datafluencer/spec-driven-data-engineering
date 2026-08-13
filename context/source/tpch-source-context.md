# TPCH_SF1 Source Context

## Purpose

This document describes how the physical source system —
`SNOWFLAKE_SAMPLE_DATA.TPCH_SF1` — represents the business concepts defined
in the approved Business Specification (`specification/sales-analytics-spec.md`).

It answers: **what does the physical source data mean in the language of
this business?**

This is a Source Context artifact. It does not define a target dimensional
model, does not prescribe implementation, and does not modify the approved
Business Specification.

## Business-to-Source Concept Mapping

| Business Concept | TPCH Physical Object |
| ----------------- | --------------------- |
| Account       | `CUSTOMER` |
| Sale          | `ORDERS`   |
| Sale Line     | `LINEITEM` |
| Product       | `PART`     |
| Vendor        | `SUPPLIER` |
| Country       | `NATION`   |
| Sales Region  | `REGION`   |

These mappings are intentional and approved. TPCH physical object and
column names are source-system terminology only. They are **not** the
business-facing terminology. For example, `CUSTOMER` is the physical source
object; the business concept it represents is **Account**. An engineering
agent consuming this Source Context must continue to use the business
terminology defined in the Business Specification when building
consumer-facing objects, and must not surface TPCH physical names (e.g.
`CUSTOMER`, `C_CUSTKEY`, `ORDERS`) as business-facing labels.

`PARTSUPP` is **not** included as a required physical object. See
[Why `PARTSUPP` Is Not Used for the Product → Vendor Relationship](#why-partsupp-is-not-used-for-the-product--vendor-relationship)
below for the reasoning.

---

## Source Objects

### `SNOWFLAKE_SAMPLE_DATA.TPCH_SF1.CUSTOMER`

* **Business concept:** Account
* **Business meaning:** An organization purchasing products from the company.
* **Source grain:** One row per Account.
* **Physical identifier/key:** `C_CUSTKEY`

| Physical Attribute | Business Meaning |
| ------------------- | ----------------- |
| `C_CUSTKEY` | Unique physical identifier for an Account. |
| `C_NAME` | The Account's name. |
| `C_NATIONKEY` | Foreign key identifying the Account's Country (`NATION`). |

Other `CUSTOMER` attributes (`C_ADDRESS`, `C_PHONE`, `C_ACCTBAL`,
`C_MKTSEGMENT`, `C_COMMENT`) are not required to support the approved KPIs
or business questions and are excluded from this context.

**Relationships:** `CUSTOMER.C_NATIONKEY` → `NATION.N_NATIONKEY` (Account →
Country). `CUSTOMER.C_CUSTKEY` is referenced by `ORDERS.O_CUSTKEY` (Account
→ Sale). This Account → Country path, continued through
`NATION.N_REGIONKEY` → `REGION`, is the approved geographic path for the
business concept **Sales Region**. See
[Sales Region Mapping](#sales-region-mapping) below.

---

### `SNOWFLAKE_SAMPLE_DATA.TPCH_SF1.ORDERS`

* **Business concept:** Sale
* **Business meaning:** A commercial transaction placed by an Account.
* **Source grain:** One row per Sale (one row per placed order).
* **Physical identifier/key:** `O_ORDERKEY`

| Physical Attribute | Business Meaning |
| ------------------- | ----------------- |
| `O_ORDERKEY` | Unique physical identifier for a Sale. |
| `O_CUSTKEY` | Foreign key identifying the Account that placed the Sale. |
| `O_ORDERDATE` | The date the Sale was placed. |
| `O_ORDERSTATUS` | A physical status code describing the order's fulfillment state, derived from the fulfillment status of the order's Sale Lines. Per established TPC-H semantics: `O` (open) when none of the order's Sale Lines have been fulfilled, `F` (finished) when every Sale Line belonging to the order has been fulfilled, and `P` (partially filled/pending) when some but not all Sale Lines have been fulfilled. See [Order and Sale Line Status Semantics](#order-and-sale-line-status-semantics) below. |
| `O_TOTALPRICE` | A physical total order amount. Per TPC-H conventions, `O_TOTALPRICE` is derived to include both tax (`L_TAX`) and discount effects across the order's Sale Lines, which differs from the gross-value and discount attributes available individually at the Sale Line grain (`L_EXTENDEDPRICE`, `L_DISCOUNT`). |

Other `ORDERS` attributes (`O_ORDERPRIORITY`, `O_CLERK`, `O_SHIPPRIORITY`,
`O_COMMENT`) are not required to support the approved KPIs or business
questions and are excluded from this context.

**Relationships:** `ORDERS.O_CUSTKEY` → `CUSTOMER.C_CUSTKEY` (Sale →
Account). `ORDERS.O_ORDERKEY` is referenced by `LINEITEM.L_ORDERKEY` (Sale
→ Sale Line).

---

### `SNOWFLAKE_SAMPLE_DATA.TPCH_SF1.LINEITEM`

* **Business concept:** Sale Line
* **Business meaning:** An individual product included within a Sale.
* **Source grain:** One row per product line within a Sale (a Sale/Order
  typically contains multiple Sale Lines).
* **Physical identifier/key:** Composite key `L_ORDERKEY` + `L_LINENUMBER`.

| Physical Attribute | Business Meaning |
| ------------------- | ----------------- |
| `L_ORDERKEY` | Foreign key identifying the parent Sale. |
| `L_LINENUMBER` | The line's sequence number within the Sale. |
| `L_PARTKEY` | Foreign key identifying the Product sold on this Sale Line. |
| `L_SUPPKEY` | Foreign key identifying the Vendor that supplied the Product on this specific Sale Line. |
| `L_QUANTITY` | The quantity of the Product sold on this Sale Line. |
| `L_EXTENDEDPRICE` | The gross extended price of the Sale Line before discount. |
| `L_DISCOUNT` | The discount fraction applied to this Sale Line. |
| `L_TAX` | The tax fraction applied to this Sale Line. |
| `L_RETURNFLAG` | Indicates whether the Sale Line was returned. Per established TPC-H semantics, a value of `R` indicates the Sale Line was returned. See [Return Status Semantics](#return-status-semantics) below. |
| `L_LINESTATUS` | A physical line-level status (`O` or `F`) that contributes to how `O_ORDERSTATUS` is derived at the Sale level. See [Order and Sale Line Status Semantics](#order-and-sale-line-status-semantics) below. |
| `L_SHIPDATE`, `L_COMMITDATE`, `L_RECEIPTDATE` | Fulfillment-related dates at the Sale Line grain (shipped, committed, and received dates respectively). See [Dates and Time Context](#dates-and-time-context). |

Other `LINEITEM` attributes (`L_SHIPINSTRUCT`, `L_SHIPMODE`, `L_COMMENT`)
are not required to support the approved KPIs or business questions and are
excluded from this context.

**Relationships:** `LINEITEM.L_ORDERKEY` → `ORDERS.O_ORDERKEY` (Sale Line →
Sale). `LINEITEM.L_PARTKEY` → `PART.P_PARTKEY` (Sale Line → Product).
`LINEITEM.L_SUPPKEY` → `SUPPLIER.S_SUPPKEY` (Sale Line → Vendor).

---

### `SNOWFLAKE_SAMPLE_DATA.TPCH_SF1.PART`

* **Business concept:** Product
* **Business meaning:** A product offered for sale by the company.
* **Source grain:** One row per Product.
* **Physical identifier/key:** `P_PARTKEY`

| Physical Attribute | Business Meaning |
| ------------------- | ----------------- |
| `P_PARTKEY` | Unique physical identifier for a Product. |
| `P_NAME` | The Product's name. |

`P_RETAILPRICE` and other `PART` attributes (`P_MFGR`, `P_BRAND`, `P_TYPE`,
`P_SIZE`, `P_CONTAINER`, `P_COMMENT`) are not required to support the
approved KPIs or business questions and are excluded from this context.
`P_RETAILPRICE` is a catalog list price, distinct from the transaction-level
Sale Line attributes (`L_EXTENDEDPRICE`, `L_DISCOUNT`) recorded on
`LINEITEM`.

**Relationships:** `PART.P_PARTKEY` is referenced by `LINEITEM.L_PARTKEY`
(Product → Sale Line).

---

### `SNOWFLAKE_SAMPLE_DATA.TPCH_SF1.SUPPLIER`

* **Business concept:** Vendor
* **Business meaning:** An organization that supplies Products.
* **Source grain:** One row per Vendor.
* **Physical identifier/key:** `S_SUPPKEY`

| Physical Attribute | Business Meaning |
| ------------------- | ----------------- |
| `S_SUPPKEY` | Unique physical identifier for a Vendor. |
| `S_NAME` | The Vendor's name. |
| `S_NATIONKEY` | Foreign key identifying the Vendor's Country (`NATION`). |

Other `SUPPLIER` attributes (`S_ADDRESS`, `S_PHONE`, `S_ACCTBAL`,
`S_COMMENT`) are not required to support the approved KPIs or business
questions and are excluded from this context.

**Relationships:** `SUPPLIER.S_NATIONKEY` → `NATION.N_NATIONKEY` (Vendor →
Country). `SUPPLIER.S_SUPPKEY` is referenced by `LINEITEM.L_SUPPKEY` (Vendor
→ Sale Line). This Vendor → Country path is retained here as useful source
lineage, but it is **not** the geographic path used for the business
concept **Sales Region**. See
[Sales Region Mapping](#sales-region-mapping) below.

---

### `SNOWFLAKE_SAMPLE_DATA.TPCH_SF1.NATION`

* **Business concept:** Country
* **Business meaning:** The country associated with an Account or Vendor.
* **Source grain:** One row per Country.
* **Physical identifier/key:** `N_NATIONKEY`

| Physical Attribute | Business Meaning |
| ------------------- | ----------------- |
| `N_NATIONKEY` | Unique physical identifier for a Country. |
| `N_NAME` | The Country's name. |
| `N_REGIONKEY` | Foreign key identifying the Sales Region (`REGION`) that contains this Country. |

`N_COMMENT` is not required to support the approved KPIs or business
questions and is excluded from this context.

**Relationships:** `NATION.N_NATIONKEY` is referenced by
`CUSTOMER.C_NATIONKEY` and `SUPPLIER.S_NATIONKEY` (Country → Account /
Country → Vendor). `NATION.N_REGIONKEY` → `REGION.R_REGIONKEY` (Country →
Sales Region). `NATION` is reachable from both Account and Vendor, but only
the Account path (via `CUSTOMER.C_NATIONKEY`) is used for the approved
Sales Region business concept. See
[Sales Region Mapping](#sales-region-mapping) below.

---

### `SNOWFLAKE_SAMPLE_DATA.TPCH_SF1.REGION`

* **Business concept:** Sales Region
* **Business meaning:** A geographic sales region containing one or more
  Countries.
* **Source grain:** One row per Sales Region.
* **Physical identifier/key:** `R_REGIONKEY`

| Physical Attribute | Business Meaning |
| ------------------- | ----------------- |
| `R_REGIONKEY` | Unique physical identifier for a Sales Region. |
| `R_NAME` | The Sales Region's name. |

`R_COMMENT` is not required to support the approved KPIs or business
questions and is excluded from this context.

**Relationships:** `REGION.R_REGIONKEY` is referenced by
`NATION.N_REGIONKEY` (Sales Region → Country).

---

### Why `PARTSUPP` Is Not Used for the Product → Vendor Relationship

`SNOWFLAKE_SAMPLE_DATA.TPCH_SF1.PARTSUPP` represents a catalog-level,
many-to-many relationship between `PART` and `SUPPLIER` (which Vendors are
generally capable of supplying which Products, along with available
quantity and supply cost). This is not the same relationship required by
the approved Business Specification.

The approved business requirement is "Sale Line → Vendor" — i.e., which
Vendor actually supplied the Product recorded on a specific Sale Line.
`LINEITEM` already carries this directly via `L_SUPPKEY`, which identifies
the actual Vendor for that transaction line. This is a more precise and
grain-appropriate source of the Sale Line → Vendor relationship than the
general catalog relationship in `PARTSUPP`.

`PARTSUPP` is therefore excluded from the Required Physical Objects
documented here, since it is not required to accurately answer the
approved business questions or support the approved KPIs.

---

## Source Representation of Required Business Elements

The Business Specification (`specification/sales-analytics-spec.md`)
defines the authoritative business meaning of Revenue, Sales, Units Sold,
and Average Sale Value, along with the business questions those elements
must support. This section documents the physical TPC-H attributes
relevant to those business elements. It maps physical source facts to the
business elements that need them; it does not restate the business
definitions themselves and does not prescribe calculation or filtering
logic. Deriving an implementation from these facts — combined with the
Business Specification and applicable Skills — is the responsibility of
the engineering agent.

### Business Element to Physical Attribute Mapping

* Sale identifier → `ORDERS.O_ORDERKEY`
* Sale date → `ORDERS.O_ORDERDATE`
* Sale fulfillment status → `ORDERS.O_ORDERSTATUS`
* Sale Line quantity → `LINEITEM.L_QUANTITY`
* Sale Line gross value before discount → `LINEITEM.L_EXTENDEDPRICE`
* Sale Line discount fraction → `LINEITEM.L_DISCOUNT`
* Sale Line tax fraction → `LINEITEM.L_TAX`
* Sale Line return status → `LINEITEM.L_RETURNFLAG`
* Sale Line fulfillment status → `LINEITEM.L_LINESTATUS`
* Product identifier for a Sale Line → `LINEITEM.L_PARTKEY`
* Vendor identifier for a Sale Line → `LINEITEM.L_SUPPKEY`
* Order total amount (tax- and discount-inclusive) → `ORDERS.O_TOTALPRICE`

### Order and Sale Line Status Semantics

`ORDERS.O_ORDERSTATUS` and `LINEITEM.L_LINESTATUS` are related physical
status attributes. Per established TPC-H semantics:

* `LINEITEM.L_LINESTATUS` carries a value of `O` (open) or `F` (finished)
  at the individual Sale Line grain.
* `ORDERS.O_ORDERSTATUS` is derived from the `L_LINESTATUS` values of all
  Sale Lines belonging to that order: it is `F` when every Sale Line has a
  status of `F`, `O` when none do, and `P` (partial) when some but not all
  do.

These are source-system status semantics only. Which status value(s)
correspond to a given business condition is determined by combining this
physical semantics with the relevant business definition in the Business
Specification.

### Return Status Semantics

`LINEITEM.L_RETURNFLAG = 'R'` indicates, per established TPC-H semantics,
that a Sale Line was returned.

`L_RETURNFLAG` and `L_LINESTATUS` are independent physical attributes
recorded separately at the Sale Line grain — a Sale Line's return status
does not determine, and is not determined by, its fulfillment status.

## Dates and Time Context

`ORDERS.O_ORDERDATE` is the date the Sale was placed and is the only date
attribute at the Sale grain.

`LINEITEM` additionally carries three fulfillment-related dates at the Sale
Line grain: `L_SHIPDATE`, `L_COMMITDATE`, and `L_RECEIPTDATE`. These
represent when a Sale Line was shipped, committed to ship, and received,
respectively — distinct physical attributes from `O_ORDERDATE`. The
Business Specification does not establish separate business meaning for
shipment, commitment, or receipt; determining which date attribute(s) are
relevant to a given business date requirement is a matter of combining
these physical facts with the Business Specification.

## Source Relationships

**Account → Sale → Sale Line → Product**

```
CUSTOMER.C_CUSTKEY = ORDERS.O_CUSTKEY
ORDERS.O_ORDERKEY = LINEITEM.L_ORDERKEY
LINEITEM.L_PARTKEY = PART.P_PARTKEY
```

**Sale Line → Vendor**

```
LINEITEM.L_SUPPKEY = SUPPLIER.S_SUPPKEY
```

**Account/Vendor → Country**

```
CUSTOMER.C_NATIONKEY = NATION.N_NATIONKEY        (Account → Country)
SUPPLIER.S_NATIONKEY = NATION.N_NATIONKEY        (Vendor → Country)
```

#### Sales Region Mapping

The Business Specification defines the authoritative meaning of Sales
Region (see Business Terminology). The physical source path relevant to
that business concept is:

```
CUSTOMER.C_CUSTKEY   = ORDERS.O_CUSTKEY          (Account → Sale)
CUSTOMER.C_NATIONKEY = NATION.N_NATIONKEY         (Account → Country)
NATION.N_REGIONKEY   = REGION.R_REGIONKEY         (Country → Sales Region)
```

This Account-based path is the sole approved geographic basis for the Sales
Region business concept, and therefore for the required business question
"What is Revenue by Sales Region?"

`SUPPLIER.S_NATIONKEY → NATION.N_NATIONKEY` (Vendor → Country) remains
documented above as useful source lineage describing the Vendor's
geography, but it is **not** an alternative representation of the Sales
Region business concept and must not be used to answer the Revenue by
Sales Region business question.

## Open Context Questions

No unresolved Source Context questions are currently identified for the
source objects, physical attributes, and relationships required by the
approved Business Specification.
