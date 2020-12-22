# Deploy Alameda on a Kubernetes Cluster

## Prerequisites  
1. **A running Kubernetest cluster**  
It is recommended to use Kubernetes 1.9 or greater. Please follow [Kubernetes setup](https://kubernetes.io/docs/setup/) document to setup a solution that fits your needs. If you just want to have a taste of Alameda, you can start from [kubeadm](https://kubernetes.io/docs/setup/independent/install-kubeadm/) to setup a test cluster on a Ubuntu machine in 6 steps.
    - Install [kubeadm](https://kubernetes.io/docs/setup/independent/install-kubeadm/#k8s-install-0)
    - Disable swap in order for the kubelet to work properly by
    ```
    swapoff -a
    ```
    - Initialize master node by 
    ```
    kubeadm init --pod-network-cidr=10.244.0.0/16
    ```
    - Make *kubectl* work for your non-root user by
    ```
    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config
    ```
    - Allow pod scheduling on the master
    ```
    kubectl taint nodes --all node-role.kubernetes.io/master-
    ```
    - Install Flannel pod network add-on to allow pods communicate with each other by
    ```
    sysctl net.bridge.bridge-nf-call-iptables=1
    kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/bc79dd1505b0c8681ece4de4c0d86c5cd2643275/Documentation/kube-flannel.yml
    ```
    
2. **A running Prometheus**
Alameda leverages [Prometheus](https://kubernetes.io/docs/tasks/debug-application-cluster/resource-usage-monitoring/#prometheus) to collect Kubernetes metrics. All the metrics that are needed by Alameda are summarized in [metrics_used_in_Alameda.md](../../../docs/metrics_used_in_Alameda.md).
For those clusters without a running Prometheus, please refer to https://github.com/coreos/prometheus-operator for the installation. For users to quickstart it, please refer to our [deploy guide by helm chart](../../../helm/README.md) to install a Prometheus.


As showed in the [Alameda architecture design](https://github.com/containers-ai/alameda/blob/master/design/architecture.md), Alameda includes several components. The following shows how to deploy them with the provided example K8s manifests.

## Deploy Alameda Components

### alameda-operator
Please execute:
```
$ kubectl apply -f example/deployment/kubernetes/alameda-operator/
```

### alameda-datahub
Please execute:
```
$ kubectl apply -f example/deployment/kubernetes/alameda-datahub/
```
Please note that the one of the responsibility of datahub is to retrieve metrics from Prometheus and the Prometheus URL setting in this deployment manifest is set to _http://prometheus-prometheus-oper-prometheus.monitoring:9090_. Please change that according to your environment.

### alameda-ai
Please execute:
```
$ kubectl apply -f example/deployment/kubernetes/alameda-ai/
```

### alameda-evictioner
Please execute:
```
$ kubectl apply -f example/deployment/kubernetes/alameda-evictioner/
```

### admission-controller
Please execute:
```
$ kubectl apply -f example/deployment/kubernetes/admission-controller/
```

> **Note:** The images of the _alameda-operator_, _alameda-datahub_, _alameda-ai_, _alameda-evictioner_, and _admission-controller_ components are assumed existed in local docker environment and pulled from it. Please refer to [build guide](../docs/build.md) for building images from source code or change the image repository settings before applying the deployment manifest.

### alameda-influxdb
This component leverages the open source [InfluxDB](https://github.com/influxdata/influxdb). To install it, please execute:
```
$ kubectl apply -f example/deployment/kubernetes/alameda-influxdb/
```

### alameda-grafana
This component leverages the open source [Grafana](https://github.com/grafana/grafana). To install it, please execute:
```
$ kubectl apply -f example/deployment/kubernetes/alameda-grafana/
```
This will install Grafana with default datasources and customized dashboards. Please also note that the Prometheus URL is set to _http://prometheus-prometheus-oper-prometheus.monitoring:9090_. Please change that according to your environment before applying the configmap manifest.

