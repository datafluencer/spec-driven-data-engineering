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
→ Sale).

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
| `O_ORDERSTATUS` | A physical status code describing the order's fulfillment state. Observed values are `O` (open), `F` (finished/fully filled), and `P` (partially filled/pending). Per established TPC-H semantics, `O_ORDERSTATUS` is derived from the fulfillment status of the order's Sale Lines: it is `F` only when every Sale Line belonging to the order has been fulfilled. This is the physical representation of the approved Completed Sale business rule. See [Sales — Completed Sale Mapping](#sales--completed-sale-mapping) below. |
| `O_TOTALPRICE` | A physical total order amount. **Interpretation note:** per TPC-H conventions, `O_TOTALPRICE` is derived to include tax (`L_TAX`) as well as discount, whereas the approved Revenue definition is explicitly "the value of products sold after applicable discounts" and does not reference tax. `O_TOTALPRICE` should therefore **not** be treated as equivalent to the business Revenue measure; Revenue must be derived from `LINEITEM` attributes (see [Revenue](#revenue)). |

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
| `L_QUANTITY` | The quantity of the Product sold on this Sale Line. Source attribute for the Units Sold KPI. |
| `L_EXTENDEDPRICE` | The gross price of the Sale Line before discount. Component of the Revenue calculation. |
| `L_DISCOUNT` | The discount fraction applied to this Sale Line. Component of the Revenue calculation. |
| `L_TAX` | The tax fraction applied to this Sale Line. **Not** part of the Revenue calculation, since the approved Revenue definition is discount-based only and does not reference tax. |
| `L_RETURNFLAG` | Indicates whether the Sale Line was returned. Per established TPC-H semantics, a value of `R` indicates the Sale Line was returned. This is the physical representation of the approved rule that returned Sale Lines are excluded from Revenue and Units Sold. See [Returned Sale Lines Mapping](#returned-sale-lines-mapping) below. |
| `L_LINESTATUS` | A physical line-level status (`O` or `F`) that contributes to how `O_ORDERSTATUS` is derived at the Sale level. See [Sales — Completed Sale Mapping](#sales--completed-sale-mapping). |
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
Note that `P_RETAILPRICE` is a catalog list price and is not used in the
Revenue calculation, which is derived from actual `LINEITEM` transaction
attributes.

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
→ Sale Line).

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
Sales Region).

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

## KPI Source Interpretation

### Revenue

**Business definition (approved):** "The value of products sold after
applicable discounts. Returned Sale Lines are excluded."

Relevant physical attributes: `LINEITEM.L_EXTENDEDPRICE` and
`LINEITEM.L_DISCOUNT`, both at the Sale Line grain. Together these
attributes represent the gross line value and the discount fraction applied
to that line, which in combination express "value... after applicable
discounts" at the Sale Line grain. `LINEITEM.L_TAX` is not part of this
business definition, since the definition does not reference tax.
`ORDERS.O_TOTALPRICE` should not be used, since it includes tax (see
`ORDERS` interpretation note above). Sale Lines where
`LINEITEM.L_RETURNFLAG = 'R'` represent returned Sale Lines and must be
excluded from the Revenue calculation. See
[Returned Sale Lines Mapping](#returned-sale-lines-mapping) below.

### Sales

**Business definition (approved):** "The number of distinct completed
Sales. A Sale is considered completed when all of its Sale Lines have been
fulfilled."

Relevant physical identifier: `ORDERS.O_ORDERKEY`, one per Sale.

#### Sales — Completed Sale Mapping

`ORDERS.O_ORDERSTATUS` carries a physical status of `O` (open), `F`
(finished), or `P` (partially filled/pending), derived from the line-level
`LINEITEM.L_LINESTATUS` values (`O` or `F`) of the Sale Lines belonging to
the order. Per established TPC-H semantics, an order's status is `F` only
when every Sale Line belonging to that order has been fulfilled (i.e. every
associated `LINEITEM.L_LINESTATUS = 'F'`); it is `O` when none have been
fulfilled, and `P` when some but not all have been fulfilled.

This maps directly to the approved Completed Sale definition: a Sale is
completed — and should be counted in the Sales KPI — when
`ORDERS.O_ORDERSTATUS = 'F'`. `O` and `P` orders do not satisfy the
approved definition, since at least one Sale Line has not been fulfilled.

Note that whether a Sale Line was returned (`LINEITEM.L_RETURNFLAG`) is a
separate physical attribute from whether it was fulfilled
(`LINEITEM.L_LINESTATUS`). The approved Completed Sale definition depends
only on fulfillment; it is not affected by returns. See
[Sales Count and Returns](#sales-count-and-returns) below.

### Units Sold

**Business definition (approved):** "The total quantity of Products sold,
excluding returned Sale Lines."

Relevant physical attribute: `LINEITEM.L_QUANTITY`, at the Sale Line grain.
Sale Lines where `LINEITEM.L_RETURNFLAG = 'R'` represent returned Sale
Lines and must be excluded from the Units Sold calculation. See
[Returned Sale Lines Mapping](#returned-sale-lines-mapping) below.

### Average Sale Value

**Business definition (approved, unchanged):** "Revenue divided by the
number of Sales."

This is a derived measure. Its numerator is the Revenue calculation
described above (aggregated from `LINEITEM.L_EXTENDEDPRICE` and
`LINEITEM.L_DISCOUNT`, excluding Sale Lines where
`LINEITEM.L_RETURNFLAG = 'R'`). Its denominator is the count of Sales
described above, restricted to Sales where `ORDERS.O_ORDERSTATUS = 'F'`
(the approved Completed Sale definition). The numerator's returned-line
exclusion and the denominator's completed-Sale restriction are independent
physical filters and must both be applied, each per its own approved
business rule.

#### Returned Sale Lines Mapping

Per established TPC-H semantics, `LINEITEM.L_RETURNFLAG = 'R'` identifies a
Sale Line as returned. This is the physical representation of the approved
business rule that returned Sale Lines are excluded from Revenue and Units
Sold.

#### Sales Count and Returns

A returned Sale Line (`LINEITEM.L_RETURNFLAG = 'R'`) does not, on its own,
cause the parent Sale to be excluded from the Sales KPI. The approved
business decision excludes returned Sale Lines only from Revenue and Units
Sold. Whether a Sale counts toward the Sales KPI is governed solely by the
Completed Sale mapping above (`ORDERS.O_ORDERSTATUS = 'F'`), which reflects
fulfillment, not returns.

## Dates and Time Context

`ORDERS.O_ORDERDATE` is the date the Sale was placed and is the only date
attribute at the Sale grain. Since the Business Specification defines a
Sale as a transaction "placed by an Account," `O_ORDERDATE` is the physical
attribute that corresponds to when a Sale occurred.

`LINEITEM` additionally carries three fulfillment-related dates at the Sale
Line grain: `L_SHIPDATE`, `L_COMMITDATE`, and `L_RECEIPTDATE`. These
represent when a Sale Line was shipped, committed to ship, and received,
respectively. The approved Business Specification does not establish any
business meaning for shipment, commitment, or receipt as distinct from the
Sale itself, and does not indicate that trending should be based on
fulfillment activity rather than when the Sale was placed. No business date
semantics beyond `O_ORDERDATE` are assumed here.

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

**Account/Vendor → Country → Sales Region**

```
CUSTOMER.C_NATIONKEY = NATION.N_NATIONKEY        (Account → Country)
SUPPLIER.S_NATIONKEY = NATION.N_NATIONKEY        (Vendor → Country)
NATION.N_REGIONKEY   = REGION.R_REGIONKEY        (Country → Sales Region)
```

## Open Context Questions

Both previously open questions have been resolved by approved business
decisions:

* **"Completed Sales" mapping** — resolved. See
  [Sales — Completed Sale Mapping](#sales--completed-sale-mapping).
* **Treatment of returned Sale Lines** — resolved. See
  [Returned Sale Lines Mapping](#returned-sale-lines-mapping).

No unresolved physical interpretation questions remain for the source
objects, KPIs, and relationships required by the approved Business
Specification.
