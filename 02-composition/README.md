# Composition

## Pre-requisites

Create the Kubernetes cluster with Crossplane installed and the AWS provider
configured as described in the main [README
Pre-Requisites](../README.md#pre-requisites).

## Setup

Define your `Network` abstraction by creating a `CompositeResourceDefinition`
(XRD) that defines the API and a `Composition` that defines the implementation:
```
kubectl apply -f xrd.yaml
kubectl apply -f composition.yaml
```

We can see that the control plane API has been extended with the new `Network`
abstraction:
```
kubectl get xrd
```

Create a couple claims for this `Network` abstraction:
```
kubectl apply -f claim-0.yaml
kubectl apply -f claim-1.yaml
```

Examine the resources that were created under the `Network` abstraction by the
compositions, and note the hierarchy of the resources (claim -> composite
resource -> composed resources):
```
crossplane beta trace network.xp-layers.crossplane.io/network-comp-0
crossplane beta trace network.xp-layers.crossplane.io/network-comp-1
```

Once again, we can see that the `InternetGateway` was able to find its VPC via a
selector, but this time we programmatically patched this selector with the label
of its matching VPC:
```
kubectl get internetgateway.ec2.aws.upbound.io/gateway-comp-0 -o json | jq '.spec.forProvider.vpcIdSelector,.spec.forProvider.vpcId'
```

## Clean-up

Clean up all the created resources:
```
kubectl delete -f claim-0.yaml
kubectl delete -f claim-1.yaml
kubectl get managed
```