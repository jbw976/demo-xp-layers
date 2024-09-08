# Managed Resources

## Pre-requisites

Create the Kubernetes cluster with Crossplane installed and the AWS provider
configured as described in the main [README
Pre-Requisites](../README.md#pre-requisites).

## Setup 
Create the VPCs and Internet Gateways:
```
kubectl apply -f vpc-0.yaml
kubectl apply -f vpc-1.yaml
kubectl apply -f internet-gateway-0.yaml
kubectl apply -f internet-gateway-1.yaml
```

Get an overview of the created VPCs and Internet Gateways:
```
kubectl get vpc,internetgateway
```

Get the full details of one of the internet gateways, paying attention to its
VPC selectors and resolved references:
```
kubectl get internetgateway.ec2.aws.upbound.io/gateway-mr-0 -oyaml
kubectl get internetgateway.ec2.aws.upbound.io/gateway-mr-0 -o json | jq '.spec.forProvider.vpcIdSelector,.spec.forProvider.vpcId'
```

Examine the events and status that tells the story of its provisioning and
health:
```
kubectl describe internetgateway.ec2.aws.upbound.io/gateway-mr-0
```

## Clean-up

Clean up all the created resources:
```
kubectl delete -f internet-gateway-0.yaml --wait=false
kubectl delete -f internet-gateway-1.yaml --wait=false
kubectl delete -f vpc-0.yaml --wait=false
kubectl delete -f vpc-1.yaml --wait=false
kubectl get managed
```