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
|statuscode|The validation result in Broken Link Service|[Get broken links count per run](#get-broken-links-count-per-run)|[Detail](#statuscode-properties)|
|linktotal|The validated link count in Broken Link Service|[Get validated links count per run](#get-validated-links-count-per-run)|[Detail](#linktotal-properties)|
|depotcount|The depot count in Broken Link Service|[Get depot count per run](#get-depot-count-per-run)|[Detail](#depotcount-properties)|


## Examples
### Get broken links count per run
```
querymetrics
| where MetricName == "statuscode" and Properties.code <> "OK"
| summarize badLinkCount = sum(Value) by runid = tostring(Properties.runid)

```

### Get validated links count per run
```
querymetrics
| where MetricName == "linktotal"
| summarize linkCount = sum(Value) by runid = tostring(Properties.runid)

```

### Get depot count per run
```
querymetrics
| where MetricName == "depotcount"
| summarize depotCount = sum(Value) by runid = tostring(Properties.runid)

```



## All Properties
### statuscode Properties
| Property name | Type |
|---------------|------|
|_MS.AggregationIntervalMs|string|
|runid|guid|
|ProcessId|string|
|locale|string|
|InvocationId|guid|
|LogLevel|string|
|Category|string|
|branch|string|
|depot|string|
|host|dynamic|
|type|string|
|code|string|


### linktotal Properties
| Property name | Type |
|---------------|------|
|_MS.AggregationIntervalMs|string|
|runid|guid|
|ProcessId|string|
|locale|string|
|InvocationId|guid|
|LogLevel|string|
|Category|string|
|branch|string|
|depot|string|


### depotcount Properties
| Property name | Type |
|---------------|------|
|runid|guid|
|ProcessId|string|
|_MS.AggregationIntervalMs|string|
|InvocationId|guid|
|LogLevel|string|
|Category|string|
|runtype|string|



