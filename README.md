# Regional Resiliency Pattern

The _Regional Resiliency Pattern_ is a [Validated Pattern][vp] derived from the
[Multicluster DevSevOps Pattern][devsecops].<br/>
The pattern uses [Red Hat OpenShift Data Foundation][odf]'s [Regional DR][regional] solution for
cross-regional disaster recovery and the [Regional DR Trigger Operator][rdrtrigger] for automating
applications failover between [Red Had Advanced Cluster Management][acm] _Managed Clusters_ in
active-passive scenarios.

## Prerequisites

- [Red Hat OpenShift][openshift] cluster.
- [Red Hat OpenShift CLI][oc] installed on your workstation.
<!-- TODO add credentials requirements -->
- [Cloud Vendor Credentials]

> :warning: Do not install the _Regional Resiliency Pattern_ in a pre-configured
> [Advanced Cluster Management][acm] setup.

## Installation

1. Fork the pattern and clone the fork to your workstation.

2. In [values-global.yaml](values-global.yaml), create your cluster pairs. _install_config_ is
   documented [here][install_config]. Use the existing example as a base:

   ```yaml
    regionalDR:
     - name: resilient # Matches with ClusterSet
       globalnetEnabled: false # Support for overlapping CIDR
       clusters: # Pair of clusters, make sure to create each in a different region
       primary:
         name: ocp-primary
         version: 4.14.12
         install_config: # See OpenShift documentation for install config spec
           ...
       secondary:
         name: ocp-secondary
         version: 4.14.12
         install_config: # See OpenShift documentation for install config spec
            ...
       # List of sync policy intervals: RPO should be larger than the sync time.
       # If your sync process takes 3 mins, the interval should be at least 4-5 mins.
       intervals:
        - 1m
        - 2m
   ```

3. Push your changes to your fork.

4. Create your own _values-secrets.yaml_ file, use
   [values-secret.yaml.template](values-secret.yaml.template) as a base.

   > :warning: DO NOT push this file to any repository.

5. Use [OpenShift CLI][oc] and log into the _Hub Cluster_.

   > :detective: Now will be an excellent time to examine [values-hub.yaml](values-hub.yaml) and
   > and [values-resilient.yaml](values-resilient.yaml), and evaluate what's included in Pattern for
   > the _Hub_ and _Managed Clusters_ respectively.

6. Install the pattern. The following will install everything described by the pattern, including namespaces,
   operators, subscriptions, etc.

   ```shell
   # Check and verify the generated pattern object
   ./pattern.sh make show

   # If everything looks good, install the pattern (if needed, use TARGET_ORIGIN and TARGET_BRANCH)
   ./pattern.sh make install
   ```

7. Obtain the [RedHat GitOps][gitops] routes, and access them. There are two different servers. One
   for the _Pattern Framework_ and one for our _Pattern Instance_:

   ```sh
   # Framework GitOps
   oc get Route -n openshift-gitops openshift-gitops-server

   # Pattern GitOps
   oc get Route -n regional-resiliency-pattern-hub hub-gitops-server
   ```

8. Verify the Framework's _GitOps_ server:<br/>
   <img src="https://raw.githubusercontent.com/RHEcosystemAppEng/regional-resiliency-pattern/main/doc/hub-framework-gitops.png" width="670" height="448" alt="">

9. Observe the Pattern's _GitOps_ server and wait for all resources to be reconciled.

   > Depending on the target vendor's resources, creating and configuring the clusters might take
   > some time.

10. In [ACM][acm]'s UI, verify _Submariner_ is healthy:<br/>
    <img src="https://raw.githubusercontent.com/RHEcosystemAppEng/regional-resiliency-pattern/main/doc/submariner.png" width="782" height="352" alt="">

11. In [ACM][acm]'s UI, verify the _Managed Clusters_ are healthy:<br/>
    <img src="https://raw.githubusercontent.com/RHEcosystemAppEng/regional-resiliency-pattern/main/doc/clusters.png" width="907" height="314" alt="">

12. In the _Hub_'s UI, verify the _DR Policies_ are validated match your requirements:<br/>
    <img src="https://raw.githubusercontent.com/RHEcosystemAppEng/regional-resiliency-pattern/main/doc/drpolicies.png" width="480" height="327" alt="">

13. For every _Managed Cluster_, obtain the [RedHat GitOps][gitops] routes, and access them. There
    are two different servers. One for the _Pattern Framework_ and one for our _Pattern Instance_:

    ```sh
    # Framework GitOps
    oc get Route -n openshift-gitops openshift-gitops-server

    # Pattern GitOps
    oc get Route -n regional-resiliency-pattern-resilient resilient-gitops-server
    ```

14. For every _Managed Cluster_, verify the Framework's _GitOps_ server:<br/>
    <img src="https://raw.githubusercontent.com/RHEcosystemAppEng/regional-resiliency-pattern/main/doc/mc-framework-gitops.png" width="316" height="167" alt="">

15. For every _Managed Cluster_, verify the Pattern's _GitOps_ server:<br/>
    <img src="https://raw.githubusercontent.com/RHEcosystemAppEng/regional-resiliency-pattern/main/doc/mc-pattern-gitops.png" width="316" height="166" alt="">

All Done.<br/>
Get started deploying _DR Applications_ with the [Regional DR][regional] docs and the
[example application][example-app]. Based on the _Primary Managed Cluster_ availability maintained
by [ACM][acm]'s heartbeat mechanism, a failover will be triggered to all related applications.

## Further Reading

- [Deploy an example DR Application](./rdr-example-app/README.md)
- [Maintain the Regional Resiliency Pattern][maintenance]
- [Troubleshooting the Regional Resiliency Pattern][troubleshooting]
- [Uninstalling the Regional Resiliency Pattern][uninstalling]

<!--LINKS-->
[acm]: https://www.redhat.com/en/technologies/management/advanced-cluster-management
[devsecops]: https://validatedpatterns.io/patterns/devsecops/
[example-app]: https://github.com/RHEcosystemAppEng/regional-resiliency-pattern/blob/main/docs/ExampleDRApp.md
[install_config]: https://docs.openshift.com/container-platform/4.14/installing/installing_aws/installing-aws-customizations.html
[gitops]: https://www.redhat.com/en/technologies/cloud-computing/openshift/gitops
[maintenance]: https://github.com/RHEcosystemAppEng/regional-resiliency-pattern/blob/main/docs/Maintenance.md
[oc]: https://docs.openshift.com/container-platform/4.14/cli_reference/openshift_cli/getting-started-cli.html
[odf]: https://access.redhat.com/documentation/en-us/red_hat_openshift_data_foundation/4.14
[openshift]: https://www.redhat.com/en/technologies/cloud-computing/openshift
[rdrtrigger]: https://github.com/RHEcosystemAppEng/regional-dr-trigger-operator-chart
[regional]: https://access.redhat.com/documentation/en-us/red_hat_openshift_data_foundation/4.14/html/configuring_openshift_data_foundation_disaster_recovery_for_openshift_workloads/rdr-solution
[troubleshooting]: https://github.com/RHEcosystemAppEng/regional-resiliency-pattern/blob/main/docs/Troubleshooting.md
[uninstalling]: https://github.com/RHEcosystemAppEng/regional-resiliency-pattern/blob/main/docs/Uninstalling.md
[vp]: https://validatedpatterns.io/
