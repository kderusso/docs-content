---
mapped_pages:
  - https://www.elastic.co/guide/en/serverless/current/collect-data-with-native-otlp.html
applies_to:
  serverless:
---

# Quickstart: Send data to the Elastic Cloud Managed OTLP Endpoint

In this quickstart guide, you'll learn how to use the [{{ecloud}} Managed OTLP Endpoint](opentelemetry://reference/motlp.md) to send logs, metrics, and traces to Elastic.

::::{warning}
This functionality is in technical preview and may be changed or removed in a future release. Elastic will work to fix any issues, but features in technical preview are not subject to the support SLA of official GA features.
::::

## Prerequisites

* An {{obs-serverless}} project. To learn more, refer to [create an Observability project](/solutions/observability/get-started/create-an-observability-project.md).
* A system forwarding logs, metrics, or traces in OTLP (any EDOT Collector or SDK—EDOT or community).

### Limitations

* The {{ecloud}} Managed OTLP Endpoint only supports histograms with delta temporality. Cumulative histograms are dropped.
* Latency distributions based on histogram values have limited precision due to the fixed boundaries of explicit bucket histograms.

## Get started

### Get your native OTLP endpoint credentials

1. [Create a new Observability project](/solutions/observability/get-started/create-an-observability-project.md), or open an existing one.

1. In your {{obs-serverless}} project, go to **Add Data**.

1. Under **What do you want to monitor?** select **Application**, and then under **Monitor your application using** select **OpenTelemetry**.

    :::{note}
    Follow this flow for all use cases, including logs and infrastructure monitoring.
    :::

1. Copy the `OTEL_EXPORTER_OTLP_ENDPOINT` URL. Replace `.apm` with `.ingest` and save this value for later.

### Create an API key

1. Click **Create an API Key** to generate a new API key. Copy this value for later.
1. (Optional) Test your new API key by sending an empty JSON object to the `/v1/traces` endpoint. For example:

    ```bash
    curl -X POST \
      -H "Content-Type: application/json" \
      -H "Authorization: ApiKey <api-key>" \
      https://{YOUR_CLUSTER}.ingest.us-east-1.aws.elastic.cloud:443/v1/traces \
      -d '{}'
    ```

    The response should be similar to:

    ```txt
    {"partialSuccess":{}}%
    ```

### Send data to your Elastic Cloud Managed OTLP endpoint

* [I have an EDOT Collector/SDK running](#otel-sdk-running)
* [I need an EDOT Collector/SDK](#no-sdk-running)
* [I just want to use the instrumentation](#instrumentation-please)

#### I have an EDOT Collector/SDK running [otel-sdk-running]

If you have an OpenTelemetry Collector or SDK exporting telemetry data,
configure it with the endpoint and API key generated in the previous steps.

**OpenTelemetry Collector configuration**

Configure your EDOT Collector as follows:

```yaml
exporters:
  otlp:
    endpoint: "https://my_cluster.ingest.us-east-1.aws.elastic.cloud:443/v1/traces"
    headers: "Authorization": "ApiKey <api-key-value-here>"
```

For more information, see [OTLP Collector configuration](https://opentelemetry.io/docs/collector/configuration/).

**Elastic Distributions of OpenTelemetry (EDOT) Collector configuration**

Configure an EDOT Collector using the same method described above in **OpenTelemetry Collector configuration**.
See the [EDOT Language SDK documentation](opentelemetry://reference/edot-collector/index.md) for more information.

**OpenTelemetry SDK configuration**

Configure your OTel SDK with the following environment variables:

* Elastic Cloud Managed OTLP endpoint: `OTEL_EXPORTER_OTLP_ENDPOINT`
* Elastic API key: `OTEL_EXPORTER_OTLP_HEADERS`

For example:

```bash
export OTEL_EXPORTER_OTLP_ENDPOINT="https://my-api-endpoint:443"
export OTEL_EXPORTER_OTLP_HEADERS="Authorization=ApiKey <api-key>"
```

For more information, see [OTLP Exporter configuration](https://opentelemetry.io/docs/languages/sdk-configuration/otlp-exporter/).

**Elastic Distributions of OpenTelemetry (EDOT) SDK configuration**

Configure an EDOT SDK using the same method described above in **OpenTelemetry SDK configuration**.
See the [EDOT Language SDK documentation](opentelemetry://reference/edot-sdks/index.md) for more information.

#### I need an EDOT Collector/SDK [no-sdk-running]

Don't have a collector or SDK running? No problem. Spin up an EDOT collector in just a few steps:

* [Kubernetes Quickstart](opentelemetry://reference/quickstart/serverless/k8s.md)
* [Hosts & VMs Quickstart](opentelemetry://reference/quickstart/serverless/hosts_vms.md)

% Commenting out Docker until the docs are ready
% * [Docker Quickstart](opentelemetry://reference/quickstart/serverless/docker.md)

#### I just want to use the instrumentation [instrumentation-please]

See [application use-cases](opentelemetry://reference/edot-sdks/index.md) for more information.

## Troubleshoot

**Api Key prefix not found**

The following error is due to an improperly formatted API key:

```txt
Exporting failed. Dropping data.
{"kind": "exporter", "data_type": }
"Unauthenticated desc = ApiKey prefix not found"
```

You must format your API key as `"Authorization": "ApiKey <api-key-value-here>"` or `"Authorization=ApiKey <api-key>"` depending on whether you're using a collector or SDK. See [I have an EDOT Collector/SDK running](#otel-sdk-running) for more information.

**Error: too many requests**

The Managed endpoint has per-project rate limits in place. If you hit this limit, reach out to our [support team](https://support.elastic.co).

## Provide feedback

Help improve the Elastic Cloud Managed OTLP Endpoint by sending us feedback in our [discussion forum](https://discuss.elastic.co/c/apm) or [community Slack](https://elasticstack.slack.com/signup#/domain-signup).

For EDOT collector feedback, open an issue in the [elastic-agent repository](https://github.com/elastic/elastic-agent/issues).

## What's next?

Visualize your OpenTelemetry data. Learn more in [](/solutions/observability/otlp-visualize.md).
