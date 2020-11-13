# QuickStart

This document helps you get started to use Alameda. If you do not have Alameda deployed in your environment yet, please reference [deployment](./deploy.md) guide first.

## Using Alameda

To have Alameda makes resource usage recommendations for you, first thing is to tell Alameda what are the target containers by creating [_AlamedaScaler_](../design/crd_alamedascaler.md) CRs. Then you can see Grafana dashboards visualize them.

### Specify a target object

Users can create a custom resource of _AlamedaScaler_ custom resource definition (CRD) to instruct Alameda that:
1. which container needs resource usage recommendations, and
2. what policy that Alameda should use to give recommendations.
3. whether or not to execute the recommendations.

Currently Alameda watches containers that are deployed by _Deployment_ or _DeploymentConfig_ kinds and provides *stable* and *compact* policies. For more detail _AlamedaScaler_ schema, please refer to the [*AlamedaScaler* design](../design/crd_alamedascaler.md). The following is an example _AlamedaScaler_ CR.

```
apiVersion: autoscaling.containers.ai/v1alpha1
kind: AlamedaScaler
metadata:
  name: alameda
  namespace: webapp
spec:
  policy: stable
  enableexecution: true
  selector:
    matchLabels:
      app: nginx
```
This CR instructs Alameda to look up _Deployment_/_DeploymentConfig_ objects in _webapp_ namespaces with _nginx_ label. For any pods derived from them, Alameda will predict their resource usage and make recommendations with _stable_ considerations. Once new recommendations are available, Alameda will execute it according the _enableexecution_ switch.

You can list all the *AlamedaScaler* CRs in your namespace by:
```
$ kubectl get alamedascalers -n <your namespace>
```
and see the details by adding `-o yaml` flag.

> **Note**: an *AlamedaScaler* CR only looks for _Deployment_/_DeploymentConfig_ objects in the same namespace.

### Visualize Alameda recommendations

If alameda-grafana is deployed, users can also visualize Alameda workload predictions and recommendations through the pre-installed dashboards.
The Grafana URL can be figured out by checking the _alameda-grafana_ service name. To access it from outside the cluster, please either modify the service to  _NodePort_ type or consider to enable [_ingress_](https://kubernetes.io/docs/concepts/services-networking/ingress/) or [_route_](https://docs.openshift.com/container-platform/3.11/architecture/networking/routes.html). The default account is _admin_ with password _admin_.

## An Example Use Case

The following is an example of the Alameda workflow.

- First we need a target application such as nginx by:
    ```
    $ cd <alameda>/example/samples/nginx
    $ kubectl create -f nginx_deployment.yaml
    ```
- Then we request Alameda to recommend resource usage for nginx by:
    ```
    $ cd <alameda>/example/samples/nginx
    $ kubectl create -f alamedascaler.yaml
    ```

You can check that Alameda is watching containers running in the nginx application by:
```
$ kubectl get alamedascaler --all-namespaces
NAMESPACE   NAME      AGE
webapp      alameda   5h
$ kubectl get alamedascaler alameda -n webapp -o yaml
apiVersion: v1
items:
- apiVersion: autoscaling.containers.ai/v1alpha1
  kind: AlamedaScaler
  metadata:
    annotations:
      kubectl.kubernetes.io/last-applied-configuration: |
        {"apiVersion":"autoscaling.containers.ai/v1alpha1","kind":"AlamedaScaler","metadata":{"annotations":{},"name":"alameda","namespace":"webapp"},"spec":{"enableexecution":true,"policy":"stable","selector":{"matchLabels":{"app":"nginx"}}}}
    creationTimestamp: "2019-02-15T10:51:29Z"
    generation: 3
    name: alameda
    namespace: webapp
    resourceVersion: "2158849"
    selfLink: /apis/autoscaling.containers.ai/v1alpha1/namespaces/webapp/alamedascalers/alameda
    uid: a60c4c47-310f-11e9-accd-000c29b48f2a
  spec:
    enableexecution: true
    policy: stable
    selector:
      matchLabels:
        app: nginx
  status:
    alamedaController:
      deployments:
        webapp/nginx-deployment:
          name: nginx-deployment
          namespace: webapp
          pods:
            webapp/nginx-deployment-7bdddc58f9-6l677:
              containers:
              - name: nginx
                resources: {}
              name: nginx-deployment-7bdddc58f9-6l677
              uid: 960374db-310f-11e9-accd-000c29b48f2a
            webapp/nginx-deployment-7bdddc58f9-wfrbh:
              containers:
              - name: nginx
                resources: {}
              name: nginx-deployment-7bdddc58f9-wfrbh
              uid: 9602a059-310f-11e9-accd-000c29b48f2a
          uid: 95fd011b-310f-11e9-accd-000c29b48f2a
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```

By checking the Grafana dashboards, users can also visualize the resource prediction and recommendations.

