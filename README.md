# CST8915 Lab 5: Algonquin Pet Store with Docker

The final `docker-compose.yaml` runs published Docker Hub images. It contains no local build steps. The Product Service uses **Python**.

## Demo video

The required YouTube demonstration is pending recording/upload. This repository is not yet ready for Brightspace submission without that link.

## Service forks

- [Order Service](https://github.com/chuk003-cloudops/order-service-L4)
- [Python Product Service](https://github.com/chuk003-cloudops/product-service-python-L4)
- [Store Front](https://github.com/chuk003-cloudops/store-front-L4)

Each fork contains the Dockerfile specified in the instructor's [Lab 5 instructions](https://github.com/ramymohamed10/26F_CST8915_Lab5).

## Public Docker Hub images

- [dubiousx/order-service:latest](https://hub.docker.com/r/dubiousx/order-service)
- [dubiousx/product-service:latest](https://hub.docker.com/r/dubiousx/product-service)
- [dubiousx/store-front:latest](https://hub.docker.com/r/dubiousx/store-front)

## Run locally

1. Copy `.env.example` to `.env`.
2. Replace the RabbitMQ password placeholder with your own alphanumeric password.
3. Run:

```sh
docker compose config --quiet
docker compose pull
docker compose up -d --wait
docker compose ps
```

Open <http://localhost:8080>. The Store Front image was built with **http://localhost:3000** for orders and **http://localhost:3030** for products. These URLs must be reachable from the browser. Changing `API_HOST` at runtime does not rebuild the static frontend; use the Store Front fork to rebuild and publish the image for a different API host.

RabbitMQ's management interface is at <http://localhost:15672>. Use the credentials in your local `.env`; do not publish them or show them in a recording.

## Verify

```sh
curl http://localhost:3030/products
curl -i -X POST http://localhost:3000/orders -H 'Content-Type: application/json' -d '{"product":{"id":1,"name":"Dog Food","price":19.99},"quantity":2,"totalPrice":39.98}'
docker compose exec rabbitmq rabbitmqctl list_queues name durable messages
```

The catalog contains three products. A valid Dog Food order for quantity 2 totals $39.98 and returns `Order received`. The `order_queue` queue is durable and its message count increases. This lab intentionally has no consumer, so queued orders remain in the broker.

The Order Service's 17 supplied tests passed, and the frontend passed its supplied lint and production-build commands. The stack has also been pulled from Docker Hub and started without local builds.

## Stop

```sh
docker compose down
```

This preserves the RabbitMQ named volume. Existing volume data includes the broker account, so changing the password in `.env` alone does not update that stored account.
