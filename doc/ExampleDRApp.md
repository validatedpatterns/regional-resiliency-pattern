# Deploy an example DR Application

Grab the manifests in the [example app folder][example-app-folder], **modify** the
_DRPlacementControl_, set the preferred and failover cluster and the target dr policy.

```yaml
---
apiVersion: ramendr.openshift.io/v1alpha1
kind: DRPlacementControl
metadata:
  name: busybox-rdr
  ...
spec:
  preferredCluster: <PRIMARY_CLUSTER_NAME_GOES_HERE>
  failoverCluster: <SECONDARY_CLUSTER_NAME_GOES_HERE>
  drPolicyRef:
    name: <DR_POLICY_NAME_GOES_HERE>
  placementRef:
    ...
  pvcSelector: {}
```

After applying, you should see the _busybox_ app running the primary cluster. If the primary cluster reports unavailability, the Pattern will trigger a failover to the secondary cluster.

[example-app-folder]: https://github.com/RHEcosystemAppEng/regional-resiliency-pattern/blob/main/docs/example-app
