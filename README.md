```
apiVersion: fleet.cattle.io/v1alpha1
kind: GitRepo
metadata:
  name: test
  namespace: fleet-default
spec:
  branch: master
  correctDrift: {}
  paths:
    - /cni-addons
  pollingInterval: 1m0s
  repo: https://github.com/ryanelliottsmith/test-fleet-cilium.git
  targets:
    - clusterName: test-fleet
```
