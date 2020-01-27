# Spring Boot on Kubernetes connecting to MySQL

This repository contains code for a spring boot application that uses MySQL as the database. The repository uses github actions to build the image, push the image to docker hub, deploy mysql in HA on a Kubernetes cluster and deploy the spring boot application on the same cluster using helm charts.

## Prerequisites

* Running Kubernetes cluster(for this assignment, I used EKS created with Terraform code [here](https://github.com/bhvishal9/eks-cluster-terraform))

## Steps

### Preparing the kubeconfig

1. Once the cluster is created or you have a running cluster, apply the [manifest file](kubernetes/so1_k8s.yaml) which will create:
    * A namespace
    * A service account
    * A rolebinding with admin access on the namespace

2. Get the token from the secret associated with the service account secret, an example script can be taken from [here](https://github.com/DevOps-with-Kubernetes/examples/blob/master/chapter7/get-sa-token.sh).

3. After getting the token use the kubeconfig [template file](kubernetes/kubeconfig.tmpl) to create the working kubeconfig, if you are using the terraform project, the kubeconfig file will get generated in the root folder which you can use as base kubeconfig.

4. Put kubeconfig in the github secret environment variable "KUBE_CONFIG_DATA".

After setting up the kubeconfig, we can deploy the application on kubernetes with the Github CI. The [CI file](.github/workflows/main.yml) has three steps:

1. Compile the code, build the docker image and push it to docker hub
2. Deploy MySQL in HA using the upstream helm chart
3. Deploy Spring application using the [helm chart](kubernetes/spring-app), the chart creates a deployment with multiple replicas and required probes to restart pod in case of failures. The chart also creates a service of type LoadBalancer to expose the application to outside.

# Task 1 - Creating nagios check/script to run check on ES

For this task I prepared a [GO script](main.go) to run against a ES cluster, the script looks for required string in an index and returns the number of hits as well as the actual results. A sample output is shown below:

```
> go run main.go
Number of hits: 2
 * ID=NwuO428BlefU_3BVFiF9, map[text:Handbill not printed]
 * ID=OAuS428BlefU_3BV7iGE, map[text:Handbill not printed due to XYZ]
 ```
