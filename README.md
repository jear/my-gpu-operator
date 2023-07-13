```

(base) ubuntu@my-ubuntu:~/workspace/github/my-gpu-operator$  helm delete gpu-operator -n gpu-operator 

(base) ubuntu@my-ubuntu:~/workspace/github/my-gpu-operator$ k delete -f nvidia.com_clusterpolicies_crd.yaml
customresourcedefinition.apiextensions.k8s.io "clusterpolicies.nvidia.com" deleted

(base) ubuntu@my-ubuntu:~/workspace/github/my-gpu-operator$ helm upgrade --install gpu-operator nvidia/gpu-operator -n gpu-operator --set operator.upgradeCRD=true -f values-22.9.1.yaml
Release "gpu-operator" does not exist. Installing it now.
NAME: gpu-operator
LAST DEPLOYED: Sat Mar 11 17:09:34 2023
NAMESPACE: gpu-operator
STATUS: deployed
REVISION: 1
TEST SUITE: None
```
