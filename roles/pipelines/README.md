# ansible-pipelines
Ansible install scripts for Kubeflow Pipelines


### Install Ansible

```command line
apt-add-repository --yes --update ppa:ansible/ansible
apt-get install -y ansible
```

### Install ksonnet

Refer to [Kubeflow quick start](https://www.kubeflow.org/docs/started/getting-started/) for requirements.

Follow [`ksonnet guide`](https://ksonnet.io/get-started/) to install. For example, following commands download and install `ksonnet v0.13.1` on Linux systems.

```command line
curl -L https://github.com/ksonnet/ksonnet/releases/download/v0.13.1/ks_0.13.1_linux_amd64.tar.gz -o ks_0.13.1_linux_amd64.tar.gz
tar zxvf ks_0.13.1_linux_amd64.tar.gz
cp ks_0.13.1_linux_amd64/ks /usr/local/bin
```

### Deploy Kubeflow Pipelines

```command line
ansible-playbook --verbose deployment.yml
```
