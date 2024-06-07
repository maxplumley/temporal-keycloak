# Temporal JWT Authentication with Keycloak

Minimal project for demonstrating Temporal Auth with Keycloak

## Docker Compose

Built with reference to official Docker Compose [configurations](https://github.com/temporalio/docker-compose) on GitHub

## Demo

Prerequisites

- clone [Temporal API](https://github.com/temporalio/api/tree/master)
- install [gRPCurl](https://github.com/fullstorydev/grpcurl)

### Start with Docker Compose

start Keycloak

```shell
docker-compose up -d keycloak
```

get a token

```shell
export TEMPORAL_CLI_AUTH=$(http --form :8880/realms/temporal/protocol/openid-connect/token client_id=temporal-admin client_secret=kqDoa2+jTLpdO5Ab4yqAAqrcDN7AvMXe grant_type=client_credentials scope=temporal-system:admin | jq -r .access_token)
```

start the Temporal services

```shell
docker-compose up -d
```

### Connect to Temporal Cluster using token and list all Namespaces

get a Temporal system administrator token

```shell
export TEMPORAL_CLI_AUTH=$(http --form :8880/realms/temporal/protocol/openid-connect/token client_id=temporal-admin client_secret=kqDoa2+jTLpdO5Ab4yqAAqrcDN7AvMXe grant_type=client_credentials scope=temporal-system:admin | jq -r .access_token)
```

```shell
grpcurl -plaintext \
  -import-path ../temporal-api \
  -proto temporal/api/workflowservice/v1/service.proto \
  -H "authorization: Bearer ${TEMPORAL_CLI_AUTH}" \
  -d "{}" \
  localhost:7233 temporal.api.workflowservice.v1.WorkflowService/ListNamespaces
```

## Apendix

### Get a token

for the Temporal system administrator

```shell
http --form :8880/realms/temporal/protocol/openid-connect/token client_id=temporal-admin client_secret=kqDoa2+jTLpdO5Ab4yqAAqrcDN7AvMXe grant_type=client_credentials scope=temporal-system:admin
```

for the `default` domain worker

```shell
http --form :8880/realms/temporal/protocol/openid-connect/token client_id=default-worker client_secret=L4xybeKPwIIPg8OizgSv/OJBF+OnzVgD grant_type=client_credentials scope=default:worker
```