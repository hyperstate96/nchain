# nchain
Repository for automated deploy of wordpress with automated image update 
Intention is to showcase auto updating of wordpress image version of minor release for example 6.3.0 to 6.3.1 or 6.3.x
Use case created on minikube version: v1.31.2


#Steps to spawn this setup with flux 


## Setting up FluxCD on the cluster.

### First install Flux 

Based on your operating system of choice:

https://fluxcd.io/flux/installation/

### Than bootstrap it 

This example is with two extra components of FluxCD, 
Repository with name: Nchain
Cluster name: nchain
and personal access key 

`flux bootstrap github   --components-extra=image-reflector-controller,image-automation-controller   --owner=hyperstate96   --repository=nchain   --branch=main   --path=clusters/nchain   --read-write-key   --personal`

This will configure FluxCD on the cluster with the before mentioned repository name. 

Important part of updating FluxCD configuration is to run: "flux reconcile kustomization flux-system --with-source" 
which updates the FluxCD configuration from beforementioned repository. 

## Creating the Helm chart for WordPress.

We simply use Bitnami's created Helm chart, but with our custom variables which are in folder "wordpress/values.yaml"

Command for installation is: 

`helm install wordpress -f values.yaml oci://registry-1.docker.io/bitnamicharts/wordpress`

In values.yaml version is pinned to 6.3.0 , so we can test the update to latest 6.3.1 version 


## Configuring automated image updates with FluxCD.

First we we fetch the repository https://github.com/hyperstate96/nchain.git
Than, we reference to "Setting up FluxCD on the cluster " in this README file.

Everything for FluxCD is prepared in "clusters/nchain" folder including "flux-system-automation" , "wordpress-policy" which is a policy instructing FluxCD how and when to update our wordpress instance. In "wordpress-registry.yaml" there is interval set for checking image of wordpress and checking the latest tag of it. 




## Any prerequisites or dependencies required for this setup.

Prerequisites are one Git repository of any kind 
Kubernetes version of any kind 
And proper access to Git repository with correct permissions 

