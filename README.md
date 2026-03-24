# dbt Labs | dbt 101 + Accelerating with Fusion Hands-On Lab

Welcome to the dbt Labs Hands-On Lab! In this workshop, you'll go from zero to productive with dbt — learning the core concepts that power modern analytics engineering, then getting hands-on with **dbt Fusion**, the next-generation engine that makes building data pipelines faster, smarter, and more cost-efficient.

This lab is designed for **complete beginners**. Your instructor will walk through the foundational concepts using slides, and this guide will be your companion for every hands-on activity. By the end of the session, you'll have written and run real dbt code, explored Fusion's intelligent developer features, and seen how state-aware orchestration reduces wasted compute — all inside the dbt Platform.

---

<div style="background-color: #fff3cd; padding: 12px 16px; border-left: 6px solid #ffcc00; margin-bottom: 20px; margin-top: 10px;">
  <strong>⚠️ IMPORTANT:</strong><br>
  Before starting the hands-on portion, register for your workshop account at:<br>
  <strong><a href="https://workshops.us1.dbt.com/workshop">https://workshops.us1.dbt.com/workshop</a></strong><br><br>
  <strong>Workshop:</strong> Accelerating dbt with Fusion<br>
  <strong>Passcode:</strong> Provided by your instructor
</div>

---

## Prerequisites

- A modern web browser (Chrome or Firefox recommended)
- A valid email address
- No prior dbt experience required!

---

## Getting Started

1. Navigate to [https://workshops.us1.dbt.com/workshop](https://workshops.us1.dbt.com/workshop)
2. Select **Accelerating dbt with Fusion** from the workshop list
3. Enter the passcode provided by your instructor
4. Fill in your name and email to register
5. Save your auto-generated credentials — you may need them to log back in

---

## Step 1: Access Your Lab Environment

*Your instructor will cover the dbt Platform overview on slides. Use this step to get oriented in your personal lab environment before the hands-on exercises begin.*

### 1.1 Log In to dbt Platform

1. If you were redirected after registration, you are already logged in — skip to step 4
2. Otherwise, navigate to [https://cloud.getdbt.com](https://cloud.getdbt.com)
3. Log in using the credentials generated during registration
4. Confirm you can see the dbt Platform home screen

### 1.2 Navigate to the Studio IDE

1. In the left-hand navigation, click **Develop**
2. Select **Studio** from the menu
3. Wait for the Studio IDE to fully load — this may take a few seconds on first launch
4. You should see a split view with a **file explorer** on the left and an **editor pane** on the right

### 1.3 Get Oriented in the Studio IDE

Take a moment to locate the following areas — you'll use all of them during the lab:

| Area | Where to find it | What it does |
|---|---|---|
| **File Explorer** | Left sidebar | Browse all files in your dbt project |
| **Editor Pane** | Center | Write and edit SQL and YAML files |
| **Command Bar** | Bottom of the screen | Run dbt commands (e.g., `dbt run`, `dbt test`) |
| **Lineage Panel** | Bottom-right or via icon | Visual map of model dependencies |
| **Results / Logs Panel** | Below the editor | Shows run output, errors, and timing |

1. Click on the **File Explorer** to expand it
2. Expand the `models` folder to see the project structure
3. Locate the **Command Bar** at the bottom of the screen

---

## Step 2: Explore dbt Models

*Your instructor will cover dbt models on slides, including what a model is, how `ref()` works, materializations, and project structure (Staging → Intermediate → Marts). Use this step to explore those concepts in the live project.*

### 2.1 Browse the Project Structure

1. In the **File Explorer**, expand the `models` folder
2. Notice the subfolders — you should see folders named `staging`, `intermediate` (or `int`), and `marts` (or similar)
3. Expand the `staging` folder and open any `.sql` file inside it
4. Read through the SQL — staging models typically rename columns, cast data types, and clean raw data with minimal logic

**Understanding the folder structure:**
- `staging/` — One model per raw source table; lightweight transformations only (renames, type casts, formatting)
- `intermediate/` — Heavier logic: joins, calculations, business rules
- `marts/` — Final tables connected to dashboards, ML models, or other consumption points

### 2.2 Open and Read a Model

1. In the `staging` folder, open any model file (e.g., `stg_customers.sql` or similar)
2. Read the `SELECT` statement — notice it selects from either a `{{ source() }}` or `{{ ref() }}` function rather than a hardcoded table path
3. Open a model in the `marts` folder
4. Find a `{{ ref('...') }}` call in the code and note the model name it references

> **Note:** The `{{ ref() }}` function is how dbt models reference each other. dbt uses these references to automatically build a dependency graph (called a DAG) and determine the correct execution order.

### 2.3 Run Your First Model

1. In the **File Explorer**, open the `marts` folder and click on any mart model file
2. With the file open in the editor, locate the **Run** button in the top-right corner of the editor pane
3. Click the **dropdown arrow** next to the Run button
4. Select **Run model** (this runs only the selected model)
5. Watch the **Results / Logs Panel** at the bottom — you should see output like:

```
Running with dbt=1.x.x
Found X models, Y tests...
1 of 1 START sql table model dev.your_schema.model_name ......... [RUN]
1 of 1 OK created sql table model dev.your_schema.model_name .... [OK in X.XXs]
```

6. Confirm the run completed with an `OK` status

> **Note:** Your model is being built in your personal **development schema** in the data warehouse — not in production. Other users' work is completely isolated from yours.

### 2.4 View the Lineage DAG

1. With your model file open, click the **Lineage** tab or icon (usually at the bottom of the editor or in the toolbar)
2. Observe the graph — your model should appear in the center, with upstream dependencies to the left and downstream dependents to the right
3. Hover over a node in the graph to see the model name and type
4. Click on an upstream node to jump to that model file

> **Note:** This is dbt's **DAG (Directed Acyclic Graph)** — the dependency map dbt automatically builds from your `{{ ref() }}` calls. dbt uses this to run models in the correct order every time.

---

## Step 3: Work with dbt Sources and Tests

*Your instructor will cover dbt sources and tests on slides. Use this step to find them in the project and add your own test.*

### 3.1 Explore the Sources YAML

1. In the **File Explorer**, expand the `models` folder and look for a folder named `sources` or a file named `sources.yml` (it may also be inside `staging/`)
2. Open the sources YAML file
3. Review the structure — you should see something like:

```yaml
version: 2

sources:
  - name: raw
    database: raw_data
    schema: public
    tables:
      - name: customers
      - name: orders
```

4. Identify the **source name**, **database**, and **schema** — this is how dbt knows where your raw data lives in the warehouse
5. Note the table names listed — each one can be referenced in a model using `{{ source('raw', 'customers') }}`

### 3.2 Trace a Source Reference in a Model

1. Go back to a staging model you opened earlier (e.g., `stg_customers.sql`)
2. Find the `{{ source('...', '...') }}` call in the `FROM` clause
3. Confirm that the source name and table name match what you saw in the `sources.yml` file
4. In the **Lineage** panel, look for **SRC** nodes to the left of your staging models — these represent your source tables

### 3.3 Run Existing Tests

1. In the **Command Bar** at the bottom of the screen, click to focus it
2. Type the following command and press **Enter**:

```
dbt test
```

3. Watch the **Results / Logs Panel** — dbt will run all defined tests and report results:

```
1 of 4 START test not_null_customers_customer_id ........... [RUN]
1 of 4 PASS not_null_customers_customer_id ................ [PASS in X.XXs]
```

4. Confirm all tests pass (green `PASS` indicators)

> **Note:** If any tests fail, the log will show a `FAIL` status and the number of failing rows. This is expected behavior — dbt stops bad data from flowing downstream.

### 3.4 Add Your Own Test

1. In the **File Explorer**, find the YAML file that defines your model's columns — it will have the same name as the model but with a `.yml` extension (e.g., `stg_customers.yml`). It may be in the same folder as the model
2. Open the YAML file and find the `columns:` section
3. Add a `not_null` test to any column that should never be empty. For example:

```yaml
models:
  - name: stg_customers
    columns:
      - name: customer_id
        tests:
          - unique
          - not_null
      - name: email
        tests:
          - not_null    # <-- add this line to an existing column
```

4. Save the file using **Cmd+S** (Mac) or **Ctrl+S** (Windows)
5. In the **Command Bar**, run only the tests for your model:

```
dbt test --select stg_customers
```

6. Confirm your new test appears in the results and passes

> **Note:** dbt translates your YAML test definitions into SQL queries and runs them directly in your data warehouse. No test-specific code to write — just declare what should be true about your data.

---

## Step 4: Build Faster with dbt Fusion

*Your instructor will cover the Fusion engine on slides. These exercises let you experience Fusion's native SQL comprehension features firsthand in the Studio IDE.*

<div style="background-color: #e8f4f8; padding: 12px 16px; border-left: 6px solid #0099cc; margin-bottom: 20px; margin-top: 10px;">
  <strong>ℹ️ What makes this different:</strong><br>
  The Studio IDE you've been using is already powered by the dbt Fusion engine. The features in this step are only possible because Fusion reads and understands your entire SQL project <em>locally</em> — before any query is sent to the warehouse.
</div>

### 4.1 Smart Autocompletion with IntelliSense

1. In the **File Explorer**, create a new file in the `marts` folder — click the **+** icon next to the folder name and name it `my_first_mart.sql`
2. Type the following to start a new model:

```sql
SELECT

FROM {{ ref('
```

3. After typing the opening quote inside `ref('`, **pause** — Fusion will display a dropdown list of all available models in the project
4. Use the **arrow keys** to browse the list, then press **Enter** or **Tab** to select a model
5. Move your cursor into the `SELECT` clause and type the alias you gave the referenced model followed by a period (`.`)
6. Observe how Fusion **autocompletes column names** from the upstream model — without you having to look them up

> **Note:** This is Fusion's native SQL comprehension in action. It knows the schema of every model in your project and surfaces that context directly in the editor.

### 4.2 Inline CTE Previews

1. Open a model that contains one or more CTEs (a `WITH` clause at the top) — look in the `intermediate` or `marts` folder
2. If you don't see a CTE, here is a simple example you can paste into `my_first_mart.sql`:

```sql
WITH orders AS (
    SELECT *
    FROM {{ ref('stg_orders') }}
),

customers AS (
    SELECT *
    FROM {{ ref('stg_customers') }}
)

SELECT
    c.customer_id,
    c.email,
    COUNT(o.order_id) AS total_orders
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
GROUP BY 1, 2
```

3. **Highlight** the name of a CTE (e.g., highlight just the word `orders` in the `FROM orders` line at the bottom)
4. Right-click and select **Preview** (or use the keyboard shortcut shown in the menu)
5. A results panel will open showing the CTE's output — without running the full model

> **Note:** Inline CTE previews let you inspect intermediate results as you build, dramatically speeding up the development and debugging cycle.

### 4.3 Real-Time Error Detection

1. In your open model, intentionally introduce a SQL error — reference a column that does not exist:

```sql
SELECT
    customer_id,
    this_column_does_not_exist,   -- intentional error
    total_orders
FROM customers c
```

2. Without running anything, observe the editor — Fusion should **immediately highlight** the invalid column reference with a red underline or error indicator
3. Hover over the highlighted text to read the error message
4. Fix the error by removing or correcting the column name
5. Confirm the error indicator disappears as soon as you correct the code

> **Note:** Traditional dbt (without Fusion) would only surface this error after sending the query to the warehouse. Fusion catches it locally in milliseconds — saving time and warehouse compute costs.

### 4.4 Hover Previews

1. In your model, find a column that is passed through from an upstream model (e.g., `customer_id`, `order_date`, or similar)
2. **Hover your cursor** over the column name in the `SELECT` clause
3. A tooltip should appear showing:
   - The column's **data type** (e.g., `VARCHAR`, `INTEGER`, `TIMESTAMP`)
   - The **source model** the column originates from
4. Try hovering over a few different columns to compare their types
5. Find a column that flows through multiple models (e.g., from `staging` → `intermediate` → `marts`) and confirm Fusion traces it back to its original source

> **Note:** Hover previews eliminate a common frustration: having to open multiple upstream model files just to check a column's data type. Fusion surfaces that information inline.

---

---
 
## Step 4a: Practice Task — Debug `fct_orders` with dbt Fusion
 
Your team needs a reliable **orders fact table** for downstream analytics. A colleague started the `fct_orders` model, but it has bugs: some will stop the query from compiling, and others will produce silently wrong results even after it runs.
 
Your job is to use **dbt Fusion** to find and fix every issue. There are **5 bugs** in total — a mix of compile errors, incorrect column references, and logic problems. Fusion will help you catch most of them without running a single query against the warehouse.
 
> **Tip:** Work through the sub-steps in order. Each one targets a different type of bug and a different Fusion feature. Don't skip ahead to the solution — the goal is to build the debugging instinct, not just fix the file.
 
---
 
### 4a.1 Open the Starter File
 
1. In the **File Explorer**, expand the `fusion_vhol_practice_tasks` folder, then open the `task_1` subfolder
2. Click on **`task_1_fct_orders_buggy.sql`** to open it in the editor
3. Read through the entire file before touching anything — notice it is built from five CTEs:
   - `orders` — pulls from a staging orders model
   - `stores` — pulls store location and tax context
   - `order_items` — pulls individual line items per order
   - `item_rollup` — aggregates items up to order grain
   - `joined` — joins everything together and calculates `expected_tax` and `tax_delta`
4. In the **File Explorer**, right-click the file and select **Move** (or drag it) into the `models/marts/` directory
5. Confirm the file now appears under `models/marts/` in the explorer
 
---
 
### 4a.2 Find and Fix the Compile Errors
 
Fusion reads your SQL in real time. As soon as the file is in your project, it will flag any references it cannot resolve — no need to run anything.
 
1. Look at the editor — you should see **red underlines or error indicators** on one or more lines
2. **Hover** over each underlined section to read the error message in the tooltip
3. Pay close attention to the `orders` CTE and the `stores` CTE — both contain errors Fusion will surface immediately
 
> **What to look for:** Fusion flags unresolved `ref()` calls (model names that don't exist) and column names that don't exist on the referenced model.
 
<details>
<summary>💡 Hint — Bug 1: orders CTE</summary>
 
Check the model name inside `ref()` in the `orders` CTE. Compare it carefully to the actual staging model name — it's a one-character difference.
 
```sql
-- Buggy
from {{ ref('stg_jaffle_shop__order') }}
 
-- Fixed
from {{ ref('stg_jaffle_shop__orders') }}
```
 
</details>
 
<details>
<summary>💡 Hint — Bug 2: stores CTE</summary>
 
Hover over the column name in the `stores` CTE SELECT list. Fusion will show the available column names on `stg_jaffle_shop__stores` — compare them to what's written.
 
```sql
-- Buggy
taxrate
 
-- Fixed
tax_rate
```
 
</details>
 
4. Fix both errors, then confirm the red underlines disappear from the editor
 
---
 
### 4a.3 Preview the `item_rollup` CTE to Check Grain
 
The model compiles now — but that doesn't mean the logic is correct. The `item_rollup` CTE is supposed to produce **exactly one row per `order_id`**. Use Fusion's CTE preview to verify this before running anything.
 
1. In the editor, locate the `item_rollup` CTE
2. **Highlight** the word `item_rollup` where it appears in the `FROM` clause of the `joined` CTE (or anywhere it is named)
3. Right-click and select **Preview** to open the CTE output in the results panel
4. Look at the result set:
   - How many rows are there?
   - Are there multiple rows for the same `order_id`?
   - Compare the values in `items_count` vs `distinct_products_count` — do they make sense?
 
> **What you're checking:** If `item_rollup` has more than one row per `order_id`, joining it to `orders` will fan out and duplicate rows in the final model. This must be fixed before going further.
 
You should find **two logic bugs** in the `item_rollup` CTE. Look carefully at the `GROUP BY` clause and the aggregate functions.
 
<details>
<summary>💡 Hint — Bug 3: distinct product count</summary>
 
`count(product_id)` counts every line item, including duplicates of the same product in an order. To count unique products, use `DISTINCT`:
 
```sql
-- Buggy
count(product_id) as distinct_products_count
 
-- Fixed
count(distinct product_id) as distinct_products_count
```
 
</details>
 
<details>
<summary>💡 Hint — Bug 4: GROUP BY breaks order grain</summary>
 
Adding `product_id` to the `GROUP BY` creates one row per product per order — not one row per order. The rollup must group only by `order_id`:
 
```sql
-- Buggy
group by order_id, product_id
 
-- Fixed
group by order_id
```
 
</details>
 
5. Apply both fixes, then **re-run the CTE preview** on `item_rollup`
6. Confirm the output now shows exactly one row per `order_id` and that `items_count` ≥ `distinct_products_count` for every row
 
---
 
### 4a.4 Fix the Join Condition
 
There is one more bug — this time in the `joined` CTE. It's a subtle one that may or may not surface as a red underline depending on your SQL dialect, but it will produce incorrect results.
 
1. Locate the `joined` CTE and read the `LEFT JOIN item_rollup` condition carefully
2. The `FROM` clause of `joined` is `from orders` — think about what table aliases are in scope
3. Check whether the left side of the join condition references a CTE that is **not** in the `FROM` clause of `joined`
 
<details>
<summary>💡 Hint — Bug 5: wrong join key alias</summary>
 
The join condition references `order_items.order_id`, but `order_items` is not in the `FROM` clause of `joined` — `orders` is. This either causes a compilation error or silently joins on the wrong reference:
 
```sql
-- Buggy
left join item_rollup
    on order_items.order_id = item_rollup.order_id
 
-- Fixed
left join item_rollup
    on orders.order_id = item_rollup.order_id
```
 
</details>
 
4. Fix the join condition
 
---
 
### 4a.5 Run the Model and Validate
 
With all five bugs fixed, it's time to run the model and confirm it meets the acceptance criteria.
 
1. With `task_1_fct_orders_buggy.sql` open in the editor, click the **Run** button dropdown
2. Select **Run model** to build only this model in your development schema
3. Confirm the run completes with an `OK` status in the **Results / Logs Panel**:
 
```
1 of 1 START sql table model dev.your_schema.task_1_fct_orders_buggy ....... [RUN]
1 of 1 OK created sql table model dev.your_schema.task_1_fct_orders_buggy .. [OK in X.XXs]
```
 
4. Click **Preview** on the final `select * from joined` to inspect the output
5. Work through the checklist below to confirm everything looks right:
 
| Check | What to verify |
|---|---|
| ✅ One row per `order_id` | No duplicate `order_id` values in the output |
| ✅ `tax_rate` is populated | No null values in the `tax_rate` column |
| ✅ `items_count` ≥ `distinct_products_count` | Every row has at least as many items as distinct products |
| ✅ `expected_tax` calculates correctly | Value equals `subtotal * tax_rate` — no type errors |
| ✅ `tax_delta` looks reasonable | Small positive or negative values relative to `expected_tax` |
| ✅ Sorted correctly | Results are ordered by `order_date desc, order_id` |
 
---
 
### ✅ Task 1 Complete
 
Nice work! You used Fusion's real-time error detection, hover tooltips, and CTE previews to catch five different types of bugs — compile errors, a wrong column name, an incorrect aggregate, a grain-breaking `GROUP BY`, and a bad join reference — before running a single warehouse query.
 
> **What's next:** Task 2 follows the same pattern but with fewer hints. You'll need to rely more on Fusion's feedback and your own grain awareness. Take what you learned here and apply it independently.

## Step 5: Reduce Costs with State-Aware Orchestration

*Your instructor will walk through this section as a demo and explain the concept on slides. Follow along with the steps below.*

<div style="background-color: #e8f4f8; padding: 12px 16px; border-left: 6px solid #0099cc; margin-bottom: 20px; margin-top: 10px;">
  <strong>ℹ️ The problem this solves:</strong><br>
  Without Fusion, every scheduled dbt job rebuilds <em>every model</em> — even if the upstream data hasn't changed. On a project with hundreds of models, this wastes significant time and warehouse compute. State-aware orchestration fixes this automatically.
</div>

---
 
## Step 4b: State-Aware Orchestration — Configure Model Freshness
 
State-aware orchestration (SAO) is one of Fusion's most impactful capabilities. Instead of rebuilding every model on every run, dbt can now determine which models actually need to be rebuilt — based on whether upstream data has changed and whether enough time has passed since the last build.
 
In this step, you'll configure freshness rules on three models and run the same production job twice, predicting what will happen before each run.
 
> **Reference:** [dbt Freshness Configuration Docs](https://docs.getdbt.com/reference/resource-configs/freshness)
 
---
 
### 4b.1 Confirm SAO Is Enabled on the Production Job
 
1. In the left-hand navigation, click **Orchestration → Jobs**
2. Open **Prod Job**
3. Click **Settings** in the top-right corner
4. Under **Execution settings**, confirm the **Enable Fusion cost optimization features** checkbox is checked
5. Click **Save**
 
---
 
### 4b.2 Configure the Job to Run on Your Branch
 
By default, the production job runs against the main branch. For this activity, you'll point it at your personal development branch so your config changes are picked up.
 
1. Navigate to **Orchestration → Jobs**
2. Click into **Prod Job**, then click **Edit**
3. Under **Environment settings**, check **Only run on a custom branch**
4. In the text box that appears, type your branch name exactly as it appears in the Studio IDE
5. Click **Save**
 
---
 
### 4b.3 Add `build_after` to `dim_customers`
 
The `build_after` config tells SAO: *"even if upstream data has changed, don't rebuild this model unless at least X time has passed since its last build."*
 
You will set `dim_customers` to only rebuild if it is at least **3 hours** old.
 
1. In the **File Explorer**, navigate to `models/marts/` and open **`dim_customers.yml`**
2. Find the `- name: dim_customers` entry
3. Add the `config:` block directly under the model `description:` line, so it looks like this:
 
```yaml
- name: dim_customers
  description: This model....
  config:
    freshness:
      build_after:
        count: 3
        period: hour
  columns:
    - name: customer_id
```
 
4. Save the file with **Cmd+S** (Mac) or **Ctrl+S** (Windows)
5. Commit your changes using the **Git Integration** button in the top-left corner
 
> **What this means:** Even if a source upstream of `dim_customers` receives new data, SAO will skip the rebuild if the model was built less than 3 hours ago. This is useful for expensive models that don't need to refresh on every pipeline run.
 
---
 
### 4b.4 Add `build_after` to `stg_jaffle_shop__orders`
 
You will set this staging model to only rebuild if at least **2 minutes** have passed since its last build.
 
1. In the **File Explorer**, navigate to `models/staging/jaffle_shop/` and open **`stg_jaffle_shop.yml`**
2. Find the `- name: stg_jaffle_shop__orders` entry
3. Add the `config:` block directly under its `description:` line:
 
```yaml
- name: stg_jaffle_shop__orders
  description: "{{ doc('stg_jaffle_shop__orders') }}"
  config:
    freshness:
      build_after:
        count: 2
        period: minute
  columns:
    - name: order_id
```
 
4. Save the file — but **do not commit yet**, as you still have one more change to make in this file
 
> **What this means:** This model will be skipped if it was built within the last 2 minutes, even if upstream data changed. For staging models that run frequently, this prevents unnecessary rebuilds within short windows.
 
---
 
### 4b.5 Add `updates_on: all` to `stg_jaffle_shop__stores`
 
The `updates_on` config controls how many upstream sources need new data before a model qualifies for a rebuild. The default is `any` — meaning the model rebuilds if *at least one* upstream source has new data. Changing it to `all` means the model only rebuilds if *every* direct upstream source has new data.
 
1. Still in **`stg_jaffle_shop.yml`**, find the `- name: stg_jaffle_shop__stores` entry
2. Add the `config:` block under its `description:` line:
 
```yaml
- name: stg_jaffle_shop__stores
  description: "{{ doc('stg_jaffle_shop__stores') }}"
  config:
    freshness:
      updates_on: all
  columns:
    - name: store_id
```
 
3. Save the file
4. Commit all your changes using the **Git Integration** button
 
---
 
### 4b.6 Prediction Exercise — Run #1
 
Before triggering the job, pause and think through what SAO will do. For each of the three models you just configured, write down your prediction:
 
| Model | Will it build or be reused? | Why? |
|---|---|---|
| `dim_customers` | | |
| `stg_jaffle_shop__orders` | | |
| `stg_jaffle_shop__stores` | | |
 
Consider:
- Has enough time passed since the last build to satisfy the `build_after` threshold?
- Did upstream source data change since the last run?
- Does the model require **any** or **all** upstream sources to have new data?
 
<details>
<summary>🔍 Expected outcome for Run #1</summary>
 
**`dim_customers` → Reused**
The `build_after: 3 hours` threshold has not been met — the model was built too recently. SAO skips the rebuild regardless of whether upstream data changed.
 
**`stg_jaffle_shop__orders` → Rebuilt**
Upstream changes were detected and the `build_after: 2 minutes` threshold was satisfied. The model qualifies for a rebuild.
 
**`stg_jaffle_shop__stores` → Rebuilt**
Upstream changes were detected and all direct upstream sources had qualifying changes, satisfying the `updates_on: all` condition.
 
</details>
 
---
 
### 4b.7 Run the Production Job — First Run
 
1. Navigate to **Orchestration → Jobs**
2. Find **Prod Job** and click **Run now**
3. Click into the running job to watch the live output
4. As models execute, note which are marked **built** and which are **reused/skipped**
5. After the run completes, compare the actual results to your prediction above
 
> **What to look for in the logs:** Lines will show either a build status (time taken, rows affected) or a skip/reuse status explaining why the model was not rebuilt.
 
---
 
### 4b.8 Prediction Exercise — Run #2
 
You are about to run the exact same job again, immediately, without introducing any new source data.
 
Before clicking **Run**, write down your predictions for all three models again:
 
| Model | Will it build or be reused? | Why? |
|---|---|---|
| `dim_customers` | | |
| `stg_jaffle_shop__orders` | | |
| `stg_jaffle_shop__stores` | | |
 
<details>
<summary>🔍 Expected outcome for Run #2</summary>
 
**All three models → Reused**
 
No new upstream data was introduced between Run #1 and Run #2. With no qualifying changes detected:
- `dim_customers` still has not met its 3-hour threshold
- `stg_jaffle_shop__orders` sees no new upstream data to trigger a rebuild
- `stg_jaffle_shop__stores` sees no new upstream data across any of its sources
 
SAO correctly reuses all prior results, and no warehouse compute is spent.
 
</details>
 
---
 
### 4b.9 Run the Production Job — Second Run
 
1. Click **Run now** on the same **Prod Job**
2. Observe the results
3. Compare what happened to both your prediction and to Run #1
 
> **Reflect:** Think about what this means at scale. In a real project with hundreds of models running on a schedule, SAO can eliminate the majority of unnecessary builds — without any changes to how you write your models, just through freshness configuration.
 
---
 
### ✅ Step 4b Complete
 
You've configured three different SAO behaviors — a time-based hold (`build_after`), a short-window gate, and a strict upstream requirement (`updates_on: all`) — and observed how each one changes what dbt decides to build vs. skip. This is one of the most direct levers you have for reducing warehouse costs in a dbt Platform environment powered by Fusion.

### 5.1 Understand the Concept Using the Lineage View

1. In the Studio IDE, open any mart model and view its **Lineage** panel
2. Trace the chain from left to right: **Source → Staging → Intermediate → Mart**
3. Imagine only the source on the left has new data since the last run — consider which models actually need to be rebuilt vs. which ones could be safely skipped
4. This is exactly what Fusion's state-aware orchestration determines automatically — no manual configuration required

**Without Fusion state-awareness:**
- All 5 models in the chain → **5 models built** on every run

**With Fusion state-awareness:**
- Only the source on the left has new data → **3 models built**, **2 models reused**

### 5.2 Observe a Production Job Run Log

1. In the left-hand navigation, click **Deploy** → **Jobs**
2. Locate the pre-configured **Production Job** provided by your instructor
3. Click on the job name to open it
4. Click on the most recent **Run** to open the run detail
5. In the run log, look for entries marked `[RUN]` and `[SKIP]` or `[REUSE]`:

```
1 of 5 START sql table model prod.stg_orders .............. [RUN]
1 of 5 OK   sql table model prod.stg_orders .............. [OK in 2.3s]
2 of 5 SKIP sql view model  prod.int_orders .............. [SKIP - upstream unchanged]
3 of 5 SKIP sql table model prod.mart_customers .......... [SKIP - upstream unchanged]
```

6. Note the difference in run time between models that were built vs. skipped

### 5.3 View the Lineage Status After a Run

1. Navigate back to the Studio IDE
2. Open a model that was listed as `SKIP` in the run log
3. In the **Lineage** panel, look for status indicators on each node — built models and reused (skipped) models may be shown with different colors or icons
4. Discuss with your neighbor or instructor: what business scenario would cause more models to be rebuilt vs. skipped?

---

## Bonus: Explore on Your Own

If you've finished all the steps above and have time remaining, try one or more of the following:

- **Add a second test type**: Go back to Step 3.4 and add an `accepted_values` test to a column that has a fixed set of valid values (e.g., a status column)
- **Build a full model chain**: In the Command Bar, run `dbt run --select +my_first_mart` — the `+` prefix tells dbt to also run all upstream dependencies
- **Generate documentation**: In the Command Bar, run `dbt docs generate` and then click the **docs** icon to browse auto-generated documentation for the project

---

## Need Help?

Raise your hand or ask a lab instructor — we're here to help!

---

## What's Next?

- 📚 **dbt Documentation**: [docs.getdbt.com](https://docs.getdbt.com)
- 🎓 **Free dbt Courses**: [courses.getdbt.com](https://courses.getdbt.com) — start with *dbt Fundamentals*
- 💬 **dbt Community Slack**: [community.getdbt.com](https://community.getdbt.com) — 30,000+ analytics engineers
- 📰 **dbt Blog**: [getdbt.com/blog](https://getdbt.com/blog)
- 🚀 **Learn more about Fusion**: [getdbt.com/product/dbt-fusion-engine](https://getdbt.com/product/dbt-fusion-engine)
