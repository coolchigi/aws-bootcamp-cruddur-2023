# Week 2 — Distributed Tracing

## Welcome to week 2!
Our focus on this week is as the name implies distributed tracing. 

## What is Distributed Tracing? ## 
Distributed tracing is a technique that addresses logging information in microservice-based applications. Distributed tracing is a crucial technique for understanding the behavior of complex, distributed systems. By tracing requests across different services and components, developers can gain visibility into how these systems behave and identify and troubleshoot any issues that may arise.

AWS CloudWatch is a monitoring and observability service provided by Amazon Web Services (AWS) that can be used for distributed tracing. It offers features such as metrics, logs, and alarms to provide a comprehensive view of the performance and health of your applications and infrastructure.

Honeycomb is a commercial service that offers distributed tracing, logging, and metrics for cloud-native applications. It allows you to explore and analyze data in real-time, making it easy to identify and diagnose issues in your system.

AWS X-Ray is another distributed tracing tool provided by AWS. It helps developers to analyze and debug distributed applications, allowing you to visualize the different components of your application and track requests as they move through your system.


## Here are a few examples to illustrate the concept: ##

Let's say you have an e-commerce website that relies on several microservices to process orders. A customer places an order, and you notice that the order is taking longer than usual to process. By using distributed tracing, you can trace the request as it moves through each microservice, identify the slowest service, and determine where the bottleneck is.

Another example is in a banking system that involves several different services for handling transactions, user authentication, and more. If a customer reports a problem with their account, you can use distributed tracing to trace the request that led to the problem and identify which service is responsible.

**As always here's a poem** 
```
In a world of complex systems, 🌎
Where services are spread out far, 🕸️
Distributed tracing is the key, 🔑
To understanding where things are. 🕵️‍♂️

With tracing, we can see the flow, 🌊
Of requests as they move along, 🏃‍♀️
And pinpoint where the issues lie, 🔍
When something goes wrong. 💥

We can trace across different services, 🔍
And components of all shapes and sizes, 📦
With tools like Jaeger, Zipkin, and more, 🔧
Our troubleshooting becomes surprises. 🤯

So embrace distributed tracing, 👐
And let it be your guiding light, 💡
For when the system gets too complex, 🤯
Distributed tracing will set it right. ✅
```


## Install Honeycomb

On the backend-flask/requirements.text, add the following code

File name can be anything you want, it doesnt have to be requirements.txt
```
opentelemetry-api 
opentelemetry-sdk 
opentelemetry-exporter-otlp-proto-http 
opentelemetry-instrumentation-flask 
opentelemetry-instrumentation-requests
```

install the dependency. this will necessary just this time as it will be run via docker compose
```
pip install -r {filename}.txt
```

Add the following on the app.py
```
# Honeycomb
from opentelemetry import trace
from opentelemetry.instrumentation.flask import FlaskInstrumentor
from opentelemetry.instrumentation.requests import RequestsInstrumentor
from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

# Honeycomb
# Initialize tracing and an exporter that can send data to Honeycomb
provider = TracerProvider()
processor = BatchSpanProcessor(OTLPSpanExporter())
provider.add_span_processor(processor)
trace.set_tracer_provider(provider)
tracer = trace.get_tracer(__name__)

# Honeycomb
# Initialize automatic instrumentation with Flask
FlaskInstrumentor().instrument_app(app)
RequestsInstrumentor().instrument()
```

from the docker-compose.yml, add the following code for the env variables
```
OTEL_SERVICE_NAME: 'backend-flask'
OTEL_EXPORTER_OTLP_ENDPOINT: "https://api.honeycomb.io"
OTEL_EXPORTER_OTLP_HEADERS: "x-honeycomb-team=${HONEYCOMB_API_KEY}"
```

from honeycomb.io, grab the unique code and create the gitpod env var
```
gp env HONEYCOMB_API_KEY=""
```

To create span and attribute, add the following code on the home_activities.py
```
from opentelemetry import trace
tracer = trace.get_tracer("home.activities")
```

```
with tracer.start_as_current_span("home-activities-mock-data"):
    span = trace.get_current_span()
```
```
span.set_attribute("app.now", now.isoformat())
 ```
 at the end of the code, put the following
 ```
span.set_attribute("app.result_lenght", len(results))

 ```

More information can be found here: [OpenTelemetry for Python](https://docs.honeycomb.io/getting-data-in/opentelemetry/python/)

***A poem to liven up the mood***
```
I faced a block, a backend woe 😔
For hours I tried, but nothing would flow 🤔
Then, a eureka moment came to me 💡
A missing requirements.txt file was the key 🔑

Take note, dear friend, remember its name 📝
And in the backend-flask directory, install the same 💻
Don't let this mishap cause you strife 🙅‍♂️
Stay sharp and code on with newfound life! 💪🚀

```


## Install Xray

 check the env var for the AWS region using the following command
 ```
 env | grep AWS_REGION
 ```

 If there is no result
 ```
export AWS_REGION="ca-central-1"
gp env AWS_REGION="ca-central-1"

 ```

from the backend-flask requirements.text, insert the following
```
aws-xray-sdk
```

install the dependency. this will necessary just this time as it will be run via docker compose
```
pip install -r requirements.txt
```

Insert the following code inside the app.py

```
# Xray
from aws_xray_sdk.core import xray_recorder
from aws_xray_sdk.ext.flask.middleware import XRayMiddleware
```
```
# Xray
xray_url = os.getenv("AWS_XRAY_URL")
xray_recorder.configure(service='backend-flask', dynamic_naming=xray_url)
```

Create xray.json inside the folder of /aws/json/ and insert the following code

```
{
    "SamplingRule": {
        "RuleName": "Cruddur",
        "ResourceARN": "*",
        "Priority": 9000,
        "FixedRate": 0.1,
        "ReservoirSize": 5,
        "ServiceName": "backend-flask",
        "ServiceType": "*",
        "Host": "*",
        "HTTPMethod": "*",
        "URLPath": "*",
        "Version": 1
    }
  }
```

launch the following command from to create the group on xray from the backend-flask folder
```
aws xray create-group \
   --group-name "Cruddur" \
   --filter-expression "service(\"backend-flask")"
```

from the main folder type the following command to create the sample rule
```
aws xray create-sampling-rule --cli-input-json file://aws/json/xray.json
```

add this line for the dockercompose.yml
```
AWS_XRAY_URL: "*4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}*"
AWS_XRAY_DAEMON_ADDRESS: "xray-daemon:2000"
```
this code will create the daemon necessary for Xray
```
xray-daemon:
    image: "amazon/aws-xray-daemon"
    environment:
      AWS_ACCESS_KEY_ID: "${AWS_ACCESS_KEY_ID}"
      AWS_SECRET_ACCESS_KEY: "${AWS_SECRET_ACCESS_KEY}"
      AWS_REGION: "eu-west-2"
    command:
      - "xray -o -b xray-daemon:2000"
    ports:
      - 2000:2000/udp
```

```
simple_processor = SimpleSpanProcessor(ConsoleSpanExporter())
provider.add_span_processor(simple_processor)
```

```
# xray
XRayMiddleware(app, xray_recorder)
```

Make sure this line is after you've declared the "app" variable in your app.py

More information can be found here: [Xray Sdk Python](https://github.com/aws/aws-xray-sdk-python)