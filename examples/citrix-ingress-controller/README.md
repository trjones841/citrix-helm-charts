# Citrix Ingress Controller  

[Citrix](https://www.citrix.com) Ingress Controller runs as a pod in a [Kubernetes](https://kubernetes.io) Cluster or in an [Openshift](https://www.openshift.com) cluster and configures the NetScaler VPX/MPX and load balances GuestBook App.


## TL;DR;

### For Kubernetes
```
   git clone https://github.com/citrix/citrix-helm-charts.git
   cd citrix-helm-charts/examples/
   helm install cic citrix-ingress-controller --set nsIP=<NSIP>,license.accept=yes,nsVIP=<VIP>,loginFileName=<Secret-for-ADC-credentials>
```

### For OpenShift

```
   git clone https://github.com/citrix/citrix-helm-charts.git
   cd citrix-helm-charts/examples/
   helm install cic citrix-ingress-controller --set nsIP=<NSIP>,license.accept=yes,nsVIP=<VIP>,loginFileName=<Secret-for-ADC-credentials>,openshift=true
```

> Note: "license.accept" is a mandatory argument and should be set to "yes" to accept the terms of the Citrix license.

## Introduction
This Chart deploys Citrix Ingress Controller in the [Kubernetes](https://kubernetes.io) Cluster or in the Openshift cluster using [Helm](https://helm.sh) package manager.

### Prerequisites

- The [Kubernetes](https://kubernetes.io/) version is 1.6 or later if using Kubernetes environment.
- The [Openshift](https://www.openshift.com) version 3.11.x or later if using OpenShift platform.
- The [Helm](https://helm.sh/) version 3.x or later. You can follow instruction given [here](https://github.com/citrix/citrix-helm-charts/blob/master/Helm_Installation_version_3.md) to install the same.
- You have installed [Prometheus Operator](https://github.com/coreos/prometheus-operator), if you want to view the metrics of the Citrix ADC CPX collected by the [metrics exporter](https://github.com/citrix/citrix-k8s-ingress-controller/tree/master/metrics-visualizer#visualization-of-metrics).

## Installing the Chart

### For Kubernetes:

To install the chart with the release name ``` my-release:```

```helm install my-release citrix-ingress-controller --set nsIP=<NSIP>,license.accept=yes,nsVIP=<VIP>,loginFileName=<Secret-for-ADC-credentials>,ingressClass[0]=<ingressClassName> ```

If you want to run exporter along with CIC, please install prometheus operator first and then use the following command:

```helm install my-release citrix-ingress-controller  --set nsIP=<NSIP>,license.accept=yes,nsVIP=<VIP>,loginFileName=<Secret-for-ADC-credentials>,ingressClass=<ingressClassName>,exporter.required=true```

### For OpenShift:
Add the service account named "cic-k8s-role" to the privileged Security Context Constraints of OpenShift:

```oc adm policy add-scc-to-user privileged system:serviceaccount:<namespace>:cic-k8s-role```

To install the chart with the release name ``` my-release:```

```helm install my-release citrix-ingress-controller --set nsIP=<NSIP>,license.accept=yes,nsVIP=<VIP>,loginFileName=<Secret-for-ADC-credentials>,ingressClass[0]=<ingressClassName>,openshift=true ```

If you want to run exporter along with CIC, please install prometheus operator first and then use the following command:

```helm install my-release citrix-ingress-controller --set nsIP=<NSIP>,license.accept=yes,nsVIP=<VIP>,loginFileName=<Secret-for-ADC-credentials>,ingressClass=<ingressClassName>,exporter.required=true,openshift=true```

These command deploys Citrix Ingress Controller on the Kubernetes cluster or in an OpenShift Cluster in the default configuration. The configuration section lists the parameters that can be configured during installation.
 
## Uninstalling the Chart
To uninstall/delete the ```my-release``` deployment:
```
helm delete --purge my-release
```
The command removes all the Kubernetes components associated with the chart and deletes the release

## Configuration
The following table lists the mandatory and optional parameters that you can configure during installation:

| Parameters | Mandatory or Optional | Default value | Description |
| --------- | --------------------- | ------------- | ----------- |
| license.accept | Mandatory | no | Set `yes` to accept the CIC end user license agreement. |
| image | Mandatory | `quay.io/citrix/citrix-k8s-ingress-controller:1.8.28` | The CIC image. |
| pullPolicy | Mandatory | IfNotPresent | The CIC image pull policy. |
| loginFileName | Mandatory | N/A | The secret key to log on to the Citrix ADC VPX or MPX. For information on how to create the secret keys, see [Prerequisites](#prerequistes). |
| nsIP | Mandatory | N/A | The IP address of the Citrix ADC device. For details, see [Prerequisites](#prerequistes). |
| nsVIP | Optional | N/A | The Virtual IP address on the Citrix ADC device. |
| nsPort | Optional | 443 | The port used by CIC to communicate with Citrix ADC. You can use port 80 for HTTP. |
| nsProtocol | Optional | HTTPS | The protocol used by CIC to communicate with Citrix ADC. You can also use HTTP on port 80. |
| logLevel | Optional | DEBUG | The loglevel to control the logs generated by CIC. The supported loglevels are: CRITICAL, ERROR, WARNING, INFO, DEBUG and TRACE. For more information, see [Logging](https://github.com/citrix/citrix-k8s-ingress-controller/blob/master/docs/configure/log-levels.md).|
| kubernetesURL | Optional | N/A | The kube-apiserver url that CIC uses to register the events. If the value is not specified, CIC uses the [internal kube-apiserver IP address](https://kubernetes.io/docs/tasks/access-application-cluster/access-cluster/#accessing-the-api-from-a-pod). |
| ingressClass | Optional | N/A | If multiple ingress load balancers are used to load balance different ingress resources. You can use this parameter to specify CIC to configure Citrix ADC associated with specific ingress class. For more information on Ingress class, see [Ingress class support](https://developer-docs.citrix.com/projects/citrix-k8s-ingress-controller/en/latest/configure/ingress-classes/). |
| nodeWatch | Optional | false | Use the argument if you want to automatically configure network route from the Ingress Citrix ADC VPX or MPX to the pods in the Kubernetes cluster. For more information, see [Automatically configure route on the Citrix ADC instance](https://developer-docs.citrix.com/projects/citrix-k8s-ingress-controller/en/latest/network/staticrouting/#automatically-configure-route-on-the-citrix-adc-instance). |
| defaultSSLCert | Optional | N/A | Default SSL certificate that needs to be used as a non-SNI certificate in Citrix ADC. |
| http2ServerSide | Optional | OFF | Enables HTTP2 for Citrix ADC service group configurations. |
| ipam | Optional | False | Set this argument if you want to use the IPAM controller to automatically allocate an IP address to the service of type LoadBalancer. |
| logProxy | Optional | N/A | Provide Elasticsearch or Kafka or Zipkin endpoint for Citrix observability exporter. |
| nsNamespace | Optional | k8s | The prefix for the resources on the Citrix ADC VPX/MPX. |
| exporter.required | Optional | false | Use the argument, if you want to run the [Exporter for Citrix ADC Stats](https://github.com/citrix/citrix-adc-metrics-exporter) along with CIC to pull metrics for the Citrix ADC VPX or MPX|
| exporter.image    | Optional | `quay.io/citrix/citrix-adc-metrics-exporter:1.4.4` | The Exporter image. |
| exporter.pullPolicy | Optional | IfNotPresent | The Exporter image pull policy. |
| exporter.ports.containerPort | Optional | 8888 | The Exporter container port. |
| openshift | Optional | false | Set this argument if OpenShift environment is being used. |
| crds.install | Optional | true | Unset this argument if you don't want to install CustomResourceDefinitions which are consumed by CIC. |
| crds.retainOnDelete | Optional | false | Set this argument if you want to retain CustomResourceDefinitions even after uninstalling CIC. This will avoid data-loss of Custom Resource Objects created before uninstallation. |


> **Tip:**
>
> The [values.yaml](https://github.com/citrix/citrix-helm-charts/blob/master/examples/citrix-cpx-with-ingress-controller/values.yaml) contains the default values of the parameters.

## Route Addition in MPX/VPX

Configure static routes on Citrix ADC VPX or MPX to reach the pods inside the cluster.

### For Kubernetes:
1. Obtain podCIDR using below options:
   ```kubectl get nodes -o yaml | grep podCIDR```

  * podCIDR: 10.244.0.0/24
  * podCIDR: 10.244.1.0/24
  * podCIDR: 10.244.2.0/24

2. Add Route in Netscaler VPX/MPX

   ```add route <podCIDR_network> <podCIDR_netmask> <node_HostIP>```

3. Ensure that Ingress MPX/VPX has a SNIP present in the host-network (i.e. network over which K8S nodes communicate with each other. Usually eth0 IP is from this network).

   Example: 
   * Node1 IP = 192.0.2.1
   * podCIDR  = 10.244.1.0/24
   * add route 10.244.1.0 255.255.255.0 192.0.2.1

### For OpenShift:
1. Use the following command to get the information about host names, host IP addresses, and subnets for static route configuration.
   ``` oc get hostsubnet```

2.  Log on to the Citrix ADC instance.
3.  Add the route on the Citrix ADC instance using the following command.
    ```add route <pod_network> <podCIDR_netmask> <gateway>```

    For example, if the output of the `oc get hostsubnet` is as follows:
    * oc get hostsubnet

        NAME            HOST           HOST IP        SUBNET
        os.example.com  os.example.com 192.0.2.1 10.1.1.0/24

    * The required static route is as follows:

           add route 10.1.1.0 255.255.255.0 192.0.2.1

## Secret Keys
To generate secret keys use
``` 
kubectl create secret  generic <filename> --from-literal=username='<username>' --from-literal=password='<password>'
```
The created filename can be passed to values.yaml.

## RBAC
By default the chart will install the recommended [RBAC](https://kubernetes.io/docs/admin/authorization/rbac/) roles and rolebindings.

## Exporter
[Exporter](https://github.com/citrix/citrix-adc-metrics-exporter) is running along with the CIC and pulling metrics from the VPX/MPX. It exposes the metrics using Kubernetes NodePort.

## Ingress Class
To know more about Ingress Class refer [this](https://github.com/citrix/citrix-k8s-ingress-controller/blob/master/docs/ingress-class.md). 

## For More Info: https://github.com/citrix/citrix-k8s-ingress-controller