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

## Step 5: Reduce Costs with State-Aware Orchestration

*Your instructor will walk through this section as a demo and explain the concept on slides. Follow along with the steps below.*

<div style="background-color: #e8f4f8; padding: 12px 16px; border-left: 6px solid #0099cc; margin-bottom: 20px; margin-top: 10px;">
  <strong>ℹ️ The problem this solves:</strong><br>
  Without Fusion, every scheduled dbt job rebuilds <em>every model</em> — even if the upstream data hasn't changed. On a project with hundreds of models, this wastes significant time and warehouse compute. State-aware orchestration fixes this automatically.
</div>

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
