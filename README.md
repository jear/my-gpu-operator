```
helm delete gpu-operator -n gpu-operator 

k delete -f nvidia.com_clusterpolicies_crd.yaml
customresourcedefinition.apiextensions.k8s.io "clusterpolicies.nvidia.com" deleted

helm repo add nvidia https://helm.ngc.nvidia.com/nvidia    && helm repo update

helm upgrade --install gpu-operator nvidia/gpu-operator -n gpu-operator --set operator.upgradeCRD=true -f values-22.9.1.yaml
helm upgrade --install gpu-operator nvidia/gpu-operator -n gpu-operator --create-namespace --set operator.upgradeCRD=true --disable-openapi-validation

# Build nVidia vGPU
https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/latest/gpu-operator-kubevirt.html#build-vgpu-manager-image

# Kubevirt and nVidia
https://kubevirt.io/user-guide/virtual_machines/host-devices/#listing-permitted-devices
https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/latest/gpu-operator-kubevirt.html


```
