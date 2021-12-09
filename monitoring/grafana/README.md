# K8S Monitor
This document is about k8s monitor framework installation. There are many monitor frameworks, the differents is showing below.

## Prometheus Operator vs. kube-prometheus vs. community helm chart

### Prometheus Operator
The Prometheus Operator uses Kubernetes [custom resources](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/) to simplify the deployment and configuration of Prometheus, Alertmanager, and related monitoring components.

### kube-prometheus
[kube-prometheus](https://github.com/prometheus-operator/kube-prometheus) provides example configurations for a complete cluster monitoring
stack based on Prometheus and the Prometheus Operator.  This includes deployment of multiple Prometheus and Alertmanager instances,
metrics exporters such as the node_exporter for gathering node metrics, scrape target configuration linking Prometheus to various
metrics endpoints, and example alerting rules for notification of potential issues in the cluster.

### helm chart
The [prometheus-community/kube-prometheus-stack](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack)
helm chart provides a similar feature set to kube-prometheus. This chart is maintained by the Prometheus community.
For more information, please see the [chart's readme](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack#kube-prometheus-stack)

Finally, we choose the **kube-prometheus** for all stack.

## Installation

### Prerequisites
Follow the [kube-prometheus](https://github.com/prometheus-operator/kube-prometheus) link, we check our environment prerequisites and select a kube-promethues matching branch.  
This means the kubelet configuration must contain these flags:
* `--authentication-token-webhook=true` This flag enables, that a `ServiceAccount` token can be used to authenticate against the kubelet(s).  This can also be enabled by setting the kubelet configuration value `authentication.webhook.enabled` to `true`.
* `--authorization-mode=Webhook` This flag enables, that the kubelet will perform an RBAC request with the API to determine, whether the requesting entity (Prometheus in this case) is allowed to access a resource, in specific for this project the `/metrics` endpoint.  This can also be enabled by setting the kubelet configuration value `authorization.mode` to `Webhook`.

We choose the **v0.8.0** tag for deployment.

### Build
1. Install Jsonnet Bundler, Jsonnet, Jsontoyaml
```
GO111MODULE="on" GOPROXY="https://goproxy.cn" go get github.com/jsonnet-bundler/jsonnet-bundler/cmd/jb
export PATH=$(go env GOPATH)/bin:$PATH
GO111MODULE="on" GOPROXY="https://goproxy.cn" go get github.com/google/go-jsonnet/cmd/jsonnet
GO111MODULE="on" GOPROXY="https://goproxy.cn" go get github.com/brancz/gojsontoyaml
```
2. Compile kube-prometheus project.
```
mkdir my-kube-prometheus; cd my-kube-prometheus
jb init 
jb install github.com/prometheus-operator/kube-prometheus/jsonnet/kube-prometheus@release-0.8
wget https://raw.githubusercontent.com/prometheus-operator/kube-prometheus/release-0.8/example.jsonnet -O example.jsonnet
wget https://raw.githubusercontent.com/prometheus-operator/kube-prometheus/release-0.8/build.sh -O build.sh
./build.sh example.jsonnet
sed -i 's/UTC/browser/g' kube-prometheus/manifests/grafana-dashboardDefinitions.yaml
sed -i 's/utc/browser/g' kube-prometheus/manifests/grafana-dashboardDefinitions.yaml
修改datasource文件，设置prometheus间隔
manifests/grafana-dashboardDatasources.yaml
apiVersion: v1
data:
  datasources.yaml: ewogICAgImFwaVZlcnNpb24iOiAxLAogICAgImRhdGFzb3VyY2VzIjogWwogICAgICAgIHsKICAgICAgICAgICAgImFjY2VzcyI6ICJwcm94eSIsCiAgICAgICAgICAgICJlZGl0YWJsZSI6IGZhbHNlLAogICAgICAgICAgICAibmFtZSI6ICJwcm9tZXRoZXVzIiwKICAgICAgICAgICAgIm9yZ0lkIjogMSwKICAgICAgICAgICAgInR5cGUiOiAicHJvbWV0aGV1cyIsCiAgICAgICAgICAgICJ1cmwiOiAiaHR0cDovL3Byb21ldGhldXMtazhzLm1vbml0b3Jpbmcuc3ZjOjkwOTAiLAogICAgICAgICAgICAidmVyc2lvbiI6IDEsCgkJCSJqc29uRGF0YSI6IHsKCQkJICAidGltZUludGVydmFsIjogIjMwcyIKCQkJfQogICAgICAgIH0KICAgIF0KfQo=
kind: Secret
metadata:
  labels:
    app.kubernetes.io/component: grafana
    app.kubernetes.io/name: grafana
    app.kubernetes.io/part-of: kube-prometheus
    app.kubernetes.io/version: 7.5.4
  name: grafana-datasources
  namespace: monitoring
type: Opaque


```
3. Download Images
```
docker pull quay.io/prometheus/alertmanager:v0.21.0
docker pull jimmidyson/configmap-reload:v0.5.0
docker pull quay.io/brancz/kube-rbac-proxy:v0.8.0
docker pull quay.io/prometheus/blackbox-exporter:v0.18.0
docker pull grafana/grafana:7.5.4
docker pull k8s.gcr.io/kube-state-metrics/kube-state-metrics:v2.0.0
docker pull quay.io/prometheus/node-exporter:v1.1.2
docker pull directxman12/k8s-prometheus-adapter:v0.8.4
docker pull quay.io/prometheus/prometheus:v2.26.0
docker pull quay.io/prometheus-operator/prometheus-operator:v0.47.0
docker pull quay.io/prometheus-operator/prometheus-config-reloader:v0.47.0

docker tag quay.io/prometheus/alertmanager:v0.21.0 harbor.dev.21vianet.com/library/alertmanager:v0.21.0
docker tag jimmidyson/configmap-reload:v0.5.0 harbor.dev.21vianet.com/library/configmap-reload:v0.5.0
docker tag quay.io/brancz/kube-rbac-proxy:v0.8.0 harbor.dev.21vianet.com/library/kube-rbac-proxy:v0.8.0
docker tag quay.io/prometheus/blackbox-exporter:v0.18.0 harbor.dev.21vianet.com/library/blackbox-exporter:v0.18.0
docker tag grafana/grafana:7.5.4 harbor.dev.21vianet.com/library/grafana:7.5.4
docker tag k8s.gcr.io/kube-state-metrics/kube-state-metrics:v2.0.0 harbor.dev.21vianet.com/library/kube-state-metrics:v2.0.0
docker tag quay.io/prometheus/node-exporter:v1.1.2 harbor.dev.21vianet.com/library/node-exporter:v1.1.2
docker tag directxman12/k8s-prometheus-adapter:v0.8.4 harbor.dev.21vianet.com/library/k8s-prometheus-adapter:v0.8.4
docker tag quay.io/prometheus/prometheus:v2.26.0 harbor.dev.21vianet.com/library/prometheus:v2.26.0
docker tag quay.io/prometheus-operator/prometheus-operator:v0.47.0 harbor.dev.21vianet.com/library/prometheus-operator:v0.47.0
docker tag quay.io/prometheus-operator/prometheus-config-reloader:v0.47.0 harbor.dev.21vianet.com/library/prometheus-config-reloader:v0.47.0

docker push harbor.dev.21vianet.com/library/alertmanager:v0.21.0
docker push harbor.dev.21vianet.com/library/configmap-reload:v0.5.0
docker push harbor.dev.21vianet.com/library/kube-rbac-proxy:v0.8.0
docker push harbor.dev.21vianet.com/library/blackbox-exporter:v0.18.0
docker push harbor.dev.21vianet.com/library/grafana:7.5.4
docker push harbor.dev.21vianet.com/library/kube-state-metrics:v2.0.0
docker push harbor.dev.21vianet.com/library/node-exporter:v1.1.2
docker push harbor.dev.21vianet.com/library/k8s-prometheus-adapter:v0.8.4
docker push harbor.dev.21vianet.com/library/prometheus:v2.26.0
docker push harbor.dev.21vianet.com/library/prometheus-operator:v0.47.0
docker push docker push harbor.dev.21vianet.com/library/prometheus-config-reloader:v0.47.0


```
4. Install the crd and kubernete manifests
Insert storageclass configuration in prometheus configuration.  
vendor/github.com/prometheus-operator/kube-prometheus/jsonnet/kube-prometheus/components/prometheus.libsonnet
```
  prometheus: {
    apiVersion: 'monitoring.coreos.com/v1',
    kind: 'Prometheus',
    metadata: {
      name: p._config.name,
      namespace: p._config.namespace,
      labels: { prometheus: p._config.name } + p._config.commonLabels,
    },
    spec: {
      replicas: p._config.replicas,
      version: p._config.version,
      image: p._config.image,
      podMetadata: {
        labels: p._config.commonLabels,
      },
      externalLabels: p._config.externalLabels,
      serviceAccountName: 'prometheus-' + p._config.name,
      serviceMonitorSelector: {},
      podMonitorSelector: {},
      probeSelector: {},
      serviceMonitorNamespaceSelector: {},
      podMonitorNamespaceSelector: {},
      probeNamespaceSelector: {},
      nodeSelector: { 'kubernetes.io/os': 'linux' },
      ruleSelector: p._config.ruleSelector,
      resources: p._config.resources,
      alerting: {
        alertmanagers: [{
          namespace: p._config.namespace,
          name: 'alertmanager-' + p._config.alertmanagerName,
          port: 'web',
          apiVersion: 'v2',
        }],
      },
      securityContext: {
        runAsUser: 1000,
        runAsNonRoot: true,
        fsGroup: 2000,
      },
      storage: {
        volumeClaimTemplate: {
          spec: {
            storageClassName: 'csi-rbd-sc',
            resources: {
              requests: {
                storage: '40Gi',
              },
            },
          },
        },
      },
      [if std.objectHas(params, 'thanos') then 'thanos']: p._config.thanos,
    },
  },

```
Insert persistent volume to grafana pod

vi vendor/github.com/brancz/kubernetes-grafana/grafana/grafana.libsonnet, add k.libsonnet header and storage block and modify storageVolume block.
<pre>
local k = import 'ksonnet/ksonnet.beta.4/k.libsonnet';
......
    storage:
      local pvc = k.core.v1.persistentVolumeClaim;
      pvc.new() +
      pvc.mixin.metadata.withNamespace($._config.namespace) +
      pvc.mixin.metadata.withName(&apos;grafana-storage&apos;) +
      pvc.mixin.spec.withAccessModes(&apos;ReadWriteMany&apos;) +
      pvc.mixin.spec.withStorageClassName(&apos;csi-cephfs-sc&apos;) +
      pvc.mixin.spec.resources.withRequests({ storage: &apos;2Gi&apos; }),
    deployment:
      local targetPort = $._config.grafana.port;
      local portName = &apos;http&apos;;
      local podLabels = $._config.grafana.labels;
      local podSelectorLabels = {
        [labelName]: podLabels[labelName]
        for labelName in std.objectFields(podLabels)
        if !std.setMember(labelName, [&apos;app.kubernetes.io/version&apos;])
      };

      local configVolumeName = &apos;grafana-config&apos;;
      local configSecretName = &apos;grafana-config&apos;;
      local configVolume = { name: configVolumeName, secret: { secretName: configSecretName } };
      local configVolumeMount = { name: configVolumeName, mountPath: &apos;/etc/grafana&apos;, readOnly: false };

      local storageVolumeName = &apos;grafana-storage&apos;;
      local storageVolume = { name: storageVolumeName, persistentVolumeClaim: {claimName: &apos;grafana-storage&apos;} };
      local storageVolumeMount = { name: storageVolumeName, mountPath: &apos;/var/lib/grafana&apos;, readOnly: false };

      local datasourcesVolumeName = &apos;grafana-datasources&apos;;
      local datasourcesSecretName = &apos;grafana-datasources&apos;;
      local datasourcesVolume = { name: datasourcesVolumeName, secret: { secretName: datasourcesSecretName } };
      local datasourcesVolumeMount = { name: datasourcesVolumeName, mountPath: &apos;/etc/grafana/provisioning/datasources&apos;, readOnly: false };
</pre>

modify the priviledge for volume file creation
<pre>
            spec: {
              containers: c,
              volumes: volumes,
              serviceAccountName: $.grafana.serviceAccount.metadata.name,
              nodeSelector: { 'beta.kubernetes.io/os': 'linux' },
              securityContext: { fsGroup: 0, runAsUser: 0 },
            },
</pre>

vi vendor/github.com/prometheus-operator/kube-prometheus/jsonnet/kube-prometheus/components/grafana.libsonnet and add persistentVolumeClaim block.
<pre>
  persistentVolumeClaim: glib.grafana.storage,
  deployment: glib.grafana.deployment,
  dashboardDatasources: glib.grafana.dashboardDatasources,
  dashboardSources: glib.grafana.dashboardSources,
</pre>


```
$ kubectl apply -f manifests/setup
$ kubectl apply -f manifests/
```

## Reference
- https://github.com/prometheus-operator/prometheus-operator
- https://github.com/prometheus-operator/kube-prometheus