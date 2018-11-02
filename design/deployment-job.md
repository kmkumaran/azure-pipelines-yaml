# Deployment Job steps

Status: PM spec

Users would like to have ability to push application updates into production environments and be able to trace the deployments to such environments. 

As part of Azure DevOps pipeline, users would want to create and consume environments that represents their physical environments so that they can 
- track deployments against the environment
- build traceability from code to the physical deployment targets
- increase visibility of resource health/availability

## Terminologies

This document will introduce and refer to following terms "Deployment", "Service Group" and "Environment". 
- Deployment: "Deployment" will be a new job type. Only the "deployment: job will recognize "environment". "deployment" job will support strategies like "rolling", "blue-green" and "canary" 
- Service Group: Service Group will refer to a group of resources (targets) against which user would like to deploy and track deployment traceability. Service Group (SG) will support different types of targets like Containers or VM types or WebApps. Example: A Service group can map to a cluster namespace hosting a set of Web tier instances within Kubernetes. 
- Environment: Environment is a collection of different types of Service groups. This is a container for heterogeneous physical environment resources. Example: ProductionScaleUnit which contains Web-tier, middle-tier and database-tier all running in different types of service groups say in Kubernetes Cluster, VMs and in Azure respectively.


## Future
- "strategy" support for "deployment" jobs. This document does not cover how to support "strategy" in Yaml.
- While the plan is to support creation of environment through Yaml, the support to create servicegroups from Yaml will be staged. So this document does not cover inputs required to dynamically create servicegroups.


## YAML syntax

## yaml with deployment key literal. This will indicate the job type will be deployment type.
```jobs:
 deployment: deployWebPkg
 
 pool:
  vmImage: 'Ubuntu 16.04'
 ```
 

## yaml with environment key literal. Tracks deployment against the specified environment.
```jobs:
 
 deployment: deployWebPkg
 
 pool:
  vmImage: 'Ubuntu 16.04'
 
 environment:
   name: "myProductionEnvironment"

 steps:
 - task: deployPkg
   inputs:
     appServiceName: “myWebApp”
     slot: staging
```

## yaml with servicegroup key literal. Tracks deployment against the specified servicegroup within the environment.
```jobs:

 deployment: deployWebPkg

 pool:
  vmImage: 'Ubuntu 16.04'

 environment:
   name: "myProductionEnvironment"
   servicegroup:
     name:"myWebTier"

 steps:
 - task: deployPkg
   inputs:
     appServiceName: “myWebApp”
     connection: "myAzureSubscription"
     slot: staging
```

## environment and servicegroup variables available for step inputs
```jobs:

 deployment: deployWebPkg

 pool:
  vmImage: 'Ubuntu 16.04'

 environment:
   name: "myProductionEnvironment"
   servicegroup:
     name:"myWebTier"

 steps:
 - task: deployPkg
   inputs:
     appServiceName: $(environment.servicegroup)
     connection: $(environment.servicegroup.connection)
     slot: staging
```
