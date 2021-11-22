# Consuming SAP BTP Services From Any Hyperscaler

## Description:
Deploy an SAP Fiori Application in any a hyperscaler, such as Google Cloud Platform (GCP), Amazon Web Services (AWS), Alibaba Cloud or Azure and use services from SAP BTP.

This application showcases:
 - How to consume Services running on SAP BTP from other environments e.g K8S clusters managed by hyperscalers
 - How to make use of SAP BTP API’s , Service Manager and Service Operator to consume services from different environment
 
 
## Business Scenario

Business Scenario to showcase how to deploy and simple fiori Master detail application in a hyperscaler, such as Google Cloud Platform (GCP), Amazon Web Services (AWS), Alibaba Cloud or Azure, and still take advantage of the services provided by SAP Business Technology Platform (SAP BTP).

In this tutorial, we use the simple fioriapp(Master Detail) application to learn some best practices when setting up your Kubernetes cluster to communicate with SAP BTP so you can use the Destination and the SAP Authorization & Trust Management services.

Build an application on hyperscaler using SAP BTP services.
As an employee of  Business Partner Validation Firm <XYZ>, which is a third party vendor of <ABC> Corporation, John would like to know the recently added business partner in SAP Backend system to review. 
 - Application Build on Hyperscaler
  
      As an Employee of <XYZ> Corporation, sees Newly added business partner in application

## Architecture

![solution diagram](./k8s/images/hyperscalar.png)

# SAP Business Technology Platform (SAP BTP) Service Operator for Kubernetes

With the SAP BTP service operator, you can consume [SAP BTP services](https://platformx-d8bd51250.dispatcher.us2.hana.ondemand.com/protected/index.html#/viewServices?) from your Kubernetes cluster using Kubernetes-native tools. 
SAP BTP service operator allows you to provision and manage service instances and service bindings of SAP BTP services so that your Kubernetes-native applications can access and use needed services from the cluster.  
The SAP BTP service operator is based on the [Kubernetes Operator pattern](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/).


## Prerequisites
- SAP BTP [Global Account](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/d61c2819034b48e68145c45c36acba6e.html) and [Subaccount](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/55d0b6d8b96846b8ae93b85194df0944.html) 
- Service Management Control (SMCTL) command line interface. See [Using the SMCTL](https://help.sap.com/viewer/09cc82baadc542a688176dce601398de/Cloud/en-US/0107f3f8c1954a4e96802f556fc807e3.html).
- [Kubernetes cluster](https://kubernetes.io/) running version 1.17 or higher 
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) v1.17 or higher
- [helm](https://helm.sh/) v3.0 or higher
- Follow the steps to setup the [Consuming SAP BTP Services in Kubernetes with SAP BTP Service Operator](https://help.sap.com/viewer/09cc82baadc542a688176dce601398de/Cloud/en-US/b5a35bfa87b5444080e6e6e6d361fa20.html)
- You have an [account on the SAP Gateway Demo System (ES5)](https://developers.sap.com/tutorials/gateway-demo-signup.html) created
- You have [connected SAP BTP to your SAP Gateway Demo System (ES5) account](https://developers.sap.com/tutorials/cp-portal-cloud-foundry-gateway-connection.html)
- You have a Kubernetes cluster and have downloaded the Kubeconfig file from any hyperscaler:
     - [Google Cloud Platform ](https://cloud.google.com/kubernetes-engine/docs/how-to/cluster-access-for-kubectl#kubeconfig)
     - [Amazon EKS](https://docs.aws.amazon.com/eks/latest/userguide/create-kubeconfig.html)
     - [Azure](https://docs.microsoft.com/bs-latn-ba/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)
     - [Alibaba Cloud](https://www.alibabacloud.com/help/doc-detail/86378.htm?spm=a2c63.p38356.b99.643.2b046485uwVkU7)

## Login to SAP Service Manager to Verify Cross consumable services from SAP Business Technology Platform (SAP BTP)

1. Assign Subaccount Service Administrator Role Collection to your user/
 
   Assign the Subaccount Service Administrator Collection, see the step 2 of the Setup section of [The procedure shows how to assign new service administrator
   role collections to a user of your choice](https://help.sap.com/viewer/09cc82baadc542a688176dce601398de/Cloud/en-US/0735965d10b342a393b5a83924dba9b4.html).*
  
2.  Execute the following command from your Service Manager Control (SMCTL) command-line interface:

    `smctl login -a https://service-manager.cfapps.<region domain> --param subdomain=<subdomain name> -u <user.email> -p <password>`
 
    To find the region domain for the SAP Service Manager, see Regions and API Endpoints Available for the Cloud Foundry Environment
    The subdomain is the subaccount subdomain, in which you would like to register the broker. You can find it in the cloud cockpit Overview tab of your subaccount.
 
    To find the region domain for the SAP Service Manager, see [Regions and API Endpoints Available for the Cloud Foundry Environment](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/350356d1dc314d3199dca15bd2ab9b0e.html#loiof344a57233d34199b2123b9620d0bb41)


3. Check the cross consumable services by entering the following command:

    `smctl marketplace`
    
     ![crossconsumableservices](./k8s/images/crossconsumption.png)

    >Note: Above image is subject to the entitlements assigned to your subaccount.
 
## Create a service instance and service binding
 
1.  In the **fioriapp** application, you use the Destination and the SAP Authorization & Trust Management services and consume them using the SAP BTP service
    operator. To use these SAP BTP services you have to create the respective service instances and service bindings. Use the `createservice.yaml` which will
    create the *destination* service instance with plan *lite* and the *xsuaa* service instance with plan *application*.

2. Create your own namespace.

    ```bash
    
    kubectl create namespace sapfiori
    
    ```

3.  Apply the resource file in your cluster to create the instance.

    ```bash
    kubectl apply -f https://github.com/SAP-samples/FioriApp-Hyperscaler/blob/master/k8s/createservice.yaml -n sapfiori
    ```

4.  Check that the status of the service in your cluster is **Created**.

    ```shell/bash
    kubectl get serviceinstances -n sapfiori
    
    |NAME|OFFERING|PLAN|STATUS|READY|AGE|
    |-------|----|-------|--------|---------|---------|
    |`destination-instance-fiori`| `destination`|`lite`|`Created`|`True`|`##`|
    |`xsuaa-instance-fiori`| `xsuaa`|`application`|`Created`|`True`|`##`|
    
    ```
    
    To know more see [SAP BTP Service Operator](https://github.com/SAP/sap-btp-service-operator)
 
 
 ## Deploy the application into Kubernetes Cluster
 
 1. To deploy the **fioriapp** application in your cluster, you have to create a pod, which encapsulates the container and ensures that a specified quorum of running instances is fulfilled. To do that, use the `deployment.yaml`.
 
 2. Apply the resource file in your cluster to deploy the application.

    ```bash
    kubectl apply -f https://github.com/SAP-samples/FioriApp-Hyperscaler/blob/master/k8s/deployment.yaml -n sapfiori
    ```
 
3. Check that the status of the deployed application in your cluster is **Running**.

    ```Shell/Bash
    kubectl get pods -n sapfiori

    |NAME|READY|STATUS|RESTART|AGE|
    |-------|----|-------|--------|---------|
    |`fioriapp-##-##`| `1/1`|`Running`|`##`|`##`|
    
    ```

## Expose the deployed application using load balancer

1. Kubernetes provides a convenient way to expose applications. With **--type=LoadBalancer** you request to provision a public IP address. It will also automatically assign a cluster-IP and a Node Port in the current setup of the cluster.

2. Apply the resource file in your cluster to deploy the application.

    ```bash
    kubectl apply -f https://github.com/SAP-samples/FioriApp-Hyperscaler/blob/master/k8s/loadbalancer.yaml -n sapfiori
    ```

3. To find the deployed load balancer IP, use the following command:

    ```Shell/Bash
    kubectl get services -n sapfiori
    ```
4. Copy the load balancer IP from the output and run in a browser with port *5000*
    
    ![finalimage](./k8s/images/businesspartner.png)

## Known Issues

## How to obtain support

[Create an issue](https://github.com/SAP-samples/btp-hyperscaler-extension/issues) in this repository if you find a bug or have questions about the content.
 
For additional support, [ask a question in SAP Community](https://answers.sap.com/questions/ask.html).

## Contributing

## License
Copyright (c) 2021 SAP SE or an SAP affiliate company. All rights reserved. This project is licensed under the Apache Software License, version 2.0 except as noted otherwise in the [LICENSE](LICENSES/Apache-2.0.txt) file.
