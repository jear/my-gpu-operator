```
helm delete gpu-operator -n gpu-operator 

k delete -f nvidia.com_clusterpolicies_crd.yaml
customresourcedefinition.apiextensions.k8s.io "clusterpolicies.nvidia.com" deleted

helm repo add nvidia https://helm.ngc.nvidia.com/nvidia    && helm repo update

helm upgrade --install gpu-operator nvidia/gpu-operator -n gpu-operator --set operator.upgradeCRD=true -f values-22.9.1.yaml
helm upgrade --install gpu-operator nvidia/gpu-operator -n gpu-operator --create-namespace --set operator.upgradeCRD=true --disable-openapi-validation

# if you can access to nvid.nvidia.com..... Build nVidia vGPU (for kubevirt use case )
Download the vGPU Software from the NVIDIA Licensing Portal. https://nvid.nvidia.com/dashboard/#/dashboard
https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/latest/gpu-operator-kubevirt.html#build-vgpu-manager-image

# Install the GPU Operator (without NVIDIA vGPU)
helm .........  --set sandboxWorkloads.enabled=true

helm upgrade --install gpu-operator nvidia/gpu-operator -n gpu-operator --create-namespace --set operator.upgradeCRD=true --disable-openapi-validation --set sandboxWorkloads.enabled=true

# Kubevirt and nVidia pGPU ( passthrough GPU )
https://kubevirt.io/user-guide/virtual_machines/host-devices/#listing-permitted-devices
https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/latest/gpu-operator-kubevirt.html

```
