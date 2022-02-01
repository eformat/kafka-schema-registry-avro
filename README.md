# kafka-avro-schema-quickstart

- https://quarkus.io/guides/kafka-schema-registry-avro

## Dev Services Mode

This will start kafka and schema registry using quarkus dev services mode (testcontainers). If you are using `podman` take a look at this [link](https://quarkus.io/blog/quarkus-devservices-testcontainers-podman/) to setup `podman-docker` and a systemd socket.

Now locally just run:

```bash
mvn quarkus:dev
```

## Use podman-compose for services locally

You can run the dev mode services locally using containers

```bash
podman-compose up -d
```

- Schema Registry should now be available on http://localhost:8081
- Kafka Bootstrap should now be avaiable on http://localhost:9092

Now start app with dev mode service disabled

```bash
mvn \
  -Dquarkus.devservices.enabled=false \
  -Dmp.messaging.connector.smallrye-kafka.apicurio.registry.url=http://localhost:8081/apis/registry/v2 quarkus:dev
```

## Testing

Create a consumer

```bash
curl -N http://localhost:8080/consumed-movies
```

Produce some movies

```bash
curl --header "Content-Type: application/json" \
  --request POST \
  --data '{"title":"The Godfather","year":1972}' \
  http://localhost:8080/movies

curl --header "Content-Type: application/json" \
  --request POST \
  --data '{"title":"The Dark Knight","year":2008}' \
  http://localhost:8080/movies
```
