# pipelines-operator

### [WIP]

This operator is an application to help deploy the minimal [`Kubeflow`](https://www.kubeflow.org/) components to run Pipelines.

TBD:

* figure out how to show the logs while the ansible playbook is running
* test on `minishift` and then `IKS`.

To run the Pipeline Operator:

1. install [`operator-sdk`](https://github.com/operator-framework/operator-sdk)

2. clone this repo
```command line
git clone https://github.com/adrian555/pipelines-operator.git
```

3. have a docker registry like `hub.docker.io`

4. build and push the image
```command line
operator-sdk build ffdlops/kfp:v0.0.1
docker push ffdlops/kfp:v0.0.1
```

5. modify [`deploy/role_binding.yaml`]() to use the namespace specified through `kubectl` command
```command line
sed -i 's/namespace:.*$/namespace: kubeflow/g' deploy/role_binding.yaml
```
replace `kubeflow` with the namespace the operator is to be deployed on.

6. modify [`deploy/operator.yaml`](https://github.com/adrian555/pipelines-operator/blob/master/deploy/operator.yaml) to use the image built above

7. deploy the operator
```command line
pushd pipelines-operator
kubectl create -f deploy/crds/pipelines_v1alpha1_pipelines_crd.yaml
kubectl create -f deploy/service_account.yaml -n kubeflow
kubectl create -f deploy/role_binding.yaml -n kubeflow
kubectl create -f deploy/operator.yaml -n kubeflow
popd
```
replace `kubeflow` with your namespace to run on.

8. create the customer resource to deploy Kubeflow Pipelines
```command line
pushd pipelines-operator
kubectl create -f deploy/crds/pipelines_v1alpha1_pipelines_cr.yaml -n kubeflow
popd
```
replace `kubeflow` with your namespace to run on.

Once this completes, query the services and run following commands to expose the Pipelines UI.

```command line
export NAMESPACE=default
kubectl port-forward -n ${NAMESPACE} $(kubectl get pods -n ${NAMESPACE} --selector=service=ambassador -o jsonpath='{.items[0].metadata.name}') 8080:80 --address 0.0.0.0&
```

Run `http://<hostname>:8080/pipeline` to load the UI.

Note: this operator runs on [`minikube`](https://kubernetes.io/docs/setup/minikube/). Refer to this [link](https://github.com/adrian555/DocsDump/blob/dev/minikube.md) for running `minikube` on Ubuntu host without a hypervisor.
