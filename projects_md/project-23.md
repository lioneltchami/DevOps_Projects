# PERSISTING DATA IN KUBERNETES
#

In this project, will be introducing the concept of Persistence in kubernetes using Persistent Volumes, Persistent Volumes Claims and Storage Classes

- A **PersistentVolume (PV)** is a piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using Storage Classes. It is a resource in the cluster just like a node is a cluster resource. PVs are volume plugins like Volumes, but have a lifecycle independent of any individual Pod that uses the PV. This API object captures the details of the implementation of the storage, be that NFS, iSCSI, or a cloud-provider-specific storage system.

- A **PersistentVolumeClaim (PVC)** is a request for storage by a user. It is similar to a Pod. Pods consume node resources and PVCs consume PV resources. Pods can request specific levels of resources (CPU and Memory). Claims can request specific size and access modes (e.g., they can be mounted ReadWriteOnce, ReadOnlyMany or ReadWriteMany, see AccessModes).

- While PersistentVolumeClaims allow a user to consume abstract storage resources, it is common that users need PersistentVolumes with varying properties, such as performance, for different problems. Cluster administrators need to be able to offer a variety of PersistentVolumes that differ in more ways than size and access modes, without exposing users to the details of how those volumes are implemented. For these needs, there is the **StorageClass resource**.

## Prerequisites

- Setup an EKS Cluster on AWS
- Configure both cluster role and node group roles




### Running the deployments
![Screenshot from 2023-03-12 20-22-25](https://user-images.githubusercontent.com/23356682/224603611-223be0cf-708c-4409-b193-875ed675563e.png)
#

#

### Creating a Persistent Volume Claim

![Screenshot from 2023-03-12 20-22-07](https://user-images.githubusercontent.com/23356682/224603608-6e1943d8-97cd-4ab8-93ad-789353c13e13.png)
#



### Creating a configmap containing the default page as its userdata. This will be populated into created pods irrespective of whether it gets deleted or not.

![Screenshot from 2023-03-13 04-51-40](https://user-images.githubusercontent.com/23356682/224604455-1eaddcfb-1f02-4ff2-8d2c-5c2867052137.png)

### Creating a Deployment based off of the Configmap created

![Screenshot from 2023-03-13 04-51-45](https://user-images.githubusercontent.com/23356682/224604485-46b3495f-5cbf-49f2-bc24-06cde5d25c06.png)


### Changes made on the configmap userdata populated in each pod
![Screenshot from 2023-03-12 20-42-25](https://user-images.githubusercontent.com/23356682/224604592-603aa512-223c-4c50-8203-7baf12bbb364.png)

