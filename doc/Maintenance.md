# Maintain the Regional Resiliency Pattern

## Update a secret post-installation

After any _Secret_ update, you **must** refresh the pattern:

```shell
./pattern.sh make post-install
```

:exclamation: You might need to remove the ExternalSecrets to generate a Secret
with the updated values. Check your ExternalSecrets and its refreshing period.

## Update Regional DR Trigger Operator

The _Regional DR Operator_ chart is in [charts/hub/rdrtrigger][target-chart]. We use
[git subtree][subtree], our target is the [original chart][rdrtrigger].
We can update it using the following command:

```shell
# replace ref with the target reference
git subtree pull --prefix=charts/hub/rdrtrigger \
https://github.com/RHEcosystemAppEng/regional-dr-trigger-operator-chart.git ref --squash
```

[target-chart]: https://github.com/RHEcosystemAppEng/regional-resiliency-pattern/blob/main/charts/hub/rdrtrigger
[rdrtrigger]: https://github.com/RHEcosystemAppEng/regional-dr-trigger-operator-chart
[subtree]: https://docs.github.com/en/get-started/using-git/about-git-subtree-merges
