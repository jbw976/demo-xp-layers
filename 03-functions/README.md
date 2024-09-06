# Functions

## Pre-requisites

Create the Kubernetes cluster with Crossplane installed and the AWS provider
configured as described in the main [README
Pre-Requisites](../README.md#pre-requisites).

## Setup

```
kubectl apply -f functions.yaml
```

Wait for the functions to become installed and healthy:
```
kubectl get pkg
```

Apply the functions based composition:
```
kubectl apply -f composition.yaml
```

Create a claim for this `Network` abstraction, that will kick off the
composition of resources using the KCL function:
```
kubectl apply -f claim-iteration.yaml
kubectl apply -f claim-conditional.yaml
```

Examine the resources that were created under the `Network` abstraction by the
compositions:
```
crossplane beta trace network.xp-layers.crossplane.io/network-iteration
crossplane beta trace network.xp-layers.crossplane.io/network-conditional
```

We see clearly the power of functions and enabling higher level languages (like
KCL) to perform more complicated logic, such as conditionals, iteration, flow
control, etc. In this example, `network-iteration` creates a variable number of
network resources on its own, and `network-conditional` conditionally skips the
creation of the `InternetGateway` resources. This type of logic in compositions
was not possible before functions.

## Clean-up

Clean up all the created resources:
```
kubectl delete -f claim-iteration.yaml
kubectl delete -f claim-conditional.yaml
kubectl get managed
```