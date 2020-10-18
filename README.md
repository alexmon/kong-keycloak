Reference: https://www.jerney.io/secure-apis-kong-keycloak-1/

build
```
docker build --rm -t kong:2.1.4-ubuntu-oidc kong/
```
start db
```
docker-compose up -d kong-db 
```
run migrations
```
docker-compose run --rm kong kong migrations bootstrap -vv
docker-compose run --rm kong kong migrations up -vv
```
start kong
```
docker-compose up -d kong
```
check if oidc is set, should return true
```
curl -s http://localhost:8001 | jq .plugins.available_on_server.oidc
```
register mockbin.org backend service on kong
```
curl -s -X POST http://localhost:8001/services \
    -d name=mock-service \
    -d url=http://mockbin.org/request \
    | python -m json.tool
```
should return something like:
```
{
            "ca_certificates": null,
            "client_certificate": null,
            "connect_timeout": 60000,
            "created_at": 1603042785,
            "host": "mockbin.org",
            "id": "d17cda09-1944-4544-a35c-72d1236abbc9",
            "name": "mock-service",
            "path": "/request",
            "port": 80,
            "protocol": "http",
            "read_timeout": 60000,
            "retries": 5,
            "tags": null,
            "tls_verify": null,
            "tls_verify_depth": null,
            "updated_at": 1603042785,
            "write_timeout": 60000
        }
```        
register route /mock and map it on a backend service, use service id from previous command
```
curl -s -X POST http://localhost:8001/routes \
    -d service.id=d17cda09-1944-4544-a35c-72d1236abbc9 \
    -d paths[]=/mock \
    | python -m json.tool
```
test backend is accessible
```
curl -s http://localhost:8000/mock
```
start keycloak db
```
docker-compose up -d keycloak-db
```
start keycloak
```
docker-compose up -d keycloak
```

See also:
https://hub.docker.com/r/pantsel/konga/