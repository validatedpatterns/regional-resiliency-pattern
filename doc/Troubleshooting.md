# Troubleshooting the Regional Resiliency Pattern

## ODF Requires Signed Certificates

[RedHat OpenShift Data Foundation][odf] exposes _S3_ endpoints as an exchange point of information
for the apps being managed by [Regional DR][regional]. These endpoints must be secured, and a
certificate needs to be signed by a Well-Known Certificate Authority.

If not in production, you may opt for a third-party solution, such as _Let's Encrypt_ or the
_Acme Operator_. Just update the _S3_ Route with the new trustable certificate.

Check for certificate issues in your _DR Policy_ resources:<br/>
<img src="https://raw.githubusercontent.com/RHEcosystemAppEng/regional-resiliency-pattern/main/doc/drpolicy-certificate-err.png" width="664" height="80" alt="">

And in the underlying _DR Cluster_ resources:<br/>
<img src="https://raw.githubusercontent.com/RHEcosystemAppEng/regional-resiliency-pattern/main/doc/drcluster-certificate-err.png" width="1131" height="470" alt="">

[odf]: https://access.redhat.com/documentation/en-us/red_hat_openshift_data_foundation/4.14
[regional]: https://access.redhat.com/documentation/en-us/red_hat_openshift_data_foundation/4.14/html/configuring_openshift_data_foundation_disaster_recovery_for_openshift_workloads/rdr-solution
