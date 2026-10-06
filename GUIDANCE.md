# Dagster on Wodby

What Wodby sets up for Dagster on this service. It is deployed with the official Dagster Helm chart and runs two workloads: the webserver (`webserver`, port 80, the service's endpoint) and the daemon (`daemon`).

## Linked services

The PostgreSQL link is required. Its host, database, user and password are passed to the chart, which stores runs, events and schedules there. The chart's own PostgreSQL is disabled. No variables need to be set for the database.

## Code locations

The service deploys no user code. The webserver reads its workspace from the config file "Workspace" (`workspace.yaml`), which is empty by default (`load_from: []`). Code locations are added to that file, for example as gRPC servers that run elsewhere in the environment; the chart's own user code deployments are not created.

## Runs

- Runs are queued by the daemon (`QueuedRunCoordinator`) and launched as Kubernetes jobs (`K8sRunLauncher`), not inside the webserver or daemon containers.
- The action "Wipe all run history" executes `dagster run wipe --force`, which deletes all run history and event logs and cannot be undone.

## Changing configuration

Edit the "Workspace" config file or add environment variables on the service, then deploy it. The service has no volume: state is in PostgreSQL.
