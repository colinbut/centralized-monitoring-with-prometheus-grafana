# centralized-performance-monitoring-with-prometheus-grafana

## Performance Monitoring System

## Prometheus

Setup your application to be able to expose the metrics so Prometheus can scrape it (i.e. provide metrics Prometheus requires)
With a Spring Boot application this is very easy as all you need to do is add Spring Boot Actuator starter pack and configure in your configuration file to expose those data on the actuator endpoints (`/metrics`) thereby allowing Prometheus to pull from it (i.e. scrape it)

Very first step is to add in the required dependencies to your application:

1. Spring Boot Actuator
2. Micrometer-Core
3. Micrometer-Registry-Prometheus

Then these are the most important configuration you need in your Spring Boot application:

```yaml
management:
  metrics:
    export:
      prometheus:
        # enabling prometheus format metrics
        enabled: true
    distribution:
      #enabling percentile bucket exposure on metrics endpoint
      percentiles-histogram.http.server.requests: true 
    percentiles:
      # expose metrics for 95% and 99% percentile buckets
      http.server.requests: 0.95, 0.99
      # expose metric for requests that meet 50ms response time SLA
      sla.http.server.requests: 50ms
  endpoints:
    web:
      # by default Spring expose actuator endpoints on /actuator/ path but prometheus needs it be on /
      base-path: /
      exposure:
        include: "*"
      path-mapping:
        # prometheus looks at /metrics by default when scraping for metrics
        metrics: spring-metrics
        prometheus: metrics
```

See the sample `application.yml` in the application folder of this project repo as reference.

### Prometheus Configuration

Here is the sample `prometheus.yml` configuration file

```yaml
global:
  scrape_interval:     15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
   - "alerting-rules.yml"
  # - "second_rules.yml"

scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: 'your-application-name'

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
    - targets: ['localhost:8999']

```

The key configurations from above sample is ensuring the url and port in the targets section matches your application and the rule_files property configures the paths to the alerting rules files defined for prometheus to do the alerting via its AlertManager.

## Grafana

[TBD]

## Running the Metrics Monitoring System

### Start up your application

#### Maven

```bash
mvn spring-boot:run
```

or if using maven wrapper:
```bash
./mvnw spring-boot:run
```

#### Gradle

```bash
gradle bootRun
```

or if using gradle wrapper:
```bash
./gradlew bootRun
```

### Start Prometheus

```bash
./prometheus --config.file=prometheus.yml
```

### Start Grafana

[TBD]