Following https://sdk.operatorframework.io/docs/building-operators/helm/tutorial/

Setup
```
$ operator-sdk version
operator-sdk version: "v1.13.1", commit: "1659ab58a073999463e6dcdf18bdc359a315e091", kubernetes version: "1.21", go version: "go1.16.8", GOOS: "linux", GOARCH: "amd64"

$ kind version
kind v0.11.1 go1.16.4 linux/amd64
```

Project creation from https://github.com/open-policy-agent/gatekeeper/tree/master/charts/gatekeeper
```
operator-sdk init --domain gatekeeper.sh --plugins helm --helm-chart ../gatekeeper/charts/gatekeeper
make docker-build docker-push IMAGE_TAG_BASE=quay.io/thomasmckay/gatekeeper-helm-operator
kind create cluster
operator-sdk olm install
make bundle bundle-build bundle-push IMAGE_TAG_BASE=quay.io/thomasmckay/gatekeeper-helm-operator
operator-sdk run bundle quay.io/thomasmckay/gatekeeper-helm-operator-bundle:v0.0.1
kubectl apply -f config/samples/charts_v1alpha1_gatekeeper.yaml
```

Errors
```
kubectl logs gatekeeper-helm-operator-controller-manager-7448cb48b4-k44sn -c manager

{"level":"error","ts":1653399508.0942523,"logger":"controller.gatekeeper-controller","msg":"Reconciler error","name":"gatekeeper-sample","namespace":"default","error":"failed to install release: failed to install CRD crds/assign-customresourcedefinition.yaml: customresourcedefinitions.apiextensions.k8s.io is forbidden: User \"system:serviceaccount:default:gatekeeper-helm-operator-controller-manager\" cannot create resource \"customresourcedefinitions\" in API group \"apiextensions.k8s.io\" at the cluster scope","stacktrace":"sigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller).Start.func2.2\n\t/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.10.0/pkg/internal/controller/controller.go:227"}
```
