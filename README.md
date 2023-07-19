```
# Uninstall operator
helm delete gpu-operator -n gpu-operator 

k delete -f nvidia.com_clusterpolicies_crd.yaml
customresourcedefinition.apiextensions.k8s.io "clusterpolicies.nvidia.com" deleted

# Install operator
helm repo add nvidia https://helm.ngc.nvidia.com/nvidia    && helm repo update

helm upgrade --install gpu-operator nvidia/gpu-operator -n gpu-operator --set operator.upgradeCRD=true -f values-22.9.1.yaml
# or
helm upgrade --install gpu-operator nvidia/gpu-operator -n gpu-operator --create-namespace \
             --set operator.upgradeCRD=true --disable-openapi-validation

#  for Kubevirt 
helm upgrade --install gpu-operator nvidia/gpu-operator -n gpu-operator --create-namespace \
             --set operator.upgradeCRD=true --disable-openapi-validation \
             --set sandboxWorkloads.enabled=true
```


