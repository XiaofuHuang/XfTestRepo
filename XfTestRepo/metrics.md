# Metric
A summary of what happened during a particular time period. 

In feature utilization, this telemetry is usually used to track the file level activity.
For example, every single error thrown by build is rarely of interest because you can always get that info in build report.
Instead a summary of errors happened every for example 1 minute is important. 

Besides customDimensions, we also provide "value" attribute which is the sum of the measurement.
For example, in last half hour, there are two records for build errors and their "value" are 20 and 30 separately, you can know based on that there are totally 50 errors thrown by build in last half hour.

## Supported Metrics
| Metric name | Description | Examples | All Properties |
|-------------|-------------|----------|----------------|
|BuildError|A summary of errors/warnings/suggestions happened during a particular time period|[Get error/warning/suggestion count for each build within 24 hours](#get-errorwarningsuggestion-count-for-each-build-within-24-hours)|[Detail](#builderror-properties)|


## Examples
### Get error/warning/suggestion count for each build within 24 hours
```
querymetrics
| where name == \"test-builderror\"
| where timestamp > now(-2d)
| summarize sum(value) by errorlevel = tostring(customDimensions.errorlevel), buildid = tostring(customDimensions.buildid)
```



## All Properties
### BuildError Properties
Empty

