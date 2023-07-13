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

# kubevirt install https://kubevirt.io/quickstart_cloud/
echo $VERSION
v1.0.0
kubectl apply -f https://github.com/kubevirt/kubevirt/releases/download/${VERSION}/kubevirt-operator.yaml
kubectl apply -f https://github.com/kubevirt/kubevirt/releases/download/${VERSION}/kubevirt-cr.yaml


# uninstall
kubectl delete -f https://github.com/kubevirt/kubevirt/releases/download/${VERSION}/kubevirt-cr.yaml
kubectl delete -f https://github.com/kubevirt/kubevirt/releases/download/${VERSION}/kubevirt-operator.yaml


# Kubevirt and nVidia pGPU ( passthrough GPU )

https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/latest/gpu-operator-kubevirt.html


https://kubevirt.io/user-guide/virtual_machines/host-devices/#listing-permitted-devices


ssh worker-gpu-1
lspci -nnv|grep -i nvidia
21:00.0 VGA compatible controller [0300]: NVIDIA Corporation GM204GL [Tesla M6] [10de:13f3] (rev a1) (prog-if 00 [VGA controller])
	Subsystem: NVIDIA Corporation GM204GL [Tesla M6] [10de:1143]
	Kernel driver in use: nvidia
	Kernel modules: nvidiafb, nouveau

ssh worker-gpu-2
23:00.0 VGA compatible controller [0300]: NVIDIA Corporation GM204GL [Tesla M6] [10de:13f3] (rev a1) (prog-if 00 [VGA controller])
	Subsystem: NVIDIA Corporation GM204GL [Tesla M6] [10de:1143]
	Kernel driver in use: nvidia
	Kernel modules: nvidiafb, nouveau
26:00.0 VGA compatible controller [0300]: NVIDIA Corporation GM204GL [Tesla M6] [10de:13f3] (rev a1) (prog-if 00 [VGA controller])
	Subsystem: NVIDIA Corporation GM204GL [Tesla M6] [10de:1143]
	Kernel driver in use: nvidia
	Kernel modules: nvidiafb, nouveau



```
