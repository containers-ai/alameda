# Alameda Deployment

Currently the following deployment guides are provided. Please find an applicable one to your environment or start with the closest one.

> **NOTE:** For who are interested in using Alameda in daily operations, they may refer to [FederatorAI-Operator](https://github.com/containers-ai/federatorai-operator) that manages Alameda components in deployment, upgrade, storage, and application lifecycle aspects

- for Openshift
  - [one-click template guide](./Alameda_Installation_Guide_for_Red_Hat_OpenShift_Container_Platform.md)
  - [helm deploy guide](../helm/README.md)
  The guide here is the same as the one list in vanilla K8s which means the deployment does not utilize openshift extension apis such as *DeploymentConfig*.
  - [example deployment manifests for openshift](../example/deployment/openshift/README.md)
- for vanilla Kubernetes
  - [helm deploy guide](../helm/README.md)
  - [example deployment manifests for K8s](../example/deployment/kubernetes/README.md)

