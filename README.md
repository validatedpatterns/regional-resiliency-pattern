# Regional Resiliency Pattern

*Regional Resiliency Pattern* is a [Validated Pattern][vp] derived from the [Multicluster DevSevOps Pattern][devsecops].
Including the [Regional DR][regional] solution from [Red Hat OpenShift Data Foundation][odf] and the
[Regional DR Trigger Operator][rdrtrigger]. Used for automated application failover between
[Red Had Advanced Cluster Management][acm] _Managed Clusters_.

<details>
<summary>Click for operator update instructions</summary>
<p>
The <em>Regional DR Operator</em> chart is in <a href="charts/hub/rdrtrigger">charts/hub/rdrtrigger</a>. We use
[git subtree][subtree], our target is the <a href="https://githuc.com/RHEcosystemAppEng/regional-dr-trigger-operator-chart">original chart</a>.
We can update it using the following command:

```shell
# replace ref with the target reference
git subtree pull --prefix=charts/hub/rdrtrigger \
https://github.com/RHEcosystemAppEng/regional-dr-trigger-operator-chart.git ref --squash
```

</p>
</details>

<!--LINKS-->
[acm]: https://www.redhat.com/en/technologies/management/advanced-cluster-management
[devsecops]: https://validatedpatterns.io/patterns/devsecops/
[odf]: https://access.redhat.com/documentation/en-us/red_hat_openshift_data_foundation/4.14
[rdrtrigger]: https://githuc.com/RHEcosystemAppEng/regional-dr-trigger-operator-chart
[regional]: https://access.redhat.com/documentation/en-us/red_hat_openshift_data_foundation/4.14/html/configuring_openshift_data_foundation_disaster_recovery_for_openshift_workloads/rdr-solution
[subtree]: https://docs.github.com/en/get-started/using-git/about-git-subtree-merges
[vp]: https://validatedpatterns.io/
