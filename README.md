# pipelines-operator

This operator is an application to help deploy the [`Kubeflow`](https://www.kubeflow.org/) components to run Pipelines.

To run the Pipeline Operator:

1. follow the [`Quick Start`](https://github.com/operator-framework/operator-sdk) to install `operator-sdk`

2. follow the [`instruction`](https://github.com/operator-framework/operator-sdk/blob/master/doc/ansible/user-guide.md) to install the requirement for `ansible` type of operator

3. clone this repo
```command line
git clone https://github.com/adrian555/pipelines-operator.git
```

4. have a docker registry like `hub.docker.io`

5. build and push the image
```command line
operator-sdk build ffdlops/kfp:v0.0.1
docker push ffdlops/kfp:v0.0.1
```

6. modify [`deploy/role_binding.yaml`]() to use the namespace specified through `kubectl` command
```command line
pushd pipelines-operator
sed -i 's/namespace:.*$/namespace: kubeflow/g' deploy/role_binding.yaml
popd
```
replace `kubeflow` with the namespace the operator is to be deployed on.

7. modify [`deploy/operator.yaml`](https://github.com/adrian555/pipelines-operator/blob/master/deploy/operator.yaml) to use the image built above

8. deploy the operator
```command line
pushd pipelines-operator
kubectl create -f deploy/crds/pipelines_v1alpha1_pipelines_crd.yaml
kubectl create -f deploy/service_account.yaml -n kubeflow
kubectl create -f deploy/role_binding.yaml -n kubeflow
kubectl create -f deploy/operator.yaml -n kubeflow
popd
```
replace `kubeflow` with your namespace to run on.

9. create the customer resource to deploy Kubeflow Pipelines
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

## - Hack to work around the PVC limit on IKS shared account

* Create a NFS dynamic StorageClass and PV --- this step can be done as part of the [jupyterlab-operator](https://github.com/adrian555/jupyterlab-operator) install

* Set the NFS dynamic StorageClass as default

```command line
# make the current default StorageClass as non-default
kubectl patch storageclass ibmc-file-bronze -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'

# make a StorageClass as default
kubectl patch storageclass nfs-dynamic -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

replace `ibmc-file-bronze` with name of the current default class and replace `nfs-dynamic` with name of the new default class.