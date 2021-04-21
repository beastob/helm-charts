# Helm Charts

Add chart repository
```shell
$ helm repo add beastob https://beastob.github.io/helm-charts/
```

## Install Charts
[Install Folding@Home via Helm](foldingathome)


## Publish Chart

```shell
$ helm package foldingathome
$ mv foldingathome-*.tgz docs
$ helm repo index docs --url https://beastob.github.io/helm-charts/
```