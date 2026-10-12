## 👨‍🍳 Cooking with CoCo — Run the Demo Yourself

This repository contains the Business Specification, Source Context, and Engineering Skills used in my **“Stop Prompting. Start Specifying: A Spec-Driven Approach to AI-Assisted Data Engineering”** presentation.

The objective is to demonstrate how Snowflake Cortex Code (CoCo) can use approved specifications, source context, and organizational engineering standards to autonomously engineer a data product—without prescribing the implementation through a series of prompts.

### Prerequisites

- Access to a Snowflake account with Cortex Code (CoCo) enabled.
- Access to the `SNOWFLAKE_SAMPLE_DATA` database and its TPC-H sample data.
- Appropriate Snowflake privileges to create and manage the demonstration objects.

### Step 1 — Set Up Your Snowflake Workspace

Create a Snowflake Workspace and reproduce the **same folder and file structure** found in this GitHub repository.

Copy the contents of the repository's specification, source context, and Skills files into the corresponding files in your Snowflake Workspace.

**Important:** Preserve the directory structure and file names. CoCo uses these artifacts to discover the business requirements, interpret the source data, and apply the approved engineering standards.

### Step 2 — Prepare the Target Database

Create an empty Snowflake database named:

`DATAMART_PROD`

This is the target database used in the demonstration.

Do not pre-create the dimensional models, business schemas, semantic objects, or other data-product components. The purpose of the exercise is to let CoCo derive and implement the solution from the approved artifacts.

### Step 3 — Execute the CoCo Prompt

1. Open Cortex Code (CoCo) in Snowflake.
2. Open the repository file **`CoCo-Prompt.md`**.
3. Copy the complete prompt text from that file.
4. Paste it into the CoCo prompt box.
5. Submit the prompt to begin the implementation.

CoCo should discover and interpret the Business Specification, Source Context, and applicable Skills before making engineering decisions.

Depending on your Snowflake environment and permission settings, you may be asked to authorize certain operations during execution.

### Step 4 — Observe and Validate

Watch how CoCo uses the approved artifacts to make implementation decisions, generate the required data-product components, and validate the results.

Once execution completes, review the results against the Business Specification and its acceptance criteria.

**The goal isn't to make AI deterministic. It's to make the engineering process around AI more deterministic.**

### The Key Principle

**Iterate on the specification, not the prompt.**

If the result doesn't meet expectations, determine whether CoCo failed to follow an explicit requirement or whether the approved artifacts need clarification.

The Business Specification defines **what success looks like**. Source Context explains **what the source data means**. Skills establish **how the organization expects engineering to be performed**.

CoCo uses these artifacts to determine **how to implement the solution**.

---

*This demonstration is intended for learning and experimentation. Review generated objects, SQL, privileges, and costs before applying this approach to production environments.*
