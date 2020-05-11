# jira-demo
This a demo of how to deploy JIRA on Azure AKS.

It provides a single-node Jira server using a Postgres DB server (both deployed as Statefulsets). 

## Overview

- This setup does not include ingress and is NOT production ready. Do not run Jira like this in production.

## What Do We Need?

- 1 namespace:  00-devops-namespace.yml
- Default Azure Storage Classes + 1 custom Storage Class for `var-lib-postgresql-data`. Custom storage class is required for Postgres as the default mount options for AzureDisk do not allow the Postgres user to write to the location.
- Persistent Volume Claims for `var-lib-postgresql-data` (pvc-postgres-data.yaml) `var-atlassian-application-data-jira` (pvc-jira-data.yaml) and `var-atlassian-application-data-jira-shared` (pvc-jira-shared.yaml) directories.
- 1 Secret for Postgres DB: secret-postgres.yml
- 1 StatefulSet for the Jira server: sts-jira.yml
- 1 StatefulSet for the PostgresSQL DB server: sts-postgres.yml
- 1 Headless Service for JIRA: svc-jira.yaml
- 1 Headless Service for Postgres: svc-postgres.yml
- 1 Load Balancer for JIRA Cluster:  svc-jira-cluster.yml
- 1 Load Balancer for Postgres Cluser svc-postgres-cluster.yaml



## Usage

### Create Namesapce 

If you wish to edit all your files to use a different namespace (default is "devops") then please note that Namespace must be created first to deploy the other configurations. Ensure namespace yaml filename is the first file that will be read when you run the kubectl apply -f command. e.g. add "00-" to beginning of yaml filename 


### Deploy JIRA to AKS Cluster

Ensure you are logged on to cluster:  az aks get-credentials --resource-group YourResourceGroup --name YourAKSCluster

git clone ssh://git@hkbitbucket.hkg.mizuho-sc.com:7999/as/jira-demo.git  >>> clone the jira-demo repo to your local machine

cd jira-demo  >>> Switch to the jira-demo folder on your local machine

kubectl apply -Rf . >>> This command will apply all of your YAML files in the current directory

kubectl -n devops get all  >>> This command will display all statefulsets, pods and services. You want to see all pods in a "running state" this may take several minutes while the images are downloaded etc.


### Configure Jira

Visit the External IP address of service/jira-cluster http://10.106.202.97:8080 (please note your ip address will be different)

At this point you follow the setup wizard to connect the application to the Postgres DB and a Trial License from Atlassian.
