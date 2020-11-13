# Metrics Used in Alameda

Alameda leverages *Prometheus* to observe metrics of containers and nodes and this document summarizes those metrics used by Alameda.
> *Note:* Prometheus installed by [Prometheus Operator chart](https://github.com/helm/charts/tree/master/stable/prometheus-operator) or pre-installed in [Openshift 3.11](https://docs.openshift.com/container-platform/3.11/install_config/prometheus_cluster_monitoring.html) and above should already have all the metrics Alameda needs.

In the next section, the summarized metrics are represented as a list of items with subitems as annotations of a metric. Those annotations are:
- **purpose**  
  This annotation shows the metric is used in prediction, GUI or both. If the metric is only used for GUI, Alameda core function can still run well without the metric. Only _cluster status_ and _node status_ dashboards can not display well.
- **exporters**  
  A metric in Prometheus is either directly exposed by an exporter or synthesized from other metrics. This annotation shows the exporter(s) that exposes the raw metric(s) in any case and the next _rule expression_ annotation will show how a metric is produced if it is a synthesized result.
- **rule expression**  
  This annotation shows what Prometheus [_rule_](https://prometheus.io/docs/practices/rules/) produces the metric if it is not a raw metric.

## List of Metrics Used in Alameda
- metric name: namespace_pod_name_container_name:container_cpu_usage_seconds_total:sum_rate
  - purpose: prediction, GUI
  - exporters: kubelet
  - rule expression:  
  ```sum by(namespace, pod_name, container_name) (rate(container_cpu_usage_seconds_total{container_name!="",image!="",job="kubelet"}[5m]))```
- metric name: kube_pod_container_resource_requests_cpu_cores
  - purpose: GUI
  - exporters: kube-state-metrics
- metric name: kube_pod_container_resource_limits_cpu_cores
  - purpose: GUI
  - exporters: kube-state-metrics
- metric name: container_memory_usage_bytes
  - purpose: prediction, GUI
  - exporters: kubelet
- metric name: kube_pod_container_resource_requests_memory_bytes
  - purpose: GUI
  - exporters: kube-state-metrics
- metric name: kube_pod_container_resource_limits_memory_bytes
  - purpose: GUI
  - exporters: kube-state-metrics

- metric name: node:node_cpu_utilisation:avg1m
  - purpose: prediction, GUI
  - exporters: node-exporter, kube-state-metrics
  - rule expression:  
  ```1 - avg by(node) (rate(node_cpu_seconds_total{job="node-exporter",mode="idle"}[1m]) * on(namespace, pod) group_left(node) node_namespace_pod:kube_pod_info:)```
- metric name: node:node_memory_bytes_total:sum
  - purpose: prediction, GUI
  - exporters: node-exporter, kube-state-metrics
  - rule expression:  
  ```sum by(node) (node_memory_MemTotal_bytes{job="node-exporter"} * on(namespace, pod) group_left(node) node_namespace_pod:kube_pod_info:)```
  - exporters: node-exporter, kube-state-metrics

- metric name: up
  - purpose: GUI
  - exporters: kubernetes apiserver, kubelet
- metric name: apiserver_request_count
  - purpose: GUI
  - exporters: kubernetes apiserver
- metric name: kube_pod_container_status_restarts_total
  - purpose: GUI
  - exporters: kube-state-metrics
- metric name: kube_node_status_condition
  - purpose: GUI
  - exporters: kube-state-metrics
- metric name: kube_node_spec_unschedulable
  - purpose: GUI
  - exporters: kube-state-metrics
- metric name: node:node_num_cpu:sum
  - purpose: GUI
  - exporters: node-exporter, kube-state-metrics
  - rule expression:  
  ```count by(node) (sum by(node, cpu) (node_cpu_seconds_total{job="node-exporter"} * on(namespace, pod) group_left(node) node_namespace_pod:kube_pod_info:))```
- metric name: node:node_memory_utilisation:ratio
  - purpose: GUI
  - exporters: node-exporter, kube-state-metrics
  - rule expression:  
  ```(node:node_memory_bytes_total:sum - node:node_memory_bytes_available:sum) / node:node_memory_bytes_total:sum```
- metric name: node_filesystem_size_bytes
  - purpose: GUI
  - exporters: node-exporter, kube-state-metrics
- metric name: node:node_disk_utilisation:avg_irate
  - purpose: GUI
  - exporters: node-exporter, kube-state-metrics
  - rule expression:  
  ```avg by(node) (irate(node_disk_io_time_seconds_total{device=~"nvme.+|rbd.+|sd.+|vd.+|xvd.+",job="node-exporter"}[1m]) * on(namespace, pod) group_left(node) node_namespace_pod:kube_pod_info:)```
- metric name: :kube_pod_info_node_count:
  - purpose: GUI
  - exporters: kube-state-metrics
  - rule expression:  
  ```sum(min by(node) (kube_pod_info))```

- metric name: kube_node_status_capacity_cpu_cores
  - purpose: GUI
  - exporters: kube-state-metrics
- metric name: kube_node_status_capacity_memory_bytes
  - purpose: GUI
  - exporters: kube-state-metrics
- metric name: node:node_memory_utilisation:
  - purpose: GUI
  - exporters: kube-state-metrics
  - rule expression:  
  ```1 - sum by(node) ((node_memory_MemFree_bytes{job="node-exporter"} + node_memory_Cached_bytes{job="node-exporter"} + node_memory_Buffers_bytes{job="node-exporter"}) * on(namespace, pod) group_left(node) node_namespace_pod:kube_pod_info:) / sum by(node) (node_memory_MemTotal_bytes{job="node-exporter"} * on(namespace, pod) group_left(node) node_namespace_pod:kube_pod_info:)```
- metric name: container_cpu_usage_seconds_total
  - purpose: GUI
  - exporters: kubelet
- metric name: node_namespace_pod:kube_pod_info:
  - purpose: GUI
  - exporters: kube-state-metrics
  - rule expression:  
  ```max by(node, namespace, pod) (label_replace(kube_pod_info{job="kube-state-metrics"}, "pod", "$1", "pod", "(.*)"))```
- metric name: kube_pod_status_phase
  - purpose: GUI
  - exporters: kube-state-metrics


