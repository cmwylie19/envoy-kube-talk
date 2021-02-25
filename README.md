# Frontend Proxy
[slide deck](https://docs.google.com/presentation/d/17_IBwR4EoDFQ67o1lq-vzJQ8-lY_We_j5BPpPfi0JvU/edit?usp=sharing)   
During this demo we are going to deploy a front Envoy and a couple of services coloated with a running service Envoy.

The three containers will be deployed inside a virtual network called enovymesh using docker-compose.

Port `8080`, `8443`, and `8001` are exposed by docker-compose and can handle `HTTP`, `HTTPS` calls to services and requests to `/admin`.

In this demo, all requests are handled by the service Envoy, and efficiently routed to your services.

## Start Containers
```
docker-compose build --pull
docker-compose up -d
docker-compose ps
```

## Testing Routes
Route 1, `Service1`:
```
curl -v http://localhost:8080/service/1
```

Route 2, `Service2`:
```
curl -v http://localhost:8080/service/2
```

Using `HTTPS` to call services behind the front Envoy:
```
curl -k -v https://localhost:8443/service/1
```

## Load Balancing
Scale up `service1` nodes to demonstrate load balancing capabilities of Envoy:
```
docker-compose scale service1=3
```

Send 3 requests and watch as Envoy load balanes doing a round robin of the three services:
```
for x in $(seq 5); do curl -v localhost:8080/service/1; done
```

## Envoy Admin
When Envoy runs, it also attaches an `admin` to your desired port. We have `admin` bound to port `8001`.

- `/server_info` - info about the Envoy version running
- `/stats` - stats about the envoy server, number of requests fulfilled by them, info about http ingress

### Server Info
```
docker-compose exec front-envoy /bin/bash

curl localhost:8001/server_info
curl localhost:8001/stats
```