# Uninstalling the Regional Resiliency Pattern

:warning: Do not try to delete pattern underlying objects manually. [GitOps][gitops] will attempt a
resync. Underlying objects not deleted in the correct order might end up as dangling objects,
blocking further deployments.

Follow these steps for uninstalling the pattern:

1. In the **framework**'s [GitOps][gitops] server, disable auto-sync for the Regional DR pattern.

2. In the **pattern**'s [GitOps][gitops] server, remove the `regional-dr` Application.

   > Depending on your environment, this process can take several minutes.

3. Once the `regional-dr` Application is correctly uninstalled, it will disappear from the
   framework's _GitOps server.

If the uninstalling process takes more than 10 minutes. Check your _MirrorPeer_ resources and the
_ODF Multicluster Orchestrator Operator_ for leads.

[gitops]: https://www.redhat.com/en/technologies/cloud-computing/openshift/gitops
