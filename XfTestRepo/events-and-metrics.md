# Available Events / Metrics

## Supported Events
One occurrence of something happening in the system, often the completion of some task (like build) or a goal achieved in the task (like successfully get build config).
In feature utilization, we provide properties related to this event in customDimensions attribute of each record. For example, build id, repo id, build status, build start time/end time are the properties of build completion event and you can filter, search, segment by using these properties.

| Service | Description | Event name | Examples | All Properties | All Metrics |
|---------|-------------|------------|----------|----------------|-------------|
|OPSBuild|Completion of Commit(Branch) build, and the related build errors, build status, build files.|Commit.Publish.Complete|[Get build duration details for each commit build](#get-build-duration-details-for-each-commit-build)|[Detail](#commitpublishcomplete-properties)|[Detail](#commitpublishcomplete-metrics)|
|OPSBuild|Completion of Pull Request build, and the related build errors, build status, build files.|PullRequest.Build.Complete|[Get build error type details for each pull request build](#get-build-error-type-details-for-each-pull-request-build)|[Detail](#pullrequestbuildcomplete-properties)|[Detail](#pullrequestbuildcomplete-metrics)|
|OPSBuild|Completion of pull request build and publish, and the related build errors, build status, build and publish files.|PullRequest.Publish.Complete|[Get published file details for each pull request build](#get-published-file-details-for-each-pull-request-build)|[Detail](#pullrequestpublishcomplete-properties)|[Detail](#pullrequestpublishcomplete-metrics)|
|OPSBuild|Commit(Branch) Build + Publish Report Information like build report url, incremental and docfx version.|Commit.Publish.Report|[Get Commit Build report, incremental and version info](#get-commit-build-report-incremental-and-version-info)|[Detail](#commitpublishreport-properties)|[Detail](#commitpublishreport-metrics)|
|OPSBuild|Pull Request Build Report Information like build report url, incremental and docfx version.|PullRequest.Build.Report|[Get Pull Request Build report, incremental and version info](#get-pull-request-build-report-incremental-and-version-info)|[Detail](#pullrequestbuildreport-properties)|[Detail](#pullrequestbuildreport-metrics)|
|OPSBuild|Pull Request Build + Publish Report Information like build report url, incremental and docfx version.|PullRequest.Publish.Report|[Get Build report, incremental and version info](#get-build-report-incremental-and-version-info)|[Detail](#pullrequestpublishreport-properties)|[Detail](#pullrequestpublishreport-metrics)|


## Supported Metrics
A summary of what happened during a particular time period. 

In feature utilization, this telemetry is usually used to track the file level activity.
For example, every single error thrown by build is rarely of interest because you can always get that info in build report.
Instead a summary of errors happened every for example 1 minute is important. 

Besides customDimensions, we also provide "value" attribute which is the sum of the measurement.
For example, in last half hour, there are two records for build errors and their "value" are 20 and 30 separately, you can know based on that there are totally 50 errors thrown by build in last half hour.

| Service | Description | Metric name | Examples | All Properties |
|---------|-------------|-------------|----------|----------------|
|BrokenLinkService|The validation result in Broken Link Service|statuscode|[Get broken links count per run](#get-broken-links-count-per-run)|[Detail](#statuscode-properties)|
|BrokenLinkService|The validated link count in Broken Link Service|linktotal|[Get validated links count per run](#get-validated-links-count-per-run)|[Detail](#linktotal-properties)|
|BrokenLinkService|The depot count in Broken Link Service|depotcount|[Get depot count per run](#get-depot-count-per-run)|[Detail](#depotcount-properties)|


## Examples
### Get build duration details for each commit build
```
queryevents
| where EventName == "Commit.Publish.Complete"
| extend JobBasicInfo = parse_json(tostring(Properties.JobBasicInfo))
| project Repository = JobBasicInfo.repo_url, Branch = JobBasicInfo.branch_name, BuildType = JobBasicInfo.build_type, BuildAction = JobBasicInfo.build_action, EventName, Timestamp, WaitingTime = Metrics["build_perf.per_stage.waiting_in_queue"].sum, PrepareTime = Metrics["build_perf.per_stage.preparing"].sum, BuildTime = Metrics["build_perf.per_stage.building"].sum, PublishTime = Metrics["build_perf.per_stage.publishing"].sum

```
### Get build error type details for each pull request build
```
queryevents
| where EventName == "PullRequest.Build.Complete"
| extend JobBasicInfo = parse_json(tostring(Properties.JobBasicInfo))
| project Repository = JobBasicInfo.repo_url, Branch = JobBasicInfo.branch_name, BuildType = JobBasicInfo.build_type, BuildAction = JobBasicInfo.build_action, EventName, Timestamp, SuggectionCount = Metrics["build_result.suggestion_count"].sum, ErrorCount = Metrics["build_result.error_count"].sum, WarningCount = Metrics["build_result.warning_count"].sum

```
### Get published file details for each pull request build
```
queryevents
| where EventName == "PullRequest.Publish.Complete"
| extend JobBasicInfo = parse_json(tostring(Properties.JobBasicInfo))
| project Repository = JobBasicInfo.repo_url, Branch = JobBasicInfo.branch_name, BuildType = JobBasicInfo.build_type, BuildAction = JobBasicInfo.build_action, EventName, Timestamp, PublishedResouceCount = Metrics["build_result.published_resource_count"].sum, PublishedContentCount = Metrics["build_result.published_content_count"].sum, PublishedTocCount = Metrics["build_result.published_toc_count"].sum, PublishedTotalCount = Metrics["build_result.published_file_count"].sum

```
### Get Commit Build report, incremental and version info
```
queryevents
| where EventName == "Commit.Publish.Report"
| extend JobBasicInfo = parse_json(tostring(Properties.JobBasicInfo))
| extend isIncremental = Properties.full_build_reason_code == 'NoReason'
| extend docfxVersion = Properties.docfx_version
| project Properties.build_report_url, isIncremental, docfxVersion, JobBasicInfo.build_id, JobBasicInfo.repo_id, JobBasicInfo.branch_name

```
### Get Pull Request Build report, incremental and version info
```
queryevents
| where EventName == "PullRequest.Build.Report"
| extend JobBasicInfo = parse_json(tostring(Properties.JobBasicInfo))
| extend isIncremental = Properties.full_build_reason_code == 'NoReason'
| extend docfxVersion = Properties.docfx_version
| project Properties.build_report_url, isIncremental, docfxVersion, JobBasicInfo.build_id, JobBasicInfo.repo_id, JobBasicInfo.branch_name

```
### Get Build report, incremental and version info
```
queryevents
| where EventName == "PullRequest.Publish.Report"
| extend JobBasicInfo = parse_json(tostring(Properties.JobBasicInfo))
| extend isIncremental = Properties.full_build_reason_code == 'NoReason'
| extend docfxVersion = Properties.docfx_version
| project Properties.build_report_url, isIncremental, docfxVersion, JobBasicInfo.build_id, JobBasicInfo.repo_id, JobBasicInfo.branch_name

```
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
### Commit.Publish.Complete Properties
| Property name | Type |
|---------------|------|
|build_log_url|string|
|build_report_url|string|
|JobBasicInfo|string|
|CorrelationId|string|
|ServiceName|string|
|RoleFlag|string|
|RoleInstance|string|
|build_status|string|
|build_message|string|
|priority|string|
|branch|string|
|ImageName|string|
|NodeName|string|
|PodName|string|
|tags|string|


### PullRequest.Build.Complete Properties
| Property name | Type |
|---------------|------|
|build_log_url|string|
|build_report_url|string|
|JobBasicInfo|string|
|CorrelationId|string|
|ServiceName|string|
|RoleFlag|string|
|RoleInstance|string|
|build_status|string|
|build_message|string|
|priority|string|
|branch|string|
|tags|string|


### PullRequest.Publish.Complete Properties
| Property name | Type |
|---------------|------|
|build_log_url|string|
|build_report_url|string|
|JobBasicInfo|string|
|CorrelationId|string|
|ServiceName|string|
|RoleFlag|string|
|build_status|string|
|ImageName|string|
|build_message|string|
|NodeName|string|
|priority|string|
|PodName|string|
|branch|string|
|tags|string|
|RoleInstance|string|


### Commit.Publish.Report Properties
| Property name | Type |
|---------------|------|
|build_log_url|string|
|build_report_url|string|
|JobBasicInfo|string|
|CorrelationId|string|
|ServiceName|string|
|markdown_engine_name|string|
|docfx_version|string|
|full_build_reason_code|string|
|incremental_infos|string|
|RoleFlag|string|
|validation_rule|string|
|RoleInstance|string|
|ImageName|string|
|NodeName|string|
|PodName|string|


### PullRequest.Build.Report Properties
| Property name | Type |
|---------------|------|
|build_log_url|string|
|build_report_url|string|
|JobBasicInfo|string|
|CorrelationId|string|
|ServiceName|string|
|RoleFlag|string|
|markdown_engine_name|string|
|incremental_infos|string|
|validation_rule|string|
|docfx_version|string|
|RoleInstance|string|


### PullRequest.Publish.Report Properties
| Property name | Type |
|---------------|------|
|build_log_url|string|
|build_report_url|string|
|JobBasicInfo|string|
|CorrelationId|string|
|ServiceName|string|
|markdown_engine_name|string|
|docfx_version|string|
|full_build_reason_code|string|
|incremental_infos|string|
|RoleFlag|string|
|validation_rule|string|
|ImageName|string|
|NodeName|string|
|PodName|string|
|RoleInstance|string|


### statuscode Properties
| Property name | Type |
|---------------|------|
|_MS.AggregationIntervalMs|string|
|runid|guid|
|ProcessId|string|
|locale|string|
|InvocationId|guid|
|Category|string|
|LogLevel|string|
|branch|string|
|depot|string|
|type|string|
|host|dynamic|
|code|string|


### linktotal Properties
| Property name | Type |
|---------------|------|
|_MS.AggregationIntervalMs|string|
|runid|guid|
|ProcessId|string|
|locale|string|
|InvocationId|guid|
|Category|string|
|LogLevel|string|
|branch|string|
|depot|string|


### depotcount Properties
| Property name | Type |
|---------------|------|
|runid|guid|
|ProcessId|string|
|_MS.AggregationIntervalMs|string|
|InvocationId|guid|
|Category|string|
|LogLevel|string|
|runtype|string|




## All Metrics
### Commit.Publish.Complete Metrics
| Metric name | Type |
|-------------|------|
|build_perf.processing_time|dynamic|
|build_result.warning_count.per_type.user|dynamic|
|build_perf.per_stage.waiting_in_queue|dynamic|
|build_result.suggestion_count.per_type.system|dynamic|
|build_result.published_toc_count|dynamic|
|build_result.suggestion_count.per_type.unspecified|dynamic|
|build_result.suggestion_count.per_type.user|dynamic|
|build_result.warning_count.per_type.unspecified|dynamic|
|build_result.total_toc_count|dynamic|
|build_result.error_count.per_type.system|dynamic|
|build_result.error_count.per_type.unspecified|dynamic|
|build_result.warning_count.per_type.system|dynamic|
|build_result.error_count.per_type.user|dynamic|
|build_perf.per_stage.publishing|dynamic|
|build_result.published_resource_count|dynamic|
|build_result.warning_count|dynamic|
|build_result.publish_cache_hit_count|dynamic|
|build_result.published_content_count|dynamic|
|build_result.total_resource_count|dynamic|
|build_result.published_file_count|dynamic|
|build_result.total_content_count|dynamic|
|build_perf.per_stage.preparing|dynamic|
|build_result.total_file_count|dynamic|
|build_result.suggestion_count|dynamic|
|build_perf.per_stage.building|dynamic|
|build_result.docset_count|dynamic|
|build_result.error_count|dynamic|
|build_perf.waiting_time|dynamic|


### PullRequest.Build.Complete Metrics
| Metric name | Type |
|-------------|------|
|build_perf.processing_time|dynamic|
|build_result.warning_count.per_type.user|dynamic|
|build_perf.per_stage.waiting_in_queue|dynamic|
|build_result.suggestion_count.per_type.system|dynamic|
|build_result.published_toc_count|dynamic|
|build_result.suggestion_count.per_type.unspecified|dynamic|
|build_result.suggestion_count.per_type.user|dynamic|
|build_result.warning_count.per_type.unspecified|dynamic|
|build_result.total_toc_count|dynamic|
|build_result.error_count.per_type.system|dynamic|
|build_result.error_count.per_type.unspecified|dynamic|
|build_result.warning_count.per_type.system|dynamic|
|build_result.error_count.per_type.user|dynamic|
|build_perf.per_stage.publishing|dynamic|
|build_result.published_resource_count|dynamic|
|build_result.warning_count|dynamic|
|build_result.publish_cache_hit_count|dynamic|
|build_result.published_content_count|dynamic|
|build_result.total_resource_count|dynamic|
|build_result.published_file_count|dynamic|
|build_result.total_content_count|dynamic|
|build_perf.per_stage.preparing|dynamic|
|build_result.total_file_count|dynamic|
|build_result.suggestion_count|dynamic|
|build_perf.per_stage.building|dynamic|
|build_result.docset_count|dynamic|
|build_result.error_count|dynamic|
|build_perf.waiting_time|dynamic|


### PullRequest.Publish.Complete Metrics
| Metric name | Type |
|-------------|------|
|build_perf.processing_time|dynamic|
|build_result.warning_count.per_type.user|dynamic|
|build_perf.per_stage.waiting_in_queue|dynamic|
|build_result.suggestion_count.per_type.system|dynamic|
|build_result.published_toc_count|dynamic|
|build_result.suggestion_count.per_type.unspecified|dynamic|
|build_result.suggestion_count.per_type.user|dynamic|
|build_result.warning_count.per_type.unspecified|dynamic|
|build_result.total_toc_count|dynamic|
|build_result.error_count.per_type.system|dynamic|
|build_result.error_count.per_type.unspecified|dynamic|
|build_result.warning_count.per_type.system|dynamic|
|build_result.error_count.per_type.user|dynamic|
|build_perf.per_stage.publishing|dynamic|
|build_result.published_resource_count|dynamic|
|build_result.warning_count|dynamic|
|build_result.publish_cache_hit_count|dynamic|
|build_result.published_content_count|dynamic|
|build_result.total_resource_count|dynamic|
|build_result.published_file_count|dynamic|
|build_result.total_content_count|dynamic|
|build_perf.per_stage.preparing|dynamic|
|build_result.total_file_count|dynamic|
|build_result.suggestion_count|dynamic|
|build_perf.per_stage.building|dynamic|
|build_result.docset_count|dynamic|
|build_result.error_count|dynamic|
|build_perf.waiting_time|dynamic|


### Commit.Publish.Report Metrics
Empty
### PullRequest.Build.Report Metrics
Empty
### PullRequest.Publish.Report Metrics
Empty

