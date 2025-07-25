# AWS Logs encoding extension

<!-- status autogenerated section -->
| Status        |           |
| ------------- |-----------|
| Stability     | [alpha]  |
| Distributions | [contrib] |
| Issues        | [![Open issues](https://img.shields.io/github/issues-search/open-telemetry/opentelemetry-collector-contrib?query=is%3Aissue%20is%3Aopen%20label%3Aextension%2Fawslogsencoding%20&label=open&color=orange&logo=opentelemetry)](https://github.com/open-telemetry/opentelemetry-collector-contrib/issues?q=is%3Aopen+is%3Aissue+label%3Aextension%2Fawslogsencoding) [![Closed issues](https://img.shields.io/github/issues-search/open-telemetry/opentelemetry-collector-contrib?query=is%3Aissue%20is%3Aclosed%20label%3Aextension%2Fawslogsencoding%20&label=closed&color=blue&logo=opentelemetry)](https://github.com/open-telemetry/opentelemetry-collector-contrib/issues?q=is%3Aclosed+is%3Aissue+label%3Aextension%2Fawslogsencoding) |
| [Code Owners](https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/CONTRIBUTING.md#becoming-a-code-owner)    | [@axw](https://www.github.com/axw), [@constanca-m](https://www.github.com/constanca-m) |

[alpha]: https://github.com/open-telemetry/opentelemetry-collector/blob/main/docs/component-stability.md#alpha
[contrib]: https://github.com/open-telemetry/opentelemetry-collector-releases/tree/main/distributions/otelcol-contrib
<!-- end autogenerated section -->

This extension unmarshals logs encoded in formats produced by AWS services, including:
 - [Amazon CloudWatch Logs Subscription Filters](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/SubscriptionFilters.html).
 - [VPC flow log records](https://docs.aws.amazon.com/vpc/latest/userguide/flow-log-records.html) sent to S3 in plain text.
   - Parquet support still to be added.
 - [S3 access log records](https://docs.aws.amazon.com/AmazonS3/latest/userguide/LogFormat.html).
 - [AWS CloudTrail logs](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-log-file-examples.html).
 - (More to be added later.)

Example for Amazon CloudWatch Logs Subscription Filters:
```yaml
extensions:
  awslogs_encoding/cloudwatch:
    format: cloudwatch_logs_subscription_filter

receivers:
  awsfirehose:
    endpoint: :1234
    encoding: awslogs_encoding/cloudwatch
```

Example for VPC flow logs:
```yaml
extensions:
  awslogs_encoding/vpc_flow_log:
    format: vpc_flow_log
    vpc_flow_log:
      # options [parquet, plain-text]. 
      # parquet option still needs to be implemented.
      file_format: plain-text 
```

Example for S3 access logs:
```yaml
extensions:
  awslogs_encoding/s3_access_log:
    format: s3_access_log
```

Example for CloudTrail logs:
```yaml
extensions:
  awslogs_encoding/cloudtrail:
    format: cloudtrail_log
```

#### VPC flow log record fields

[VPC flow log record fields](https://docs.aws.amazon.com/vpc/latest/userguide/flow-log-records.html#flow-logs-fields) are mapped this way in the resulting OpenTelemetry log:

| Flow log field               | Attribute in OpenTelemetry log                                                                        |
|------------------------------|-------------------------------------------------------------------------------------------------------|
| `version`                    | `aws.vpc.flow.log.version`                                                                            |
| `account-id`                 | `cloud.account.id`                                                                                    |
| `interface-id`               | `network.interface.name`                                                                              |
| `srcaddr`                    | `source.address`: if `pkt-srcaddr` not filled or the same <br> `network.peer.address`: otherwise      |
| `pkt-srcaddr`                | `source.address` if filled                                                                            |
| `dstaddr`                    | `destination.address`: if `pkt-dstaddr` not filled or the same <br> `network.peer.address`: otherwise |
| `pkt-dstaddr`                | `destination.address` if filled                                                                       |
| `srcport`                    | `source.port`                                                                                         |
| `dstport`                    | `destination.port`                                                                                    |
| `protocol`                   | `network.protocol.name`                                                                               |
| `packets`                    | `aws.vpc.flow.packets`                                                                                |
| `bytes`                      | `aws.vpc.flow.bytes`                                                                                  |
| `start`                      | `aws.vpc.flow.start`                                                                                  |
| `end`                        | Log timestamp                                                                                         |
| `action`                     | `aws.vpc.flow.action`                                                                                 |
| `log-status`                 | `aws.vpc.flow.status`                                                                                 |
| `vpc-id`                     | `aws.vpc.id`                                                                                          |
| `subnet-id`                  | `aws.vpc.subnet.id`                                                                                   |
| `instance-id`                | `host.id`                                                                                             |
| `tcp-flags`                  | `network.tcp.flags`                                                                                   |
| `type`                       | `network.type`                                                                                        |
| `region`                     | `cloud.region`                                                                                        |
| `az-id`                      | `aws.az.id`                                                                                           |
| `sublocation-type`           | `aws.sublocation.type`                                                                                |
| `sublocation-id`             | `aws.sublocation.id`                                                                                  |
| `pkt-src-aws-service`        | `aws.vpc.flow.source.service`                                                                         |
| `pkt-dst-aws-service`        | `aws.vpc.flow.destination.service`                                                                    |
| `flow-direction`             | `network.io.direction`                                                                                |
| `traffic-path`               | `aws.vpc.flow.traffic_path`                                                                           |
| `ecs-cluster-arn`            | `aws.ecs.cluster.arn`                                                                                 |
| `ecs-cluster-name`           | `aws.ecs.cluster.name`                                                                                |
| `ecs-container-instance-arn` | `aws.ecs.container.instance.arn`                                                                      |
| `ecs-container-instance-id`  | `aws.ecs.container.instance.id`                                                                       |
| `ecs-container-id`           | `aws.ecs.container.id`                                                                                |
| `ecs-second-container-id`    | `aws.ecs.second.container.id`                                                                         |
| `ecs-service-name`           | `aws.ecs.service.name`                                                                                |
| `ecs-task-definition-arn`    | `aws.ecs.task.definition.arn`                                                                         |
| `ecs-task-arn`               | `aws.ecs.task.arn`                                                                                    |
| `ecs-task-id`                | `aws.ecs.task.id`                                                                                     |
| `reject-reason`              | `aws.vpc.flow.reject_reason`                                                                          |

#### S3 access log record fields

[S3 access log record fields](https://docs.aws.amazon.com/AmazonS3/latest/userguide/LogFormat.html) are mapped this way in the resulting OpenTelemetry log:


| AWS field           | OpenTelemetry Field                                                                                                                                                                                                                                                                                     |
|---------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Bucket owner        | `aws.s3.owner`                                                                                                                                                                                                                                                                                          |
| Bucket              | `aws.s3.bucket`                                                                                                                                                                                                                                                                                         |
| Time                | Log timestamp                                                                                                                                                                                                                                                                                           |
| Remote IP           | `source.address`                                                                                                                                                                                                                                                                                        |
| Requester           | `user.id`                                                                                                                                                                                                                                                                                               |
| Request ID          | `aws.request_id`                                                                                                                                                                                                                                                                                        |
| Operation           | `rpc.method`                                                                                                                                                                                                                                                                                            |
| Key                 | `aws.s3.key`                                                                                                                                                                                                                                                                                            |
| Request URI         | Split by space: <br> 1. `http.request.method` <br> 2. URL is parsed:<br> &nbsp;&nbsp; 1. `url.path`<br> &nbsp;&nbsp; 2. `url.scheme`<br> &nbsp;&nbsp; 3. `url.query` <br>3. Protocol splits in character `/`:<br> &nbsp;&nbsp; 1.`network.protocol.name`<br> &nbsp;&nbsp; 2. `network.protocol.version` |
| HTTP status         | `http.response.status_code`                                                                                                                                                                                                                                                                             |
| Error code          | `error.type`                                                                                                                                                                                                                                                                                            |
| Bytes sent          | `http.response.body.size`                                                                                                                                                                                                                                                                               |
| Object size         | `aws.s3.object.size`                                                                                                                                                                                                                                                                                    |
| Total time          | `duration`                                                                                                                                                                                                                                                                                              |
| Turn around time    | `aws.s3.turn_around_time`                                                                                                                                                                                                                                                                               |
| Referer             | `http.request.header.referer`                                                                                                                                                                                                                                                                           |
| User-Agent          | `user_agent.original`                                                                                                                                                                                                                                                                                   |
| Version ID          | `aws.s3.version_id`                                                                                                                                                                                                                                                                                     |
| Host ID             | `aws.extended_request_id`                                                                                                                                                                                                                                                                               |
| Signature version   | `aws.signature.version`                                                                                                                                                                                                                                                                                 |
| Cipher suite        | `tls.cipher`                                                                                                                                                                                                                                                                                            |
| Authentication Type | `aws.s3.auth_type`                                                                                                                                                                                                                                                                                      |
| Host header         | `http.request.header.host`                                                                                                                                                                                                                                                                              |
| TLS version         | `tls.protocol.version`                                                                                                                                                                                                                                                                                  |
| Access point ARN    | `aws.s3.access_point.arn`                                                                                                                                                                                                                                                                               |
| aclRequired         | `aws.s3.acl_required`                                                                                                                                                                                                                                                                                   |
#### AWS WAF log record fields

[AWS WAF log record fields](https://docs.aws.amazon.com/waf/latest/developerguide/logging-fields.html) are mapped this way in the resulting OpenTelemetry log:



| Original log field            | OpenTelemetry field                                                                              |
|-------------------------------|--------------------------------------------------------------------------------------------------|
| `webaclId`                    | `cloud.resource_id`<br>Also splits the value to get:<br>1.`cloud.region`<br>2.`cloud.account.id` |
| `terminatingRuleId`           | `aws.waf.terminating_rule.id`                                                                    |
| `terminatingRuleType`         | `aws.waf.terminating_rule.type`                                                                  |
| `httpSourceName`              | `aws.waf.source.name`                                                                            |
| `httpSourceId`                | `aws.waf.source.id`                                                                              |
| `httpRequest.clientIp`        | `client.address`                                                                                 |
| `httpRequest.headers`         | Each header is saved under `http.request.header.<header_name>`                                   |
| `httpRequest.uri`             | `url.path`                                                                                       |
| `httpRequest.args`            | `url.query`                                                                                      |
| `httpRequest.httpMethod`      | `http.request.method`                                                                            |
| `httpRequest.httpVersion`     | Splits in:<br>1.`network.protocol.name`<br>2.`network.protocol.version`                          |
| `httpRequest.requestId`       | `aws.request_id`                                                                                 |
| `httpRequest.fragment`        | `url.fragment`                                                                                   |
| `httpRequest.scheme`          | `url.scheme`                                                                                     |
| `httpRequest.country`         | `geo.country.iso_code`                                                                           |
| `httpRequest.host`            | `server.address`                                                                                 |
| `responseCodeSent`            | `http.response.status_code`                                                                      |
| `ja3Fingerprint`              | `tls.client.ja3`                                                                                 |
| `ja4Fingerprint`              | `tls.client.ja4`                                                                                 |
| `formatVersion`               | _Currently not supported_                                                                        |
| `terminatingRuleMatchDetails` | _Currently not supported_                                                                        |
| `ruleGroupList`               | _Currently not supported_                                                                        |
| `rateBasedRuleList`           | _Currently not supported_                                                                        |
| `nonTerminatingMatchingRules` | _Currently not supported_                                                                        |
| `requestHeadersInserted`      | _Currently not supported_                                                                        |
| `labels`                      | _Currently not supported_                                                                        |
| `captchaResponse`             | _Currently not supported_                                                                        |
| `cfDistributionTenantId`      | _Currently not supported_                                                                        |
| `challengeResponse`           | _Currently not supported_                                                                        |
| `oversizeFields`              | _Currently not supported_                                                                        |

#### CloudTrail log record fields

[CloudTrail log record fields](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-record-contents.html) are mapped this way in the resulting OpenTelemetry log:

| CloudTrail field                      | Attribute in OpenTelemetry log                                |
|---------------------------------------|---------------------------------------------------------------|
| `eventID`                             | `aws.cloudtrail.event_id`                                     |
| `eventVersion`                        | `aws.cloudtrail.event_version`                                |
| `eventCategory`                       | `aws.event.category`                                          |
| `errorCode`                           | `aws.error.code`                                              |
| `managementEvent`                     | `aws.event.management`                                        |
| `errorMessage`                        | `aws.error.message`                                           |
| `readOnly`                            | `aws.event.read_only`                                         |
| `userIdentity.identityStoreArn`       | `aws.identity_store.arn`                                      |
| `insightDetails`                      | `aws.insight_details` (as a map, if available)                |
| `userIdentity.arn`                    | `aws.principal.arn`                                           |
| `userIdentity.principalId`            | `aws.principal.id`                                            |
| `userIdentity.type`                   | `aws.principal.type`                                          |
| `requestParameters`                   | `aws.request.parameters` (map of all request parameters)      |
| `requestID`                           | `aws.request_id`                                              |
| `resources`                           | `aws.resources` (as an array, if available)                   |
| `responseElements`                    | `aws.response.elements` (map of all response elements)        |
| `sessionCredentialFromConsole`        | `aws.session.console` (set to true if value is "true")        |
| `sharedEventID`                       | `aws.shared_event_id`                                         |
| `recipientAccountId`                  | `cloud.account.id`                                            |
| `awsRegion`                           | `cloud.region`                                                |
| `eventName`                           | `rpc.method`                                                  |
| `eventSource`                         | `rpc.service`                                                 |
| `eventType`                           | `rpc.system`                                                  |
| `tlsDetails.clientProvidedHostHeader` | `server.address`                                              |
| `sourceIPAddress`                     | `source.address`                                              |
| `tlsDetails.cipherSuite`              | `tls.cipher`                                                  |
| `tlsDetails.tlsVersion`               | `tls.protocol.version`                                        |
| `userAgent`                           | `user_agent.original`                                         |
| `userIdentity.userId`                 | `user.id`                                                     |
| `userIdentity.userName`               | `user.name`                                                   |


All request parameters and response elements are included directly as nested maps in the attributes, preserving their original structure.
