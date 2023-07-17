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

# Kubevirt notes
helm upgrade --install gpu-operator nvidia/gpu-operator -n gpu-operator --create-namespace --set operator.upgradeCRD=true --disable-openapi-validation --set sandboxWorkloads.enabled=true
```

```
# if you can access to nvid.nvidia.com..... Build nVidia vGPU (for kubevirt use case )
Download the vGPU Software from the NVIDIA Licensing Portal. https://nvid.nvidia.com/dashboard/#/dashboard
https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/latest/gpu-operator-kubevirt.html#build-vgpu-manager-image
# Install the GPU Operator (without NVIDIA vGPU)
helm .........  --set sandboxWorkloads.enabled=true
```

```
# Host preparation for PCI Passthrough
https://kubevirt.io/user-guide/virtual_machines/host-devices/#host-preparation-for-pci-passthrough


# kubevirt install https://kubevirt.io/quickstart_cloud/
echo $VERSION
v1.0.0
kubectl apply -f https://github.com/kubevirt/kubevirt/releases/download/${VERSION}/kubevirt-operator.yaml
kubectl apply -f https://github.com/kubevirt/kubevirt/releases/download/${VERSION}/kubevirt-cr.yaml


# uninstall
kubectl delete -f https://github.com/kubevirt/kubevirt/releases/download/${VERSION}/kubevirt-cr.yaml
kubectl delete -f https://github.com/kubevirt/kubevirt/releases/download/${VERSION}/kubevirt-operator.yaml
```


```
# Kubevirt and nVidia pGPU ( passthrough GPU )

https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/latest/gpu-operator-kubevirt.html


https://kubevirt.io/user-guide/virtual_machines/host-devices/#listing-permitted-devices

# Get device ID
####
ssh worker-gpu-1
lspci -nnv|grep -i nvidia
21:00.0 VGA compatible controller [0300]: NVIDIA Corporation GM204GL [Tesla M6] [10de:13f3] (rev a1) (prog-if 00 [VGA controller])
	Subsystem: NVIDIA Corporation GM204GL [Tesla M6] [10de:1143]
	Kernel driver in use: nvidia
	Kernel modules: nvidiafb, nouveau

lspci -DD|grep NVIDIA
0000:21:00.0 VGA compatible controller: NVIDIA Corporation GM204GL [Tesla M6] (rev a1)

####
ssh worker-gpu-2
23:00.0 VGA compatible controller [0300]: NVIDIA Corporation GM204GL [Tesla M6] [10de:13f3] (rev a1) (prog-if 00 [VGA controller])
	Subsystem: NVIDIA Corporation GM204GL [Tesla M6] [10de:1143]
	Kernel driver in use: nvidia
	Kernel modules: nvidiafb, nouveau
26:00.0 VGA compatible controller [0300]: NVIDIA Corporation GM204GL [Tesla M6] [10de:13f3] (rev a1) (prog-if 00 [VGA controller])
	Subsystem: NVIDIA Corporation GM204GL [Tesla M6] [10de:1143]
	Kernel driver in use: nvidia
	Kernel modules: nvidiafb, nouveau

lspci -DD|grep NVIDIA
0000:23:00.0 VGA compatible controller: NVIDIA Corporation GM204GL [Tesla M6] (rev a1)
0000:26:00.0 VGA compatible controller: NVIDIA Corporation GM204GL [Tesla M6] (rev a1)

####

# Add device ID to permittedHostDevices
( https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/latest/gpu-operator-kubevirt.html#add-gpu-resources-to-kubevirt-cr )

k get kubevirts.kubevirt.io -n kubevirt kubevirt -o yaml

kubectl edit kubevirts.kubevirt.io -n kubevirt
...
spec:
  configuration:
  developerConfiguration:
    featureGates:
    - GPU
  permittedHostDevices:
    pciHostDevices:
    - externalResourceProvider: true
      pciVendorSelector: 10DE:13f3
      resourceName: nvidia.com/GM204GL
#    mediatedDevices:
#    - externalResourceProvider: true
#      mdevNameSelector: NVIDIA A10-24Q
#      resourceName: nvidia.com/NVIDIA_A10-24Q
...

```

```
# patching kubevirt kind
kubectl patch kubevirt  -n kubevirt kubevirt --type='json' \
        -p='[{"op": "add", "path": "/spec/configuration/developerConfiguration/featureGates/-", "value": "DisableMDEVConfiguration" }]'
The request is invalid: the server rejected our request due to an error in our request

kubectl patch kubevirt  -n kubevirt kubevirt --type=merge \
        -p='[{"op": "add", "path": "/spec/configuration/developerConfiguration/featureGates/-", "value": "DisableMDEVConfiguration" }]'
The request is invalid: patch: Invalid value: "[{\"op\":\"add\",\"path\":\"/spec/configuration/developerConfiguration/featureGates/-\",\"value\":\"DisableMDEVConfiguration\"}]": couldn't get version/kind; json parse error: json: cannot unmarshal array into Go value of type struct { APIVersion string "json:\"apiVersion,omitempty\""; Kind string "json:\"kind,omitempty\"" }

# Nested virtualization
kubectl patch kubevirt -n kubevirt  kubevirt --type=merge \
        --patch '{"spec":{"configuration":{"developerConfiguration":{"useEmulation":true}}}}'
```

```
# label worker GPU nodes ( here to allo vm-passthrough )
kubectl label node worker-gpu-1 --overwrite nvidia.com/gpu.workload.config=vm-passthrough
kubectl label node worker-gpu-1 --overwrite nvidia.com/gpu.workload.config=vm-passthrough


```
