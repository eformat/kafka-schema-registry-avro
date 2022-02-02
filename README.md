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
mvn quarkus:dev \
  -Dquarkus.devservices.enabled=false \
  -Dmp.messaging.connector.smallrye-kafka.apicurio.registry.url=http://localhost:8081/apis/registry/v2
```

## RHOSAK

Try running Kafka, Registry as cloud managed services:

- [Red Hat Kafka Managed Service](https://console.redhat.com/beta/application-services/streams/kafkas)
- [Red Hat Registry Managed Service](https://console.redhat.com/beta/application-services/service-registry)

Login, create a Kafka instance, movies Topic and Schema Registry. Add a service account that has read-write access to these.

Export Env Vars from console

```bash
export BOOTSTRAP_SERVER=moives-c-sv--qt--tmi--nl-lg.bf2.kafka.rhcloud.com:443
export CLIENT_ID=srvc-acct-c7541c66-513f-4b77-96aa-8a30cd015370
export CLIENT_SECRET=<secret>
export OAUTH_TOKEN_ENDPOINT_URI=https://identity.api.openshift.com/auth/realms/rhoas/protocol/openid-connect/token
export REGISTRY_URL=https://bu98.serviceregistry.rhcloud.com/t/4130e1e8-2678-4c33-b4da-e87959840d57/apis/registry/v2
export OAUTH_SERVER_URL=https://identity.api.openshift.com/auth
export OAUTH_REALM=rhoas
```

Now start the app using the `rhosak` profile vars in `application.properties`

```bash
mvn quarkus:dev \
  -Dquarkus.devservices.enabled=false \
  -Dquarkus.profile=dev,rhosak
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

Try some performance testing

```bash
hey -t 30 -c 100 -n 10000 -H "Content-Type: application/json;charset=utf-8" -m POST -d '{"title":"The Godfather","year":1972}' http://localhost:8080/movies
```
