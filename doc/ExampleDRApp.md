# RegionalDR Example Application

This document explains how to deploy an application for testing the RegionalDR
Pattern

The application deploys two pods (pacman-app, MongoDB). The MongoDB instance
mounts a 5GiB PVC which will be replicated between active and passive clusters.

## Deployment

1. Check the DRHUB Operator is well configured
![DR-HUB-CR-STATUS][DR-HUB-CR-STATUS]

2. Before creating the app, consider the changes you made in your values file, and
adapt this app for your setup. Open the `03-drpc.yaml` file and check the lines:

    ```yaml
    ---
    apiVersion: ramendr.openshift.io/v1alpha1
    kind: DRPlacementControl
    metadata:
      labels:
        app: pacman
        cluster.open-cluster-management.io/backup: resource
      name: pacman-placement-1-drpc
      annotations:
      namespace: pacman
    spec:
      preferredCluster: <YOUR_PRIMARY_CLUSTER_NAME>
      failoverCluster: <YOUR_SECONDARY_CLUSTER_NAME>
      drPolicyRef:
        name: <YOUR_DR_POLICY_NAME>
      placementRef:
        kind: PlacementRule
        name: pacman-placement-1
        namespace: pacman
      pvcSelector: {}
    ```

    :warning: If you didn't change the `values-resilient.yaml` file, you don't need
    to follow this step. The example value is already prepared to work with the
    pre-defined resilient values.

3. For deploying the Pacman app, connect your `oc` CLI to the Hub Cluster and run
the following commands:

```sh
oc apply -f ./rdr-example-app
```

## Verify App

1. Once the app is deployed, wait a few minutes and check the DRPC to check the
status of our app

```sh
oc get drpc -n pacman
# NAME                      AGE   PREFERREDCLUSTER   FAILOVERCLUSTER DESIREDSTATE   CURRENTSTATE
# pacman-placement-1-drpc   45m   ocp-primary        ocp-secondary                  Deployed
```

This can be checked also on the ACM Applications section, or in the DRHub
Operator page, and get the DRPC details:
![DRPC-DEPLOYED][DRPC-DEPLOYED]

Access the Pacman Route on the primary cluster

```sh
oc get route pacman -n pacman -o jsonpath='{.spec.host}'
```

![PACMAN-FIRST-PLAY][PACMAN-FIRST-PLAY]

Play until you loose, and save your record. We will use that info to demonstrate
the data was sync correctly later.

![PACMAN-RESULTS][PACMAN-RESULTS]

## Trigger Failover

Triggering the Failover process is so easy, and it can be done in two different
ways: manually patching the DRPC, or simulating an infrastructure issue and
using the automatic failover trigger included with this pattern.

### Automatic Triggering

The pattern includes an operator deployed on the `regional-dr-trigger` namespace
on the HUB cluster. This operator checks the `ManagedClusters` objects, and if it
detects that any of them is reporting **DOWN**, the operator will look for the
DRPCs deployed on it, and activate the Failover process automatically for not
depending on human intervention. As the status of the `ManagedCluster` is reported
by its `kubelet`, if you remove the pod on the primary cluster, it will be
considered **DOWN** after 5 min, and the `regional-dr-trigger` will launch the
DR operation.

You can do it running the following command on the **primary cluster**:

```sh
oc scale deployment klusterlet-agent --replicas=0 -n open-cluster-management-agent
```

### Manual Triggering

For testing the DR manually, just patch the DRPC object with the following command:

```sh
oc patch drpc pacman-placement-1-drpc -n pacman --type merge -p '{"spec": {"action": "Failover"}}'
```

![DRPC-FAILINGOVER][DRPC-FAILINGOVER]

Wait until the application is failed over correctly

![DRPC-FAILEDOVER][DRPC-FAILEDOVER]

## Check data was migrated correctly

Access the Pacman Route on the secondary cluster

```sh
oc get route pacman -n pacman -o jsonpath='{.spec.host}'
```

On the main menu, click on "High Score", and you will see how the scores of the
Pacman game still having the same records when it was running on the Primary
cluster.
![PACMAN-AFTER-DR][PACMAN-AFTER-DR]
![PACMAN-RESULTS][PACMAN-RESULTS]

<!--LINKS-->
[DR-HUB-CR-STATUS]: ./rdr-example-app-images/dr-hub-cr-status.png
[DRPC-DEPLOYED]: ./rdr-example-app-images/drpc-deployed.png
[DRPC-FAILINGOVER]: ./rdr-example-app-images/drpc-failingover.png
[DRPC-FAILEDOVER]: ./rdr-example-app-images/drpc-failedover.png
[PACMAN-FIRST-PLAY]: ./rdr-example-app-images/pacman-first-play.png
[PACMAN-RESULTS]: ./rdr-example-app-images/pacman-results.png
[PACMAN-AFTER-DR]: ./rdr-example-app-images/pacman-after-dr.png
