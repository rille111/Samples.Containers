﻿# Install Helm 

Helm & Tiller is like a package manager for Kubernetes, that can install and configure stuff according to `charts`.
Charts can be seen as scripts & configuration steps.
Helm is the client component talking to Tiller that is installed in the cluster.
UPDATE !!! Tiller is removed from Helm!! See https://helm.sh/docs/faq/#removal-of-tiller

This guide assumes you have your cluster RBAC enabled, and *DO NOT* use Helm/Tiller in TLS mode.

There might be some cloud provider specific setup, 
 * see details: https://helm.sh/docs/using_helm/
 * also google for your provider and helm, like `aks helm installation` for Azure, that will lead you to https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/aks/kubernetes-helm.md

### Installation

* First install local CLI
	* run `choco install kubernetes-helm`
* Then initiate with `helm init`
* You should see something like 
	* 'Tiller (the Helm server-side component) has been installed into your Kubernetes Cluster.' 
	* And a notification where the Helm local folder is, like $HELM_HOME has been configured at C:\Users\XXX\.helm

* That's it! Now you can use Helm to install stuff
