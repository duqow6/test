# Elastic Stack (ELK) on Docker

Run the latest version of the [Elastic Stack][elk-stack] with Docker and Docker Compose.

It allows you to analyze data using Elasticsearch's search/aggregation capabilities and Kibana's visualization power.

Based on the [official Docker images][elastic-docker]:

* [Elasticsearch](https://github.com/elastic/elasticsearch/tree/main/distribution/docker)
* [Logstash](https://github.com/elastic/logstash/tree/main/docker)
* [Kibana](https://github.com/elastic/kibana/tree/main/src/dev/build/tasks/os_packages/docker_generator)

## Quick Start

```sh
docker compose up setup
docker compose up
```

## Requirements

* [Docker Engine][docker-install] **18.06.0** or newer
* [Docker Compose][compose-install] **2.0.0** or newer
* 1.5 GB of RAM

### Exposed Ports

* 5044: Logstash Beats input
* 50000: Logstash TCP input
* 9600: Logstash monitoring API
* 9200: Elasticsearch HTTP
* 9300: Elasticsearch TCP transport
* 5601: Kibana

## Setup

Clone the repository and initialize the Elasticsearch users:

```sh
git clone https://github.com/deviantony/docker-elk.git
docker compose up setup
docker compose up
```

Access Kibana at <http://localhost:5601> with default credentials:

* user: *elastic*
* password: *changeme*

### Resetting User Passwords

```sh
docker compose exec elasticsearch bin/elasticsearch-reset-password --batch --user elastic
docker compose exec elasticsearch bin/elasticsearch-reset-password --batch --user logstash_internal
docker compose exec elasticsearch bin/elasticsearch-reset-password --batch --user kibana_system
```

Update passwords in the `.env` file accordingly.

## Configuration

### Elasticsearch

Edit [`elasticsearch/config/elasticsearch.yml`][config-es] or override settings via environment variables in `docker-compose.yml`:

```yml
elasticsearch:
  environment:
    network.host: _non_loopback_
    cluster.name: my-cluster
```

### Kibana

Edit [`kibana/config/kibana.yml`][config-kbn] or override settings via environment variables:

```yml
kibana:
  environment:
    SERVER_NAME: kibana.example.org
```

### Logstash

Edit [`logstash/config/logstash.yml`][config-ls] or override settings via environment variables:

```yml
logstash:
  environment:
    LOG_LEVEL: debug
```

## Extensibility

### Adding Plugins

1. Add a `RUN` statement in the `Dockerfile` (e.g., `RUN logstash-plugin install logstash-filter-json`).
2. Update the configuration files.
3. Rebuild images using `docker compose build`.

### JVM Tuning

Adjust memory allocation:

```yml
logstash:
  environment:
    LS_JAVA_OPTS: -Xms1g -Xmx1g
```

For more configurations, refer to official [Elastic Stack documentation][elastic-docs].

