# Elevate Your Infrastructure: Exploring Crossplane's Full Potential

 Crossplane ( https://www.crossplane.io/) and its user experience has matured
 greatly over the years and there are now numerous layers you can interact with
 while designing and building your internal developer platform powered by
 Crossplane.
 
 Should you directly declare the cloud resources you want Crossplane
 to create, should you create developer friendly simplified abstractions on top,
 should you stick with YAML or use a more full featured high level programming
 language?
 
 We will explore each of these layers in further detail through live
 demos to understand and efficiently harness the capabilities and experience of
 each. This will be a broad tour through the possibilities offered by
 Crossplane, all of which lead to a reliable and robust control plane to manage
 everything in the cloud that your organization could need!

## Layers of Crossplane

1. [Managed Resources](./01-managed-resources/)
1. [Composition](./02-composition/)
1. [Functions](./03-functions/)
1. [Full Code](./04-full-code/)

## Pre-Requisites

Create a Kubernetes cluster, e.g. with `kind`:
```
kind create cluster
```

Install Crossplane from the `stable` release channel, e.g.:
```
helm repo add crossplane-stable https://charts.crossplane.io/stable
helm repo update
helm install crossplane --namespace crossplane-system --create-namespace crossplane-stable/crossplane
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