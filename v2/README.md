# Crossplane v2

## Pre-requisites

Create a Kubernetes cluster, e.g. with `kind`:
```
kind create cluster
```

Install Crossplane from the `master` release channel, e.g.:
```
helm repo add crossplane-master https://charts.crossplane.io/master/
helm repo update
helm install crossplane --namespace crossplane-system --create-namespace crossplane-master/crossplane --devel
```

Install the AWS provider:
```
kubectl apply -f provider.yaml
```

Wait for the AWS providers to become installed and healthy:
```
kubectl get pkg
```

Create credentials for the AWS provider to create resources in your AWS account:
```
AWS_PROFILE=default && echo -e "[default]\naws_access_key_id = $(aws configure get aws_access_key_id --profile $AWS_PROFILE)\naws_secret_access_key = $(aws configure get aws_secret_access_key --profile $AWS_PROFILE)" > aws-creds.txt
kubectl create secret generic aws-creds -n crossplane-system --from-file=credentials=./aws-creds.txt
kubectl apply -f provider-config-default.yaml
```

## Setup

Define your namespaced `Network` abstraction by creating a `CompositeResourceDefinition`
(XRD) that defines the API and a `Composition` that defines the implementation:
```
kubectl apply -f xrd.yaml
kubectl apply -f composition.yaml
```

Install the functions that the composition pipeline will run:
```
kubectl apply -f functions.yaml
```

Wait for the functions to become installed and healthy:
```
kubectl get pkg
```

Create a namespace for your resources:
```
kubectl create namespace xp-v2
```

Create some namespaced `Network` XRs, that will kick off the
composition of resources using the KCL function:
```
kubectl apply -f xr-iteration.yaml
kubectl apply -f xr-conditional.yaml
```

Examine the resources that were created under the `Network` abstraction by the
compositions:
```
crossplane beta trace -n xp-v2 network.v2.xp-layers.crossplane.io/network-iteration
crossplane beta trace -n xp-v2 network.v2.xp-layers.crossplane.io/network-conditional
```

We can see v2 namespaced composite resources composing namespaced managed resources!

## Clean-up

Clean up all the created resources:
```
kubectl delete -f xr-iteration.yaml
kubectl delete -f xr-conditional.yaml
kubectl get managed
```