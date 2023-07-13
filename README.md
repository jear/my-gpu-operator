```
# Uninstall operator
helm delete gpu-operator -n gpu-operator 

k delete -f nvidia.com_clusterpolicies_crd.yaml
customresourcedefinition.apiextensions.k8s.io "clusterpolicies.nvidia.com" deleted

# Install operator
helm repo add nvidia https://helm.ngc.nvidia.com/nvidia    && helm repo update

helm upgrade --install gpu-operator nvidia/gpu-operator -n gpu-operator --set operator.upgradeCRD=true -f values-22.9.1.yaml
# or
helm upgrade --install gpu-operator nvidia/gpu-operator -n gpu-operator --create-namespace --set operator.upgradeCRD=true --disable-openapi-validation




# Kubevirt and nVidia notes
# if you can access to nvid.nvidia.com..... Build nVidia vGPU (for kubevirt use case )
Download the vGPU Software from the NVIDIA Licensing Portal. https://nvid.nvidia.com/dashboard/#/dashboard
https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/latest/gpu-operator-kubevirt.html#build-vgpu-manager-image

# Install the GPU Operator (without NVIDIA vGPU)
helm .........  --set sandboxWorkloads.enabled=true

helm upgrade --install gpu-operator nvidia/gpu-operator -n gpu-operator --create-namespace --set operator.upgradeCRD=true --disable-openapi-validation --set sandboxWorkloads.enabled=true

# kubevirt
export VERSION=$(curl -s https://api.github.com/repos/kubevirt/kubevirt/releases | grep tag_name | grep -v -- '-rc' | sort -r | head -1 | awk -F': ' '{print $2}' | sed 's/,//' | xargs)
echo $VERSION
v1.0.0
kubectl apply -f https://github.com/kubevirt/kubevirt/releases/download/${VERSION}/kubevirt-operator.yaml

kubectl apply -f https://github.com/kubevirt/kubevirt/releases/download/${VERSION}/kubevirt-cr.yaml
kubectl apply -f https://github.com/kubevirt/kubevirt/releases/download/v1.0.0/kubevirt-cr.yaml

# uninstall
kubectl delete -f https://github.com/kubevirt/kubevirt/releases/download/v1.0.0/kubevirt-cr.yaml
kubectl delete -f https://github.com/kubevirt/kubevirt/releases/download/v1.0.0/kubevirt-operator.yaml


# Kubevirt and nVidia pGPU ( passthrough GPU )
https://kubevirt.io/user-guide/virtual_machines/host-devices/#listing-permitted-devices
https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/latest/gpu-operator-kubevirt.html

```
