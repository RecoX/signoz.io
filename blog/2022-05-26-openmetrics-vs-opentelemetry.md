---
title: OpenMetrics vs OpenTelemetry - A guide on understanding these two specifications
slug: openmetrics-vs-opentelemetry
date: 2022-05-26
tags: [Tools Comparison]
authors: [bhupesh]
description: OpenMetrics and OpenTelemetry are popular open-source standards for generating telemetry data from application code. While OpenTelemetry can be used for logs, metrics, and traces, OpenMetrics is focused on generating metrics at scale from...
image: /img/blog/2022/05/opentelemetry_vs_openmetrics_cover.webp
keywords:
  - openmetrics
  - opentelemetry
  - openmetrics vs opentelemetry
  - prometheus
---

<head>
  <link rel="canonical" href="https://signoz.io/blog/openmetrics-vs-opentelemetry/"/>
</head>

import SignUps from '../docs/shared/sign-ups.md'

OpenMetrics and OpenTelemetry are popular standards for instrumenting cloud-native applications. Both projects are part of the <a href = "https://cncf.io/" rel="noopener noreferrer nofollow" target="_blank">Cloud Native Computing Foundation (CNCF)</a> and aim to simplify how we generate, collect and monitor services in a modern cloud-native distributed application environment.


<!--truncate-->

![Cover Image](/img/blog/2022/05/opentelemetry_vs_openmetrics_cover.webp)

Let's have a look at how both the standards are aiming to help solve the observability conundrum.


## What is OpenMetrics?

<a href = "https://github.com/OpenObservability/OpenMetrics" rel="noopener noreferrer nofollow" target="_blank">OpenMetrics</a> improves on the Prometheus exposition format with some cleanups and new features. Under the hood, the OpenMetrics standard is basically Prometheus exposition format. Just like Prometheus, OpenMetrics specifies that clients expose metrics to the `/metrics` endpoint.

While all the underlying specifications are the same as Prometheus, including the usage of PromQL, OpenMetrics provides some notable improvements in the new specification:

1. Units as new metadata.
2. `_created` events for metric creation & resets.
3. Support for Push metrics (Prometheus format only supported pull metrics).
4. Exemplars allow information about traces to your logs and metrics.
5. Usage of seconds as default time format rather than milliseconds.

### The current state of OpenMetrics

OpenMetrics is still in its early stages. Let's look at the current state of the standard:

- Prometheus python client is the reference implementation and uses the OpenMetrics data model internally.
- OpenTelemetry supports OpenMetrics as a first-class wire format (although in <a href = "https://opentelemetry.io/docs/specs/otel/compatibility/openmetrics/" rel="noopener noreferrer nofollow" target="_blank">experimental stages</a>)
- OpenMetrics is a part of the Prometheus Conformance Program, which means that any client which needs the “Prometheus Compatible” mark, needs to be compatible with OpenMetrics as well. You can read more about Prometheus-OpenMetrics compliance on their GitHub page, <a href = "https://github.com/prometheus/compliance/tree/main/openmetrics" rel="noopener noreferrer nofollow" target="_blank">Prometheus - OpenMetrics Compliance tester</a>.


## What is OpenTelemetry?

<a href = "https://opentelemetry.io/" rel="noopener noreferrer nofollow" target="_blank">OpenTelemetry</a> is a set of standards, API, SDKs, and libraries that aim to standardize the generation, collection, and management of telemetry data (logs, metrics, and traces). Founded in 2019, OpenTelemetry is a Cloud Native Computing Foundation project created after the merger of OpenCensus(from Google) and OpenTracing(from Uber).

The collected data from OpenTelemetry API is vendor-agnostic, so it can be exported in different formats. OpenTelemetry does not provide a storage and visualization layer for the collected telemetry data. 

But as data can be exported in multiple formats, OpenTelemetry users have the freedom to choose a backend analysis tool. This way, users never get locked in with a specific observability vendor.

<SignUps />

OpenTelemetry provides its own set of <a href = "https://opentelemetry.io/docs/instrumentation/" rel="noopener noreferrer nofollow" target="_blank">client libraries</a> which makes it easier for you to integrate it into your application code. OpenTelemetry follows a specification-driven development. The client libraries follow the specification for their implementation.

Monitoring apps have become a crucial component in a modern microservices architecture. A well setup observability stack with OpenTelemetry will help you answer questions like:

- Why is my application slow?
- Why do we've so many errors?
- Where are user requests spending time?

The above questions can be answered on basis of these 3 key pieces of information: logs, metrics, and traces.

<figure data-zoomable align='center'>
    <img src="/img/blog/2022/05/opentelemetry_pipeline_light.webp" alt="OpenTelemetry uses client libraries to instrument application code which is then sent to OpenTelemetry Collectors. The OTel collector then exports it to a backend analysis tool of your choice."/>
    <figcaption><i>OpenTelemetry Architecture - A look behind how OTel interacts with your application</i></figcaption>
</figure>

<br></br>

The OpenTelemetry project calls the above data points signals. It is currently working on four signals, namely, logs, metrics, traces, and baggage.

OpenTelemetry is a collaborative effort between 185 organizations around the world, involving thousands of individuals. Companies around the world have started adopting OpenTelemetry as a core engineering practice.

Take the example of <a href = "https://github.blog/2021-05-26-why-and-how-github-is-adopting-opentelemetry/" rel="noopener noreferrer nofollow" target="_blank">GitHub</a>, which wrote about its usage of OpenTelemetry last year. Considering its wide and increasing contributor ratio, OpenTelemetry is a safe choice for your application.

### The current state of OpenTelemetry

The status of open-telemetry can be measured by how the signals (tracing, metrics, baggage, and logging) have matured in 4 different components, namely API, SDKs, the OTLP Protocol, and the collector.

You can read a more in-depth and updated status on <a href = "https://opentelemetry.io/status/" rel="noopener noreferrer nofollow" target="_blank">OpenTelemetry status</a>. A more detailed status of each SDK can be found on <a href = "https://github.com/open-telemetry/opentelemetry-specification/blob/main/spec-compliance-matrix.md" rel="noopener noreferrer nofollow" target="_blank">OpenTelemetry - Compliance of implementation with the specification</a>.

## Key differences between OpenMetrics and OpenTelemetry

From the above description, you might have a good idea about the differences between OpenMetrics and OpenTelemetry. Let us summarize the key differences between OpenMetrics and OpenTelemetry below:

- OpenTelemetry aims to provide a framework for an end-to-end observability stack with specifications for handling logs, traces, and metrics, whereas OpenMetrics is more focused on the metrics side.
- OpenMetrics just standardizes the exposition format through which metrics data must be communicated over a network, whereas OpenTelemetry aims to provide compatibility with OpenMetrics by using this standardized format to export metrics data.
- OpenTelemetry is mainly designed for push, whereas OpenMetrics is designed for pull, although the OpenMetrics specification has consideration for both push and pull mechanisms.
- The OpenMetrics format only captures metrics. For creating a robust monitoring framework, you will need to track metrics, logs, and traces.

## Choosing between OpenMetrics and OpenTelemetry

If you are choosing between OpenMetrics and OpenTelemetry, go for Opentelemetry. It provides a more holistic solution to set up observability for your entire application stack. While OpenMetrics can be a good solution for metrics, OpenTelemetry is a one-stop solution for logs, metrics, and traces.

## OpenTelemetry and SigNoz - the right combo for all your observability needs

OpenTelemetry is becoming the world standard for instrumenting application code due to its multi-language support and ease of use. But OpenTelemetry helps only to generate and collect telemetry data. You need to export the telemetry data to a backend analysis tool so that your teams can store, query, and visualize the collected data.

And that's where [SigNoz](https://signoz.io/) comes into the picture. SigNoz uses OpenTelemetry natively to instrument application codes. OpenTelemetry collector then sends the data to the SigNoz backend, where users can query and visualize their data with ease.

SigNoz comes with out of box visualization of things like RED metrics. There is a unified UI of metrics and traces so that you can easily identify the root cause of issues causing things like latency in your apps.


<figure data-zoomable align='center'>
    <img src="/img/blog/common/signoz_flamegraphs.webp" alt="How opentelemetry fits with an application"/>
    <figcaption><i>Spans of a trace visualized with the help of flamegraphs and gantt charts in SigNoz dashboard</i></figcaption>
</figure>

<br></br>

## Getting started with SigNoz

SigNoz can be installed on macOS or Linux computers in just three steps by using a simple install script.

```bash
git clone -b main https://github.com/SigNoz/signoz.git
cd signoz/deploy/
./install.sh
```

You can visit our documentation for instructions on how to install SigNoz using Docker Swarm and Helm Charts.

[![Deployment Docs](/img/blog/common/deploy_docker_documentation.webp)](https://signoz.io/docs/install/)


## Further Reading

[SigNoz - an open-source alternative to DataDog](https://signoz.io/blog/open-source-datadog-alternative/)