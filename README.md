# pipelines-operator

[WIP]

TBD:

* add parameters to specify the namespace, version etc
* download `ks` and `kubectl` automatically
* allow to specify the KUBECONFIG
* fix the service account and role binding
* figure out how to show the logs while the ansible playbook is running

To run the Pipeline Operator:

1. install operator-sdk
2. clone this repo
3. have a docker registry like `hub.docker.io`
4. build and push the image
```command line
operator-sdk build ffdlops/kfp:v0.0.1
docker push ffdlops/kfp:v0.0.1
```
5. modify `deploy/operator.yaml` to use the image built above
6. deploy the operator
```command line
kubectl create -f deploy/crds/pipelines_v1alpha1_pipelines_crd.yaml
kubectl create -f deploy/service_account.yaml
kubectl create -f deploy/role.yaml
kubectl create -f deploy/role_binding.yaml
kubectl create -f deploy/operator.yaml
```
7. create the customer resource to deploy Kubeflow Pipelines
```command line
kubectl create -f deploy/crds/pipelines_v1alpha1_pipelines_cr.yaml
```

Once this completes, query the services and run following commands to expose the Pipelines UI.

```command line
export NAMESPACE=default
kubectl port-forward -n ${NAMESPACE} $(kubectl get pods -n ${NAMESPACE} --selector=service=ambassador -o jsonpath='{.items[0].metadata.name}') 8080:80 --address 0.0.0.0&
```

Run `http://<hostname>:8080/pipeline` to load the UI.