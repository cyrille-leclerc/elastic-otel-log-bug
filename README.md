
# How to run the test case

* Prerequisite: setup Elastic 8.0 with Elasticsearch and APM Server. We also recommend an Otel Collector configured to send traces, metrics, and logs to Elastic 
   * elasticsearch.url=
   * elasticsearch.username=
   * elasticsearch.password=
   * otel.exporter.otlp.endpoint=
* create a file `src/main/resources/.env` copying `src/main/resources/.env.template`
* run
```shell
./mvnw package exec:java
```

# Example OpenTelemetry Collector configuration

````yaml
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: '127.0.0.1:4317'

  #...

processors:
  batch:
exporters:
  otlp/elasticcloud:
    endpoint: "***.cloud.es.io:443"
    headers:
      Authorization: "Bearer ***"      
  logging:
    loglevel: warn
  logging/debug:
    loglevel: debug
service:
  pipelines:
    metrics:
      receivers: [otlp]
      processors: [batch]
      exporters: [otlp/elasticcloud, logging]
    traces:
      receivers: [otlp]
      processors: [batch]
      exporters: [otlp/elasticcloud, logging]
    logs:
      receivers: [otlp]
      processors: [batch]
      exporters: [otlp/elasticcloud, logging/debug]
````