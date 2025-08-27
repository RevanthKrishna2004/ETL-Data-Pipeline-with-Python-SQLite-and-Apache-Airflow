# ETL-Data-Pipeline-with-Python-SQLite-and-Apache-Airflow
ETL Data Pipeline with Python, SQLite, and Apache Airflow
**ETL Data Pipeline with Python, SQLite, and Apache Airflow**

This project implements a complete **ETL (Extract, Transform, Load)
pipeline** for processing AI-related news articles. The pipeline
retrieves live data from the **News API**, cleans and transforms it
using **Python and Pandas**, and loads it into a **SQLite database**. To
ensure automation and orchestration, the pipeline is managed with
**Apache Airflow**, enabling daily scheduling, modular task execution,
and monitoring.

------------------------------------------------------------------------

**Objectives**

- Automate retrieval of AI-related news articles using the News API.

- Transform raw API responses into a structured, clean dataset.

- Store the data in a relational SQLite database for persistence and
  analysis.

- Orchestrate the entire workflow with Apache Airflow for automation and
  reliability.

------------------------------------------------------------------------

**Pipeline Architecture**

The pipeline follows the standard ETL workflow:

![A blue rectangle with white text Description automatically
generated](media/image1.png){width="6.5in"
height="1.7729166666666667in"}

**Extract → Transform → Load**

- **Extract**: Fetches AI-related news articles from the News API.

- **Transform**: Cleans and standardizes fields such as author names and
  publication dates.

- **Load**: Stores structured records into a SQLite database.

- **Orchestration**: Managed by an Airflow DAG with clear task
  dependencies and daily scheduling.

------------------------------------------------------------------------

**Implementation Steps**

**1. Extract Stage**

- The News API was queried for articles on the topic \"AI\".

- Articles were restricted to the previous 24 hours using dynamic
  from_date and to_date.

- Results were passed between Airflow tasks using **XComs**.

- Logging was used to capture successful and failed API calls.

result = news_api.get_everything(q=\"AI\", language=\"en\",
from_param=from_date, to=to_date)

kwargs\[\'task_instance\'\].xcom_push(key=\'extract_result\',
value=result\[\"articles\"\])

------------------------------------------------------------------------

**2. Transform Stage**

- Raw JSON responses were flattened into a **Pandas DataFrame**.

- Selected fields: Source, Author Name, News Title, URL, Date Published,
  Content.

- Publication dates were standardized to the format: YYYY-MM-DD
  HH:MM:SS.

- Author fields were cleaned:

  - Extracting the first author if multiple listed.

  - Converting names to title case.

  - Replacing missing authors with \"No Author\".

- The cleaned dataset was stored in **XComs** as JSON for downstream
  loading.

------------------------------------------------------------------------

**3. Load Stage**

- A SQLite database (etl_news_data.sqlite) was used for persistent
  storage.

- A news_table was created (if not already present).

- Transformed records were appended to the database using
  pandas.DataFrame.to_sql().

------------------------------------------------------------------------

**4. Orchestration with Apache Airflow**

The workflow was automated with Airflow:

- A **DAG** (news_etl) was defined with a **daily schedule** (@daily).

- Start date set dynamically to yesterday, with retry logic (retries=1).

- The pipeline was broken into three Python tasks:

  - extract_news

  - transform_news

  - load_news

- Dependencies enforced:

\_extract_news_data \>\> \_transform_news_data \>\> \_load_news_data

This ensured that tasks ran sequentially and data flowed smoothly across
the ETL process.

------------------------------------------------------------------------

**Results**

- The Airflow-managed pipeline successfully fetches **daily AI-related
  news articles**, transforms them, and stores them in a SQLite
  database.

- Logs and XComs provide transparency and facilitate debugging.

- The database now serves as a **central repository** of clean,
  structured AI news articles for analysis.
