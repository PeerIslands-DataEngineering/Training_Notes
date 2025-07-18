# Databricks Lakeflow Jobs 

Databricks is a unified data analytics platform that simplifies big data processing, machine learning, and data engineering tasks by leveraging Apache Spark. A key feature of Databricks is its **Lakeflow Jobs** (previously referred to as Databricks Jobs), which allows users to orchestrate and automate workflows for data processing, machine learning, and analytics. This tutorial provides a detailed guide to creating, configuring, and managing Lakeflow Jobs in Databricks, complete with explanations and examples.

## What are Databricks Lakeflow Jobs?

Lakeflow Jobs is a workflow automation tool in Databricks that enables users to schedule and orchestrate multiple tasks as part of a larger workflow. A job consists of one or more tasks, which can include running notebooks, pipelines, or other operations. These tasks can be organized with dependencies, conditional logic (e.g., if/else), or loops, and are visually represented as a **Directed Acyclic Graph (DAG)**.

### Key Components of Lakeflow Jobs
1. **Job**: The primary resource for coordinating and scheduling tasks. A job can range from a single task (e.g., running a notebook) to a complex workflow with hundreds of tasks and dependencies.
2. **Task**: A specific unit of work within a job, such as running a notebook, a pipeline, or a Python script. Tasks can have parameters and dependencies.
3. **Trigger**: Defines when a job runs, either time-based (e.g., daily at 2 AM) or event-based (e.g., when new data arrives in cloud storage).
4. **Parameters**: Runtime variables passed to tasks for dynamic execution.
5. **Notifications**: Alerts (e.g., email, Slack, or webhooks) for job events like failures or long-running tasks.
6. **Git Integration**: Source control for task code, ensuring consistency across job runs.

## Step-by-Step Tutorial: Creating and Managing a Lakeflow Job

This tutorial will walk you through creating a simple Lakeflow Job in Databricks that processes a dataset, applies a transformation, and validates data quality. We'll use the Databricks UI and provide code examples for programmatic management using the Databricks SDK and REST API.

### Prerequisites
- An active Databricks account (on AWS, Azure, or GCP).
- A Databricks workspace with permissions to create clusters, notebooks, and jobs.
- A notebook in your workspace (we'll create one for this tutorial).
- Basic familiarity with Python or another supported language (SQL, Scala, or R).
- Optional: Databricks CLI installed for programmatic management (version 0.218.0 or above).

### Step 1: Create a Notebook for the Job
Jobs often involve running Databricks notebooks. Let’s create a sample notebook to process a dataset of baby names and filter it by year.

1. **Navigate to the Databricks Workspace**:
   - In your Databricks workspace, click **New** in the sidebar and select **Notebook**.
   - Name the notebook `FilterBabyNames`.
   - Choose **Python** as the default language and select a cluster (or create a new one with default settings).

2. **Write the Notebook Code**:
   Below is a sample Python script for the notebook that reads a dataset, filters it by a parameter (year), and saves the output to a Delta table.

   ```python
   # FilterBabyNames notebook
   from pyspark.sql import SparkSession

   # Get the year parameter passed to the job
   year = dbutils.widgets.get("year")

   # Initialize Spark session
   spark = SparkSession.builder.appName("FilterBabyNames").getOrCreate()

   # Sample dataset (replace with your data source, e.g., a CSV in DBFS or cloud storage)
   data = [
       (1, "Alice", 2014, 100),
       (2, "Bob", 2014, 150),
       (3, "Cathy", 2015, 200),
       (4, "David", 2015, 180)
   ]
   df = spark.createDataFrame(data, ["id", "name", "year", "count"])

   # Filter by year
   filtered_df = df.filter(df.year == year)

   # Save output to a Delta table
   output_path = f"/mnt/output/baby_names_{year}"
   filtered_df.write.mode("overwrite").format("delta").save(output_path)

   # Display results for verification
   display(filtered_df)
   ```

   - This script uses a widget to accept a `year` parameter, filters a sample dataset, and saves the result as a Delta table.
   - Replace the sample dataset with a real data source (e.g., a CSV file in cloud storage like AWS S3 or Azure Blob Storage).

3. **Test the Notebook**:
   - Run the notebook manually in the Databricks UI to ensure it works. Pass a test value for the `year` widget (e.g., `2014`).
   - Verify the output in the Delta table at `/mnt/output/baby_names_2014`.

### Step 2: Create a Lakeflow Job in the Databricks UI
Now, let’s create a job that runs the `FilterBabyNames` notebook.

1. **Navigate to Jobs & Pipelines**:
   - In the Databricks workspace sidebar, click **Jobs & Pipelines**.
   - Click **Create Job**.

2. **Configure the Job**:
   - **Job Name**: Enter `FilterBabyNamesJob`.
   - **Task Name**: Enter `filter-baby-names`.
   - **Type**: Select **Notebook**.
   - **Notebook Path**: Use the file browser to select the `FilterBabyNames` notebook.
   - **Cluster**: Choose an existing cluster or create a new **Job Cluster** (recommended for cost efficiency, as it terminates after the job). For this tutorial, select a cluster with Databricks Runtime 9.1 or later.
   - **Parameters**: Add a key-value pair:
     - **Key**: `year`
     - **Value**: `2014`
   - **Schedule**: Set a trigger to run the job daily at 11:29 AM (or leave it unscheduled for manual runs).
   - **Notifications**: Optionally, add an email or Slack webhook for job failure alerts.

3. **Save and Run the Job**:
   - Click **Create** to save the job.
   - To run immediately, click **Run now** in the **Runs** tab.
   - Monitor the job in the **Active Runs** or **Completed Runs** table. Click the task name to view the output and logs.

4. **Verify the Output**:
   - Check the Delta table at `/mnt/output/baby_names_2014` to confirm the filtered data.
   - View the job run details in the UI to see the task’s status, duration, and logs.

### Step 3: Add Conditional Logic to the Job
Let’s extend the job to include multiple tasks with conditional logic (e.g., checking for nulls and running a validation task).

1. **Create a Second Notebook for Data Quality Validation**:
   - Create a new notebook named `ValidateBabyNames`.
   - Add the following code to check for null values in the dataset:

   ```python
   # ValidateBabyNames notebook
   from pyspark.sql import SparkSession

   year = dbutils.widgets.get("year")
   spark = SparkSession.builder.appName("ValidateBabyNames").getOrCreate()

   # Read the filtered data
   input_path = f"/mnt/output/baby_names_{year}"
   df = spark.read.format("delta"). загрузить(input_path)

   # Check for nulls
   null_count = df.filter(df.name.isNull()).count()

   if null_count > 0:
       raise Exception(f"Found {null_count} null values in the dataset for year {year}")
   else:
       print(f"No null values found for year {year}")
   ```

2. **Update the Job**:
   - Go to the **Jobs & Pipelines** tab and select `FilterBabyNamesJob`.
   - Click **Add Task**.
   - **Task Name**: `validate-baby-names`.
   - **Type**: Select **Notebook**.
   - **Notebook Path**: Select the `ValidateBabyNames` notebook.
   - **Depends On**: Select the `filter-baby-names` task to ensure it runs after the first task.
   - **Parameters**: Add `year` with value `2014`.
   - Optionally, add an **if/else condition**:
     - Create a third task (e.g., `transform-data`) to run if no nulls are found, or rerun `filter-baby-names` with different parameters if nulls are detected.
     - Use the Databricks UI’s visual authoring tool to define this logic (e.g., if `validate-baby-names` succeeds, run `transform-data`; otherwise, rerun `filter-baby-names`).

3. **Run and Monitor**:
   - Run the updated job and check the DAG in the UI to visualize task dependencies.
   - Verify the output and logs for both tasks.

### Step 4: Programmatic Job Management with Databricks SDK
For automation, you can create and manage jobs programmatically using the **Databricks SDK for Python** or the **REST API**. Below is an example using the Python SDK.

1. **Install the Databricks SDK**:
   ```bash
   pip install databricks-sdk
   ```

2. **Create a Job Programmatically**:
   Use the following Python script to create a job that runs the `FilterBabyNames` notebook.

   ```python
   from databricks.sdk import WorkspaceClient
   from databricks.sdk.service import jobs
   import os
   import time

   # Initialize the WorkspaceClient
   w = WorkspaceClient()

   # Define notebook path and cluster ID
   notebook_path = "/Users/<your-username>/FilterBabyNames"
   cluster_id = os.environ["DATABRICKS_CLUSTER_ID"]  # Set this environment variable

   # Create a job
   created_job = w.jobs.create(
       name=f"FilterBabyNamesJob-{int(time.time_ns())}",
       tasks=[
           jobs.Task(
               task_key="filter-baby-names",
               existing_cluster_id=cluster_id,
               notebook_task=jobs.NotebookTask(
                   notebook_path=notebook_path,
                   base_parameters={"year": "2014"}
               ),
               timeout_seconds=0
           )
       ]
   )

   # Run the job
   run_response = w.jobs.run_now(job_id=created_job.job_id)

   print(f"Job created: {w.config.host}/#job/{created_job.job_id}")
   print(f"Run ID: {run_response.response.run_id}")
   ```

   - Replace `<your-username>` with your Databricks workspace username.
   - Set the `DATABRICKS_CLUSTER_ID` environment variable to an existing cluster ID.
   - This script creates a job, runs it, and prints the job URL and run ID.

3. **Monitor and Delete the Job**:
   ```python
   # Monitor the job run
   run_output = w.jobs.get_run_output(run_id=run_response.response.run_id)
   print(run_output)

   # Delete the job (cleanup)
   w.jobs.delete(job_id=created_job.job_id)
   ```

### Step 5: Using Databricks Asset Bundles for Job Deployment
**Databricks Asset Bundles** allow you to manage jobs and their artifacts (e.g., notebooks) programmatically using configuration files. This is ideal for CI/CD pipelines.

1. **Create a Bundle**:
   - Install the Databricks CLI:
     ```bash
     pip install databricks-cli
     databricks -v  # Ensure version >= 0.218.0
     ```
   - Create a bundle using the default Python template:
     ```bash
     databricks bundle init python
     ```

2. **Define the Job in Configuration Files**:
   - Edit `databricks.yml`:
     ```yaml
     bundle:
       name: filter-baby-names-bundle
     resources:
       jobs:
         filter_baby_names_job:
           name: FilterBabyNamesJob
           tasks:
             - task_key: filter-baby-names
               existing_cluster_id: <your-cluster-id>
               notebook_task:
                 notebook_path: ./src/FilterBabyNames.ipynb
                 base_parameters:
                   year: "2014"
     ```
   - Place the `FilterBabyNames` notebook in the `src/` folder as `FilterBabyNames.ipynb`.

3. **Deploy and Run the Job**:
   - Validate the bundle:
     ```bash
     databricks bundle validate
     ```
   - Deploy to the workspace:
     ```bash
     databricks bundle deploy
     ```
   - Run the job:
     ```bash
     databricks bundle run filter_baby_names_job
     ```

4. **Verify Deployment**:
   - Check the workspace UI under **Jobs & Pipelines** to confirm the job was created.
   - View the notebook in `Users/<your-username>/.bundle/<project-name>/dev/files/src`.

### Step 6: Monitoring and Debugging
Databricks provides robust tools for monitoring and debugging jobs:

1. **UI Monitoring**:
   - In the **Jobs & Pipelines** tab, view job run history, task details, and metrics (e.g., duration, status).
   - Click a task to see logs and output.

2. **Spark UI**:
   - Access the Spark UI from the job run details to analyze task execution, stages, and bottlenecks.

3. **Notifications**:
   - Configure email or Slack notifications for job failures or long-running tasks in the **Job details** panel.

4. **REST API for Monitoring**:
   - Retrieve job run details programmatically:
     ```python
     run_list = w.jobs.list_runs(job_id=created_job.job_id)
     print(run_list)
     ```
   - Use the `GET /api/2.1/jobs/r Runs/get` endpoint for detailed run metadata.

5. **Debugging Tips**:
   - Check logs in the job run details for error messages.
   - Use the Spark UI to identify slow tasks or resource bottlenecks.
   - Implement logging in your notebook code (e.g., `print` statements or `logging` module) for runtime insights.
   - Test interactively in the notebook before scheduling as a job.

### Example: Multi-Task Job with Dependencies
Here’s an example of a job with three tasks:
1. Ingest data.
2. Check for nulls.
3. Transform data if no nulls; otherwise, run a validation task.

**Job Configuration (via UI or Bundle)**:
- **Task 1: ingest-data** (Notebook: `IngestData`, ingests revenue data).
- **Task 2: check-nulls** (Notebook: `CheckNulls`, depends on `ingest-data`).
- **Task 3: transform-data** (Notebook: `TransformData`, runs if `check-nulls` succeeds) or **validate-data** (Notebook: `ValidateData`, runs if `check-nulls` fails).

**DAG Visualization** (in the UI):
```
ingest-data → check-nulls → transform-data (if no nulls)
                     ↳ validate-data (if nulls)
```

**Bundle Configuration (databricks.yml)**:
```yaml
resources:
  jobs:
    revenue_processing_job:
      name: RevenueProcessingJob
      tasks:
        - task_key: ingest-data
          existing_cluster_id: <cluster-id>
          notebook_task:
            notebook_path: ./src/IngestData.ipynb
        - task_key: check-nulls
          depends_on:
            - task_key: ingest-data
          existing_cluster_id: <cluster-id>
          notebook_task:
            notebook_path: ./src/CheckNulls.ipynb
        - task_key: transform-data
          depends_on:
            - task_key: check-nulls
          condition_task:
            op: EQUAL
            left: "{{tasks.check-nulls.output}}"
            right: "no_nulls"
          existing_cluster_id: <cluster-id>
          notebook_task:
            notebook_path: ./src/TransformData.ipynb
        - task_key: validate-data
          depends_on:
            - task_key: check-nulls
          condition_task:
            op: NOT_EQUAL
            left: "{{tasks.check-nulls.output}}"
            right: "no_nulls"
          existing_cluster_id: <cluster-id>
          notebook_task:
            notebook_path: ./src/ValidateData.ipynb
```

- The `condition_task` defines conditional logic based on the output of `check-nulls`.
- Deploy and run this bundle as shown in Step 5.

### Best Practices
1. **Use Job Clusters**: Prefer job clusters over all-purpose clusters for cost efficiency, as they terminate after the job completes.
2. **Version Control**: Use Git integration to manage notebook code and ensure consistent job runs. Avoid workspace paths in production.
3. **Parameterize Jobs**: Pass parameters dynamically to make jobs reusable across different inputs.
4. **Monitor and Alert**: Set up notifications and monitor job performance using the UI, Spark UI, or REST API.
5. **Optimize Performance**:
   - Cache frequently used data to reduce computation time.
   - Partition data to distribute workloads evenly.
   - Tune Spark configurations based on workload needs.
6. **Use Asset Bundles for CI/CD**: Manage jobs programmatically with bundles for reproducibility and automation.
7. **Handle Errors Gracefully**: Implement error handling in notebooks and set up retry policies for transient failures.

### Limitations of Databricks Jobs
- **Cost Visibility**: The Jobs API doesn’t provide direct cost information. Use the Accounts API or cloud provider billing for cost tracking.
- **Limited Task Types**: Only specific task types (e.g., notebooks, pipelines) are supported. Advanced dependencies may require external orchestrators like Airflow.
- **Debugging Challenges**: The Jobs API provides limited debugging data. Reproduce failures with the same code, data, and parameters for diagnosis.

### Additional Resources
- **Databricks Documentation**:
  - [Create your first workflow with Lakeflow Jobs](https://docs.databricks.com/workflows/jobs/create-jobs.html)
  - [Develop a job with Databricks Asset Bundles](https://docs.databricks.com/workflows/asset-bundles/develop-jobs.html)
  - [Databricks Jobs API](https://docs.databricks.com/dev-tools/api/latest/jobs.html)
- **DataCamp Tutorials**:
  - [Introduction to Databricks](https://www.datacamp.com/courses/introduction-to-databricks)
  - [Complete Databricks Dolly Tutorial](https://www.datacamp.com/tutorial/complete-databricks-dolly-tutorial-for-building-applications)
- **Databricks SDK for Python**: [Documentation](https://databricks-sdk-py.readthedocs.io/en/latest/)
