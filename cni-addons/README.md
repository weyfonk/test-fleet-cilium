# Cilium CNI Addons

_[ToC]_

## Restart existing pods

- This job is using a script to identify Cilium Endpoints that are not associated with any pods within each namespace in a Kubernetes cluster then restart it.

- Original Script (only get pods) can be helpful for troubleshooting or auditing purposes to ensure that Cilium networking is correctly configured.

    ```sh
    #!/bin/bash

    for ns in $(kubectl get ns -o jsonpath='{.items[*].metadata.name}'); do
         ceps=$(kubectl -n "${ns}" get cep \
             -o jsonpath='{.items[*].metadata.name}')
         pods=$(kubectl -n "${ns}" get pod \
             -o custom-columns=NAME:.metadata.name,NETWORK:.spec.hostNetwork \
             | grep -E '\s(<none>|false)' | awk '{print $1}' | tr '\n' ' ')
         ncep=$(echo "${pods} ${ceps}" | tr ' ' '\n' | sort | uniq -u | paste -s -d ' ' -)
         for pod in $(echo $ncep); do
           echo "${ns}/${pod}";
         done
    done
    ```

### Verify All Pods Managed by Cilium

- Check pod logs, either you will see a pod restart or something like below:

  ```sh
  All Pods in namespace cattle-fleet-system are managed by Cilium
  All Pods in namespace cattle-gatekeeper-system are managed by Cilium
  All Pods in namespace cattle-impersonation-system are managed by Cilium
  All Pods in namespace cattle-system are managed by Cilium
  All Pods in namespace cert-manager are managed by Cilium
  All Pods in namespace chimera-ops are managed by Cilium
  All Pods in namespace cilium-test are managed by Cilium
  All Pods in namespace cloudability are managed by Cilium
  All Pods in namespace datadog-system are managed by Cilium
  All Pods in namespace default are managed by Cilium
  All Pods in namespace edns-system are managed by Cilium
  All Pods in namespace ingress-nginx are managed by Cilium
  All Pods in namespace k6-loadtesting are managed by Cilium
  All Pods in namespace k6-operator-system are managed by Cilium
  All Pods in namespace kube-node-lease are managed by Cilium
  All Pods in namespace kube-public are managed by Cilium
  All Pods in namespace kube-system are managed by Cilium
  All Pods in namespace local are managed by Cilium
  All Pods in namespace mai-playground are managed by Cilium
  All Pods in namespace np-test are managed by Cilium
  All Pods in namespace np-test2 are managed by Cilium
  All Pods in namespace secrets-system are managed by Cilium
  All Pods in namespace velero are managed by Cilium
  All Pods in namespace xcr-aqua are managed by Cilium
  All Pods in namespace xcr-argocd are managed by Cilium
  All Pods in namespace xcr-proportional-autoscaler are managed by Cilium
  All Pods in namespace xcr-reloader are managed by Cilium
  All Pods in namespace xcr-test-01 are managed by Cilium
  All Pods in namespace xcr-test-app are managed by Cilium
  All Pods in namespace xcr-test-wp are managed by Cilium
  ```

- You can use Cilium cli and command `cilium status`, notice line `Cluster Pods`

  ```sh
  $ cilium status
      /¯¯\
   /¯¯\__/¯¯\    Cilium:             OK
   \__/¯¯\__/    Operator:           OK
   /¯¯\__/¯¯\    Envoy DaemonSet:    disabled (using embedded mode)
   \__/¯¯\__/    Hubble Relay:       OK
      \__/       ClusterMesh:        disabled

  Deployment             cilium-operator    Desired: 2, Ready: 2/2, Available: 2/2
  DaemonSet              cilium             Desired: 4, Ready: 4/4, Available: 4/4
  Deployment             hubble-ui          Desired: 1, Ready: 1/1, Available: 1/1
  Deployment             hubble-relay       Desired: 1, Ready: 1/1, Available: 1/1
  Containers:            cilium             Running: 4
                         cilium-operator    Running: 2
                         hubble-ui          Running: 1
                         hubble-relay       Running: 1
  Cluster Pods:          72/72 managed by Cilium
  Helm chart version:
  Image versions         cilium             quay.io/cilium/cilium:v1.15.4@sha256:b760a4831f5aab71c711f7537a107b751d0d0ce90dd32d8b358df3c5da385426: 4
                         cilium-operator    quay.io/cilium/operator-generic:v1.15.4@sha256:404890a83cca3f28829eb7e54c1564bb6904708cdb7be04ebe69c2b60f164e9a: 2
                         hubble-ui          quay.io/cilium/hubble-ui:v0.13.0@sha256:7d663dc16538dd6e29061abd1047013a645e6e69c115e008bee9ea9fef9a6666: 1
                         hubble-ui          quay.io/cilium/hubble-ui-backend:v0.13.0@sha256:1e7657d997c5a48253bb8dc91ecee75b63018d16ff5e5797e5af367336bc8803: 1
                         hubble-relay       quay.io/cilium/hubble-relay:v1.15.4@sha256:03ad857feaf52f1b4774c29614f42a50b370680eb7d0bfbc1ae065df84b1070a: 1
  ```

## Hubble Ingress

- Cilium Helm Chart doesn't enable hubble UI by default.

## Reference

- <https://docs.cilium.io/en/stable/installation/cni-chaining-aws-cni/#restart-existing-pods>
- <https://medium.com/@norlin.t/cilium-with-ingress-opentelemetry-and-l7-policies-44522e349abe>
