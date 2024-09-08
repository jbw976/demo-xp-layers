# Full Code

This demo shows how to write a Crossplane composition function using a general
purpose programming language, specifically `go`. This is the far end of the "no
code" to "full code" spectrum that Crossplane supports.

The full code for this demo is available in the following GitHub repository:
https://github.com/jbw976/demo-xfn-network

## Pre-requisites

Create the Kubernetes cluster with Crossplane installed and the AWS provider
configured as described in the main [README
Pre-Requisites](../README.md#pre-requisites).

## Function Development

In the https://github.com/jbw976/demo-xfn-network repo, we can test the full
functionality of our function by running it locally and then using the `render`
command:
```
go run . --insecure --debug
```

In another tab:
```
cd examples
crossplane render xr.yaml composition.yaml functions.yaml -x -r
```

Run unit tests to check functionality and verify our code has not regressed:
```
go test -v -cover .
```

Build a cross platform Crossplane package and push to the Upbound marketplace:
```
docker build . --quiet --platform=linux/amd64 --tag runtime-amd64
docker build . --quiet --platform=linux/arm64 --tag runtime-arm64
crossplane xpkg build --package-root=package --embed-runtime-image=runtime-amd64 --package-file=function-amd64.xpkg
crossplane xpkg build --package-root=package --embed-runtime-image=runtime-arm64 --package-file=function-arm64.xpkg
crossplane xpkg push --package-files=function-amd64.xpkg,function-arm64.xpkg jaredorg/demo-xfn-network:v0.0.1
```

## Real World Usage

Install our custom full code function:
```
kubectl apply -f functions.yaml
```

We can see the new `demo-xfn-network` function installed and healthy:
```
kubectl get pkg
```

Apply a new composition, for our same underlying `Network` abstraction, that
will use the custom full code function:
```
kubectl apply -f composition.yaml
```

Create a claim just like we did in the last demo, that will initiate the
creation of the `Network` resources:
```
kubectl apply -f claim-code.yaml
```

The composition function pipeline will execute, our custom full code function
will be called, and we should see the `Network` resources provisioning and start
to become ready and healthy:
```
crossplane beta trace network.xp-layers.crossplane.io/network-code
```

The end result is the same as the previous demo, where we end up with a variable
number of `VPCs` and conditionally created `InternetGateways`. However, the
heavy lifting this time was performed by a full code function written in golang
that we have the most amount of flexibility and expressiveness with, and all our
native language tooling to help us write great code and verify its correctness.

## Clean-up

Clean up all the created resources:
```
kubectl delete -f claim-code.yaml
kubectl get managed
```