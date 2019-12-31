# Event
One occurrence of something happening in the system, often the completion of some task (like build) or a goal achieved in the task (like successfully get build config).
In feature utilization, we provide properties related to this event in customDimensions attribute of each record. For example, build id, repo id, build status, build start time/end time are the properties of build completion event and you can filter, search, segment by using these properties.

## Supported Events
| Event name | Description | Examples | All Properties | All Metrics |
|------------|-------------|----------|----------------|-------------|
|Commit.Publish.Complete|Completion of Commit(Branch) build, and the related build errors, build status, build files.|[Get build duration details for each commit build](#get-build-duration-details-for-each-commit-build)|[Detail](#commitpublishcomplete-properties)|[Detail](#commitpublishcomplete-metrics)|
|PullRequest.Build.Complete|Completion of Pull Request build, and the related build errors, build status, build files.|[Get build error type details for each pull request build](#get-build-error-type-details-for-each-pull-request-build)|[Detail](#pullrequestbuildcomplete-properties)|[Detail](#pullrequestbuildcomplete-metrics)|
|PullRequest.Publish.Complete|Completion of pull request build and publish, and the related build errors, build status, build and publish files.|[Get published file details for each pull request build](#get-published-file-details-for-each-pull-request-build)|[Detail](#pullrequestpublishcomplete-properties)|[Detail](#pullrequestpublishcomplete-metrics)|
|Commit.Publish.Report|Commit(Branch) Build + Publish Report Information like build report url, incremental and docfx version.|[Get Commit Build report, incremental and version info](#get-commit-build-report-incremental-and-version-info)|[Detail](#commitpublishreport-properties)|[Detail](#commitpublishreport-metrics)|
|PullRequest.Build.Report|Pull Request Build Report Information like build report url, incremental and docfx version.|[Get Pull Request Build report, incremental and version info](#get-pull-request-build-report-incremental-and-version-info)|[Detail](#pullrequestbuildreport-properties)|[Detail](#pullrequestbuildreport-metrics)|
|PullRequest.Publish.Report|Pull Request Build + Publish Report Information like build report url, incremental and docfx version.|[Get Build report, incremental and version info](#get-build-report-incremental-and-version-info)|[Detail](#pullrequestpublishreport-properties)|[Detail](#pullrequestpublishreport-metrics)|


## Examples
### Get build duration details for each commit build
```
"queryevents
| where EventName == "Commit.Publish.Complete"
| extend JobBasicInfo = parse_json(tostring(Properties.JobBasicInfo))
| project Repository = JobBasicInfo.repo_url, Branch = JobBasicInfo.branch_name, BuildType = JobBasicInfo.build_type, BuildAction = JobBasicInfo.build_action, EventName, Timestamp, WaitingTime = Metrics["build_perf.per_stage.waiting_in_queue"].sum, PrepareTime = Metrics["build_perf.per_stage.preparing"].sum, BuildTime = Metrics["build_perf.per_stage.building"].sum, PublishTime = Metrics["build_perf.per_stage.publishing"].sum"

```

### Get build error type details for each pull request build
```
"queryevents
| where EventName == "PullRequest.Build.Complete"
| extend JobBasicInfo = parse_json(tostring(Properties.JobBasicInfo))
| project Repository = JobBasicInfo.repo_url, Branch = JobBasicInfo.branch_name, BuildType = JobBasicInfo.build_type, BuildAction = JobBasicInfo.build_action, EventName, Timestamp, SuggectionCount = Metrics["build_result.suggestion_count"].sum, ErrorCount = Metrics["build_result.error_count"].sum, WarningCount = Metrics["build_result.warning_count"].sum"

```

### Get published file details for each pull request build
```
"queryevents
| where EventName == "PullRequest.Publish.Complete"
| extend JobBasicInfo = parse_json(tostring(Properties.JobBasicInfo))
| project Repository = JobBasicInfo.repo_url, Branch = JobBasicInfo.branch_name, BuildType = JobBasicInfo.build_type, BuildAction = JobBasicInfo.build_action, EventName, Timestamp, PublishedResouceCount = Metrics["build_result.published_resource_count"].sum, PublishedContentCount = Metrics["build_result.published_content_count"].sum, PublishedTocCount = Metrics["build_result.published_toc_count"].sum, PublishedTotalCount = Metrics["build_result.published_file_count"].sum"

```

### Get Commit Build report, incremental and version info
```
"queryevents
| where EventName == "Commit.Publish.Report"
| extend JobBasicInfo = parse_json(tostring(Properties.JobBasicInfo))
| extend isIncremental = Properties.full_build_reason_code == 'NoReason'
| extend docfxVersion = Properties.docfx_version
| project Properties.build_report_url, isIncremental, docfxVersion, JobBasicInfo.build_id, JobBasicInfo.repo_id, JobBasicInfo.branch_name"

```

### Get Pull Request Build report, incremental and version info
```
"queryevents
| where EventName == "PullRequest.Build.Report"
| extend JobBasicInfo = parse_json(tostring(Properties.JobBasicInfo))
| extend isIncremental = Properties.full_build_reason_code == 'NoReason'
| extend docfxVersion = Properties.docfx_version
| project Properties.build_report_url, isIncremental, docfxVersion, JobBasicInfo.build_id, JobBasicInfo.repo_id, JobBasicInfo.branch_name"

```

### Get Build report, incremental and version info
```
"queryevents
| where EventName == "PullRequest.Publish.Report"
| extend JobBasicInfo = parse_json(tostring(Properties.JobBasicInfo))
| extend isIncremental = Properties.full_build_reason_code == 'NoReason'
| extend docfxVersion = Properties.docfx_version
| project Properties.build_report_url, isIncremental, docfxVersion, JobBasicInfo.build_id, JobBasicInfo.repo_id, JobBasicInfo.branch_name"

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
|build_status|string|
|RoleInstance|string|
|RoleFlag|string|
|build_message|string|
|priority|string|
|branch|string|
|tags|string|
|ImageName|string|
|NodeName|string|
|PodName|string|


### PullRequest.Build.Complete Properties
| Property name | Type |
|---------------|------|
|build_log_url|string|
|build_report_url|string|
|JobBasicInfo|string|
|CorrelationId|string|
|build_status|string|
|RoleInstance|string|
|ServiceName|string|
|RoleFlag|string|
|priority|string|
|branch|string|
|tags|string|
|build_message|string|
|ImageName|string|
|NodeName|string|
|PodName|string|


### PullRequest.Publish.Complete Properties
| Property name | Type |
|---------------|------|
|build_log_url|string|
|build_report_url|string|
|JobBasicInfo|string|
|CorrelationId|string|
|build_status|string|
|RoleInstance|string|
|ServiceName|string|
|RoleFlag|string|
|priority|string|
|branch|string|
|build_message|string|
|tags|string|
|ImageName|string|
|NodeName|string|
|PodName|string|


### Commit.Publish.Report Properties
| Property name | Type |
|---------------|------|
|build_log_url|string|
|build_report_url|string|
|JobBasicInfo|string|
|CorrelationId|string|
|ServiceName|string|
|ImageName|string|
|RoleFlag|string|
|NodeName|string|
|PodName|string|
|full_build_reason_code|string|
|docfx_version|string|
|validation_rule|string|
|incremental_infos|string|
|markdown_engine_name|string|
|RoleInstance|string|


### PullRequest.Build.Report Properties
| Property name | Type |
|---------------|------|
|build_log_url|string|
|build_report_url|string|
|JobBasicInfo|string|
|CorrelationId|string|
|RoleInstance|string|
|ServiceName|string|
|markdown_engine_name|string|
|incremental_infos|string|
|RoleFlag|string|
|validation_rule|string|
|full_build_reason_code|string|
|docfx_version|string|
|ImageName|string|
|NodeName|string|
|PodName|string|


### PullRequest.Publish.Report Properties
| Property name | Type |
|---------------|------|
|build_log_url|string|
|build_report_url|string|
|JobBasicInfo|string|
|CorrelationId|string|
|RoleInstance|string|
|ServiceName|string|
|markdown_engine_name|string|
|incremental_infos|string|
|RoleFlag|string|
|validation_rule|string|
|full_build_reason_code|string|
|docfx_version|string|
|ImageName|string|
|NodeName|string|
|PodName|string|




## All Metrics
### Commit.Publish.Complete Metrics
| Metric name | Type |
|-------------|------|
|build_result.error_count.per_type.user|dynamic|
|build_result.total_resource_count|dynamic|
|build_result.total_file_count|dynamic|
|build_result.published_resource_count|dynamic|
|build_perf.per_stage.publishing|dynamic|
|build_perf.waiting_time|dynamic|
|build_result.suggestion_count|dynamic|
|build_result.suggestion_count.per_type.unspecified|dynamic|
|build_result.published_toc_count|dynamic|
|build_result.suggestion_count.per_type.system|dynamic|
|build_perf.per_stage.waiting_in_queue|dynamic|
|build_result.warning_count|dynamic|
|build_result.publish_cache_hit_count|dynamic|
|build_result.warning_count.per_type.system|dynamic|
|build_perf.per_stage.preparing|dynamic|
|build_result.docset_count|dynamic|
|build_result.warning_count.per_type.unspecified|dynamic|
|build_result.suggestion_count.per_type.user|dynamic|
|build_result.error_count|dynamic|
|build_result.error_count.per_type.unspecified|dynamic|
|build_perf.processing_time|dynamic|
|build_result.warning_count.per_type.user|dynamic|
|build_result.published_file_count|dynamic|
|build_result.error_count.per_type.system|dynamic|
|build_result.total_toc_count|dynamic|
|build_perf.per_stage.building|dynamic|
|build_result.published_content_count|dynamic|
|build_result.total_content_count|dynamic|


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
|build_result.published_file_count|dynamic|
|build_result.total_resource_count|dynamic|
|build_result.total_content_count|dynamic|
|build_perf.per_stage.preparing|dynamic|
|build_result.total_file_count|dynamic|
|build_perf.per_stage.building|dynamic|
|build_result.suggestion_count|dynamic|
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
|build_result.published_file_count|dynamic|
|build_result.total_resource_count|dynamic|
|build_result.total_content_count|dynamic|
|build_perf.per_stage.preparing|dynamic|
|build_result.total_file_count|dynamic|
|build_perf.per_stage.building|dynamic|
|build_result.suggestion_count|dynamic|
|build_result.docset_count|dynamic|
|build_result.error_count|dynamic|
|build_perf.waiting_time|dynamic|


### Commit.Publish.Report Metrics
Empty
### PullRequest.Build.Report Metrics
Empty
### PullRequest.Publish.Report Metrics
Empty

