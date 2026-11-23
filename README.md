Overview
========

Welcome to Astronomer! This project was generated to replicate 'astro dev init' using the Astronomer CLI. This readme describes the contents of the project, as well as how to run Apache Airflow on your local machine.

> **Multi-bundle demo context:** this project is the **main** DAG bundle in a 3-bundle Astro Multi-Bundle Support demo. It owns the deployment's image (Dockerfile, `requirements.txt`, `include/`). The other two bundles are plain DAG folders, each in its own repo, deployed independently onto the same deployment via `astro deploy --dags --dags-path ... --dag-bundle-name ...`:
>
> | Repo | Bundle | Contents |
> |------|--------|----------|
> | [multi-dag-bundle-main](https://github.com/astronomer/multi-dag-bundle-main) | `main` (implicit) | Full Astro project: image + `example_astronauts` DAG |
> | [multi-dag-bundle-python-ops](https://github.com/astronomer/multi-dag-bundle-python-ops) | `mdb-python-ops` | PythonOperator DAGs + `python_ops_sql` non-DAG bundle |
> | [multi-dag-bundle-bash-ops](https://github.com/astronomer/multi-dag-bundle-bash-ops) | `mdb-bash-ops` | BashOperator DAGs |
>
> Bundles must be created on the deployment (Astro UI, `astro deployment bundle create`, or the Astro API) before the first deploy into them. DAG IDs must be unique across all bundles on a deployment.

Project Contents
================

Your Astro project contains the following files and folders:

- dags: This folder contains the Python files for your Airflow DAGs. By default, this directory includes one example DAG:
    - `example_astronauts`: This DAG shows a simple ETL pipeline example that queries the list of astronauts currently in space from the Open Notify API and prints a statement for each astronaut. The DAG uses the TaskFlow API to define tasks in Python, and dynamic task mapping to dynamically print a statement for each astronaut. For more on how this DAG works, see our [Getting started tutorial](https://www.astronomer.io/docs/learn/get-started-with-airflow).
- Dockerfile: This file contains a versioned Astro Runtime Docker image that provides a differentiated Airflow experience. If you want to execute other commands or overrides at runtime, specify them here.
- include: This folder contains any additional files that you want to include as part of your project. It is empty by default.
- packages.txt: Install OS-level packages needed for your project by adding them to this file. It is empty by default.
- requirements.txt: Install Python packages needed for your project by adding them to this file. It is empty by default.
- plugins: Add custom or community plugins for your project to this file. It is empty by default.
- airflow_settings.yaml: Use this local-only file to specify Airflow Connections, Variables, and Pools instead of entering them in the Airflow UI as you develop DAGs in this project.

Deploy Your Project Locally
===========================

Start Airflow on your local machine by running 'astro dev start'.

This command will spin up five Docker containers on your machine, each for a different Airflow component:

- Postgres: Airflow's Metadata Database
- Scheduler: The Airflow component responsible for monitoring and triggering tasks
- DAG Processor: The Airflow component responsible for parsing DAGs
- API Server: The Airflow component responsible for serving the Airflow UI and API
- Triggerer: The Airflow component responsible for triggering deferred tasks

When all five containers are ready the command will open the browser to the Airflow UI at http://localhost:8080/. You should also be able to access your Postgres Database at 'localhost:5432/postgres' with username 'postgres' and password 'postgres'.

Note: If you already have either of the above ports allocated, you can either [stop your existing Docker containers or change the port](https://www.astronomer.io/docs/astro/cli/troubleshoot-locally#ports-are-not-available-for-my-local-airflow-webserver).

Deploy Your Project to Astronomer
=================================

If you have an Astronomer account, pushing code to a Deployment on Astronomer is simple. For deploying instructions, refer to Astronomer documentation: https://www.astronomer.io/docs/astro/deploy-code/

For this bundle specifically (the implicit `main` bundle), deploy DAGs only with:

    astro deploy <deployment-id> --dags

CI/CD
=====

Pushes to `main` deploy automatically via `.github/workflows/deploy.yml`
(GitHub Actions), using `astronomer/setup-astro-cli` (CLI pinned to 1.47.0) +
`astronomer/deploy-action@v0.17.0` against the shared deployment.
`deploy-type` is left as `infer`, so the workflow runs a full image+dags
deploy when non-DAG files change (Dockerfile, requirements.txt, etc.) and a
DAG-only deploy when only `dags/` changes. Markdown-only pushes are ignored,
and a manual run (Actions > Run workflow) does a full image+dags deploy.
Deploys here only touch the `main` bundle; the named bundles keep their own
versions. Requires an `ASTRO_API_TOKEN` repo secret and an
`ASTRO_DEPLOYMENT_ID` repo variable.

Contact
=======

The Astronomer CLI is maintained with love by the Astronomer team. To report a bug or suggest a change, reach out to our support.
