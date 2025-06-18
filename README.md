# Template values Helm chart

Have you ever wanted to include your own extra templates into an existing Helm chart, but it didn't officially support it?

This chart may be what you are looking for. No application, just an empty canvas for you to deploy whatever you want with your Helm charts.

No more having to run kustomize before deploying a chart to deploy those extra manifests before deploying your chart.

You can use this chart as a dependency to create your own chart without having to fork it (kind of).
You could create an empty chart that has both the chart you want to use and this `template` as dependencies, and use like this:


```
# Chart.yaml
dependencies:
- name: example
  repository: <your-charts-repo>
- name: template
  repository: oci://ghcr.io/migruiz4/template-values-chart
  version: 0.1.0
```

Then, in the values.yaml, define this:
```
# Your app settings
example:
    ...
# Any extra templates you would like to deploy
template:
    extraDeploy:
       - |
         apiVersion: v1
         kind: ConfigMap
         metadata:
           name: {{ .Release.Name }}
           namespace: {{ .Release.Namespace }}
         ...
```


Alternatively, using tools like `helmfile` directly extend the dependencies using:

```
releases:
- name: example
  chart: <your-chart>
  dependencies:
  - name: template
    chart: oci://ghcr.io/migruiz4/template-values-chart/template
    version: 0.1.0
```

## Important

It is important to understand that subcharts (dependencies) share helper functions but can not access the parent chart's values.

If you'd like to pass values to this subchart, you will need to do it using 'global' or using the subchart values section aswell.
