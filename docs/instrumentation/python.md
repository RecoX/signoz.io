---
id: python
title: Python OpenTelemetry Instrumentation
description: Send events from your Python application to SigNoz

---

import Tabs from "@theme/Tabs";
import TabItem from "@theme/TabItem";
import InstrumentationFAQ from '../shared/instrumentation-faq.md'


This document contains instructions on how to set up OpenTelemetry instrumentation in your Python applications. OpenTelemetry, also known as OTel for short, is an open source observability framework that can help you generate and collect telemetry data - traces, metrics, and logs from your Python application.

Once the telemetry data is collected, you can configure an exporter to send the data to SigNoz.

There are three major steps to using OpenTelemetry:

- Instrumenting your Python application with OpenTelemetry
- Configuring exporter to send data to SigNoz
- Validating that configuration to ensure that data is being sent as expected.

<figure data-zoomable align='center'>
    <img src="/img/docs/otel_python_instrumentation.webp" alt="OpenTelemetry helps to generate and collect telemetry data from your application which is then sent to an observability backend like SigNoz"/>
    <figcaption><i>OpenTelemetry helps generate and collect telemetry data from Python applications which can then be sent to SigNoz for storage, visualization, and analysis.</i></figcaption>
</figure>

<br></br>

Let’s understand how to download, install, and run OpenTelemetry in Python.

## Requirements

- Python 3.8 or newer

## Send Traces to SigNoz Cloud

Based on your application environment, you can choose the setup below to send traces to SigNoz Cloud.

<Tabs>
<TabItem value="vm" label="VM" default>

From VMs, there are two ways to send data to SigNoz Cloud.

- [Send traces directly to SigNoz Cloud](#send-traces-directly-to-signoz-cloud)
- [Send traces via OTel Collector binary](#send-traces-via-otel-collector-binary) (recommended)

#### **Send traces directly to SigNoz Cloud**

Step 1. Install the OpenTelemetry dependencies

```bash
pip install opentelemetry-distro==0.38b0
pip install opentelemetry-exporter-otlp==1.17.0
```

Step 2. Add automatic instrumentation

```bash
opentelemetry-bootstrap --action=install
```

Step 3. Run your application

```bash
OTEL_RESOURCE_ATTRIBUTES=service.name=<service_name> \
OTEL_EXPORTER_OTLP_ENDPOINT="https://ingest.{region}.signoz.cloud:443" \
OTEL_EXPORTER_OTLP_HEADERS="signoz-access-token=SIGNOZ_INGESTION_KEY" \
opentelemetry-instrument <your_run_command>
```

- *`<service_name>`* is the name of the service you want
- *`<your_run_command>`* can be `python3 app.py` or `flask run`
- Replace `SIGNOZ_INGESTION_KEY` with the api token provided by SigNoz. You can find it in the email sent by SigNoz with your cloud account details.

Depending on the choice of your region for SigNoz cloud, the ingest endpoint will vary according to this table.

| Region | Endpoint |
| --- | --- |
| US |	ingest.us.signoz.cloud:443 |
| IN |	ingest.in.signoz.cloud:443 |
| EU | ingest.eu.signoz.cloud:443 |

Step 4. Validate if your application is sending traces to SigNoz cloud by following the instructions [here](#validating-instrumentation-by-checking-for-traces).

---

#### **Send traces via OTel Collector binary**

OTel Collector binary helps to collect logs, hostmetrics, resource and infra attributes. It is recommended to install Otel Collector binary to collect and send traces to SigNoz cloud. You can correlate signals and have rich contextual data through this way.

You can find instructions to install OTel Collector binary [here](https://signoz.io/docs/tutorial/opentelemetry-binary-usage-in-virtual-machine/) in your VM. Once you are done setting up your OTel Collector binary, you can follow the below steps for instrumenting your Python application.

Step 1. Install the OpenTelemetry dependencies

```bash
pip install opentelemetry-distro==0.38b0
pip install opentelemetry-exporter-otlp==1.17.0
```

Step 2. Add automatic instrumentation

```bash
opentelemetry-bootstrap --action=install
```

Step 3. To run your application and send data to collector in same VM:

```bash
OTEL_RESOURCE_ATTRIBUTES=service.name=<service_name> \
OTEL_EXPORTER_OTLP_ENDPOINT="http://localhost:4317" \
opentelemetry-instrument <your_run_command>
```

where,

- *`<service_name>`* is the name of the service you want
- *`<your_run_command>`* can be `python3 app.py` or `flask run`

In case you have OtelCollector Agent in different VM, replace localhost:4317 with `<IP Address of the VM>:4317`.

Step 4. You can validate if your application is sending traces to SigNoz cloud by following the instructions [here](#validating-instrumentation-by-checking-for-traces).

</TabItem>
<TabItem value="k8s" label="Kubernetes">

For Python application deployed on Kubernetes, you need to install OTel Collector agent in your k8s infra to collect and send traces to SigNoz Cloud. You can find the instructions to install OTel Collector agent [here](https://signoz.io/docs/tutorial/kubernetes-infra-metrics/).

Once you have set up OTel Collector agent, you can proceed with OpenTelemetry Python instrumentation by following the below steps:

Step 1. Install the OpenTelemetry dependencies

```bash
pip install opentelemetry-distro==0.38b0
pip install opentelemetry-exporter-otlp==1.17.0
```

Step 2. Add automatic instrumentation

```bash
opentelemetry-bootstrap --action=install
```

Step 3. Run your application:

```bash
OTEL_RESOURCE_ATTRIBUTES=service.name=<service_name> \
OTEL_EXPORTER_OTLP_ENDPOINT="http://localhost:4317" \
opentelemetry-instrument <your_run_command>
```

where,

- *`<service_name>`* is the name of the service you want
- *`<your_run_command>`* can be `python3 app.py` or `flask run`


Step 4. Make sure to dockerise your application along with OpenTelemetry instrumentation.

You can validate if your application is sending traces to SigNoz cloud by following the instructions [here](#validating-instrumentation-by-checking-for-traces).
  
</TabItem>
</Tabs>

## Send Traces to Self-Hosted SigNoz

You can use OpenTelemetry Python to send your traces directly to SigNoz. OpenTelemetry provides a handy distro in Python that can help you get started with automatic instrumentation. We recommend using it to get started quickly.

### Steps to auto-instrument Python app for traces


:::info

  If you are on K8s, you should checkout [opentelemetry operators](/docs/tutorial/opentelemetry-operator-usage/#opentelemetry-auto-instrumentation-injection) which enable auto instrumenting Python applications very easily.

:::

1. **Create a virtual environment**<br></br>
    
    ```bash
    python3 -m venv .venv
    source .venv/bin/activate
    ```

2. **Install the OpenTelemetry dependencies**<br></br>
    
    ```bash
    pip install opentelemetry-distro
    pip install opentelemetry-exporter-otlp
    ```

    The dependencies included are briefly explained below:

    `opentelemetry-distro` - The distro provides a mechanism to automatically configure some of the more common options for users. It helps to get started with OpenTelemetry auto-instrumentation quickly. 
    
    `opentelemetry-exporter-otlp` - This library provides a way to install all OTLP exporters. You will need an exporter to send the data to SigNoz.

    :::note
    💡 The `opentelemetry-exporter-otlp` is a convenience wrapper package to install all OTLP exporters. Currently, it installs:
    
    - opentelemetry-exporter-otlp-proto-http
    - opentelemetry-exporter-otlp-proto-grpc

    - (soon) opentelemetry-exporter-otlp-json-http
    
    The `opentelemetry-exporter-otlp-proto-grpc` package installs the gRPC exporter which depends on the `grpcio` package. The installation of `grpcio` may fail on some platforms for various reasons. If you run into such issues, or you don't want to use gRPC, you can install the HTTP exporter instead by installing the `opentelemetry-exporter-otlp-proto-http` package. You need to set the `OTEL_EXPORTER_OTLP_PROTOCOL` environment variable to `http/protobuf` to use the HTTP exporter.
    :::
    
3. **Add automatic instrumentation**<br></br>
     The below command inspects the dependencies of your application and installs the instrumentation packages relevant for your Python application.
    
    ```bash
    opentelemetry-bootstrap --action=install
    ```

    :::note
     Please make sure that you have installed all the dependencies of your application before running the above command. The command will not install instrumentation for the dependencies which are not installed.
    :::

4. **Run your application**<br></br>
     
    :::note
     Don’t run app in reloader/hot-reload mode as it breaks instrumentation. For example, if you use `export FLASK_ENV=development`, it enables the reloader mode which breaks OpenTelemetry instrumentation.
    :::
     
     
     To start sending data to SigNoz, use the following run command:

     ```bash
     OTEL_RESOURCE_ATTRIBUTES=service.name=<service_name> OTEL_EXPORTER_OTLP_ENDPOINT="http://<IP of SigNoz Backend>:4317" OTEL_EXPORTER_OTLP_PROTOCOL=grpc opentelemetry-instrument <your run command>
     ```

     *<service_name>* is the name of the service you want

     *<your_run_command>* can be `python3 app.py` or `flask run`

     `IP of SigNoz backend` is the IP of the machine where you installed SigNoz. If you have installed SigNoz on `localhost`, the endpoint will be `http://localhost:4317` for gRPC exporter and `http://localhost:4318` for HTTP exporter.

     Replacing these environment variables, a sample final run command will look like this:

     ```bash
     OTEL_RESOURCE_ATTRIBUTES=service.name=python_app OTEL_EXPORTER_OTLP_ENDPOINT="http://localhost:4317" opentelemetry-instrument python3 app.py
     ```
     
    :::note
     The port numbers are 4317 and 4318 for the gRPC and HTTP exporters respectively. Remember to allow incoming requests to port **4317**/**4318** of machine where SigNoz backend is hosted.
    :::
     

## Validating instrumentation by checking for traces

With your application running, you can verify that you’ve instrumented your application with OpenTelemetry correctly by confirming that tracing data is being reported to SigNoz.

To do this, you need to ensure that your application generates some data. Applications will not produce traces unless they are being interacted with, and OpenTelemetry will often buffer data before sending. So you need to interact with your application and wait for some time to see your tracing data in SigNoz.

Validate your traces in SigNoz:

1. Trigger an action in your app that generates a web request. Hit the endpoint a number of times to generate some data. Then, wait for some time.
2. In SigNoz, open the `Services` tab. Hit the `Refresh` button on the top right corner, and your application should appear in the list of `Applications`.
3. Go to the `Traces` tab, and apply relevant filters to see your application’s traces.

You might see other dummy applications if you’re using SigNoz for the first time. You can remove it by following the docs [here](https://signoz.io/docs/operate/docker-standalone/#remove-the-sample-application).

<figure data-zoomable align='center'>
    <img src="/img/docs/opentelemetry_python_app_instrumented.webp" alt="Python Application in the list of services being monitored in SigNoz"/>
    <figcaption><i>Python Application in the list of services being monitored in SigNoz</i></figcaption></figure>
<br></br>

## Instrumenting different Python Frameworks

The `opentelemetry-distro` package can initialize instrumentation for a lot of popular Python frameworks. You can find a complete list [here](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation). For popular Python frameworks too, the distro provides a quick way to get started with automatic instrumentation.

### Django Instrumentation

It is recommended to use the [opentelemetry distro](#steps-to-auto-instrument-python-app-for-traces) for instrumenting Django applications. Though for Django, you must define `DJANGO_SETTINGS_MODULE`correctly. If your project is called `mysite`, something like following should work:

```jsx
export DJANGO_SETTINGS_MODULE=mysite.settings
```

Please refer the official [Django docs](https://docs.djangoproject.com/en/1.10/topics/settings/#designating-the-settings) for more details.

### Flask Instrumentation

It is recommended to use the [opentelemetry distro](#steps-to-auto-instrument-python-app-for-traces) for instrumenting Flask applications.

### FastAPI Instrumentation

It is recommended to use the [opentelemetry distro](#steps-to-auto-instrument-python-app-for-traces) for instrumenting FastAPI applications.

### Falcon Instrumentation

It is recommended to use the [opentelemetry distro](#steps-to-auto-instrument-python-app-for-traces) for instrumenting Falcon applications.

## Database Instrumentation

Make sure that the DB client library you are using has the corresponding instrumentation library, and the version of the DB client library is supported by OpenTelemetry.

### MongoDB

You can use  `opentelemetry-distro` to initialize instrumentation for your MongoDB database calls. You need to ensure that the version of your DB client library is supported by OpenTelemetry. For MongoDB, the instrumentation library is `opentelemetry-instrumentation-pymongo`.

You can check the supported versions [here](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation).

### Redis

You can use  `opentelemetry-distro` to initialize instrumentation for your Redis database calls. You need to ensure that the version of your DB client library is supported by OpenTelemetry. For Redis, the instrumentation library is `opentelemetry-instrumentation-redis`.

You can check the supported versions [here](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation).

### MySQL

You can use  `opentelemetry-distro` to initialize instrumentation for your MySQL database calls. You need to ensure that the version of your DB client library is supported by OpenTelemetry. For MySQL, we have two instrumentation libraries:

- opentelemetry-instrumentation-mysql
- opentelemetry-instrumentation-pymysql

You can check the supported versions [here](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation).

### Postgres

You can use  `opentelemetry-distro` to initialize instrumentation for your PostgreSQL database calls. You need to ensure that the version of your DB client library is supported by OpenTelemetry. For Postgres, the instrumentation library is `opentelemetry-instrumentation-psycopg2`.

You can check the supported versions [here](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation).

:::note

`psycopg2-binary` is not supported by opentelemetry auto instrumentation libraries as it is not recommended for production use. Please use `psycopg2` to see DB calls also in your trace data in SigNoz

:::

## Running applications with Gunicorn, uWSGI

For application servers which are based on pre fork model like Gunicorn, uWSGI you have to add a `post_fork` hook or a `@postfork` decorator in your configuration.

Check this [documentation](https://opentelemetry-python.readthedocs.io/en/latest/examples/fork-process-model/README.html) from OpenTelemetry on how to set it up.

[Here's](https://github.com/open-telemetry/opentelemetry-python/tree/main/docs/examples/fork-process-model) a working example configured with a gunicorn server with `post_fork` hook.
  
<p>&nbsp;</p>


## Troubleshooting your SigNoz installation

#### Spans are not being reported

If spans are not being reported to SigNoz, try enabling debug exporter which writes the JSON formatted trace data to the console by setting env var OTEL_TRACES_EXPORTER=console.

`OTEL_RESOURCE_ATTRIBUTES=service.name=python_app OTEL_TRACES_EXPORTER=console opentelemetry-instrument <your run command>`:

```json
{
    "name": "alice",
    "context": {
        "trace_id": "0xedb7caf0c8b082a9578460a201759193",
        "span_id": "0x57cf7eee198e1fed",
        "trace_state": "[]"
    },
    "kind": "SpanKind.INTERNAL",
    "parent_id": null,
    "start_time": "2022-03-27T14:55:18.804758Z",
    "end_time": "2022-03-27T14:55:18.804805Z",
    "status": {
        "status_code": "UNSET"
    },
    "attributes": {},
    "events": [],
    "links": [],
    "resource": {
        "telemetry.sdk.language": "python",
        "telemetry.sdk.name": "opentelemetry",
        "telemetry.sdk.version": "1.10.0",
        "service.name": "my-service"
    }
}
```

<p>&nbsp;</p>

<!-- #### If DB Calls are not reported in spans

Ensure you have the correct opentelemetry instrumentations:

- [opentelemetry-instrumentation-psycopg2](https://pypi.org/project/opentelemetry-instrumentation-psycopg2/) for Postgres
- [opentelemetry-instrumentation-pymysql](https://pypi.org/project/opentelemetry-instrumentation-pymysql/) for MySQL
- [opentelemetry-instrumentation-sqlite3](https://pypi.org/project/opentelemetry-instrumentation-sqlite3/) for SQLite3
- Complete list [here](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation) -->


## Sample Application

- [Working example](https://github.com/SigNoz/opentelemetry-python/tree/main/docs/examples/fork-process-model) where we have configured a gunicorn server with `post_fork` hook.




<InstrumentationFAQ />
