
# Kubernetes Sample Application Deployment

This project demonstrates the deployment of a sample NGINX application on Amazon EKS (Elastic Kubernetes Service) using various Kubernetes resources.

## Project Overview

The project deploys a sample NGINX application with the following Kubernetes resources:

-   Namespace
-   ConfigMap
-   Secret
-   PersistentVolume (PV)
-   PersistentVolumeClaim (PVC)
-   Deployment
-   Service (LoadBalancer type)
-   Ingress

## Prerequisites

-   AWS CLI configured with appropriate permissions
-   kubectl installed and configured to work with your EKS cluster
-   An existing EKS cluster

## Deployment Steps

1.  **Create and apply the Kubernetes resources:**

`kubectl apply -k .` 

This command applies all the resources defined in your kustomization file.

2.  **Verify the PersistentVolumeClaim is bound:**

`kubectl get pvc -n sample-app` 

Example output:

`NAME     STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE app-pvc  Bound    app-pv   100Mi      RWO            standard                               4m4s` 

3.  **Delete existing pods to force recreation with new configurations:**

`kubectl delete pods --all -n sample-app` 

4.  **Verify the new pods are running:**

`kubectl get pods -n sample-app` 

Example output:

`NAME                          READY   STATUS    RESTARTS   AGE' 
'sample-app-7fcdbf6789-64j4j 1/1     Running 0 7s sample-app-7fcdbf6789-fxqvd 1/1     Running 0 7s` 

5.  **Describe a pod to check its details:**

`kubectl describe pod sample-app-7fcdbf6789-64j4j -n sample-app` 

Example output:

`Name:             sample-app-7fcdbf6789-64j4j Namespace:        sample-app Status:           Running IP:               172.34.1.77 Containers:
 sample-app: Image:          nginx:latest State:          Running Ports:          80/TCP` 

6.  **Check node details:**

`kubectl describe nodes` 

Example output:

`Name:               ip-172-34-1-169.us-west-2.compute.internal Roles:              <none> Labels:             beta.kubernetes.io/arch=amd64
 beta.kubernetes.io/instance-type=t3.medium Status:             Ready Capacity:
 cpu:              2 memory:           3943308Ki` 

## Accessing the Application

To access the application:

1.  Get the LoadBalancer service external IP:

`kubectl get service sample-app-service -n sample-app` 

Example output:

`NAME                 TYPE           CLUSTER-IP       EXTERNAL-IP                                                               PORT(S) AGE sample-app-service   LoadBalancer 10.100.106.250   a9066345be9b64592881fec714a51357-1416695589.us-west-2.elb.amazonaws.com 80:30158/TCP   13m` 

2.  Use the EXTERNAL-IP to access the application:

`curl http://<EXTERNAL-IP>` 

Replace `<EXTERNAL-IP>` with the actual IP address or DNS name provided by the LoadBalancer.

## Troubleshooting

-   If pods are in a pending state, check node resources and PV/PVC bindings.
-   For 403 Forbidden errors, ensure the NGINX configuration is correct and serving the intended content.
-   Check pod logs for any application-specific issues:

`kubectl logs <pod-name> -n sample-app` 

## Resource Limits

To avoid resource starvation, add resource requests and limits to your deployment:

`resources:  requests: memory: "64Mi" cpu: "250m" limits: memory: "128Mi" cpu: "500m"` 

Apply the updated deployment:

`kubectl apply -f deployment.yaml` 

## Clean Up

To delete all resources created by this project:

`kubectl delete namespace sample-app` 


