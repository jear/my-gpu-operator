```
lsmod | grep -i nouveau
dmesg | grep -i nouveau

sudo tee /etc/modules-load.d/ipmi.conf <<< "ipmi_msghandler"     && sudo tee /etc/modprobe.d/blacklist-nouveau.conf <<< "blacklist nouveau"     && sudo tee -a /etc/modprobe.d/blacklist-nouveau.conf <<< "options nouveau modeset=0"
sudo update-initramfs -u
sudo init 6


# Uninstall operator
helm delete gpu-operator -n gpu-operator 

# k delete -f nvidia.com_clusterpolicies_crd.yaml
k delete crd clusterpolicies.nvidia.com nvidiadrivers.nvidia.com

# Install operator
helm repo add nvidia https://helm.ngc.nvidia.com/nvidia    && helm repo update

helm upgrade --install gpu-operator nvidia/gpu-operator -n gpu-operator --set operator.upgradeCRD=true -f values-22.9.1.yaml
# or
helm upgrade --install gpu-operator nvidia/gpu-operator -n gpu-operator --create-namespace \
             --set operator.upgradeCRD=true --disable-openapi-validation

```

```
https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/latest/getting-started.html#gpu-telemetry

helm upgrade --install gpu-operator nvidia/gpu-operator -n gpu-operator \
--set dcgmExporter.config.name=metrics-config \
--set dcgmExporter.env[0].name=DCGM_EXPORTER_COLLECTORS \
--set dcgmExporter.env[0].value=/etc/dcgm-exporter/dcgm-metrics.csv
```


# setup prometheus
https://docs.nvidia.com/datacenter/cloud-native/gpu-telemetry/latest/kube-prometheus.html
```
# kubecost
helm upgrade --install my-prometheus  prometheus-community/kube-prometheus-stack  \
             --namespace prometheus --create-namespace  \
             --values ./kube-prometheus-stack.values.nvidia \
             --set prometheus-pushgateway.enabled=false \
             --set alertmanager.enabled=false \
             -f https://raw.githubusercontent.com/opencost/opencost/develop/kubernetes/prometheus/extraScrapeConfigs.yaml 
```
