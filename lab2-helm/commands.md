## PostgreSQL

    helm search postgresql
    helm install --name confy-database  --set postgresUser=confy,postgresPassword=confy01 stable/postgresql


## RabbitMQ

    helm search rabbitmq
    helm install --name confy-queue --set rabbitmqUsername=confy,rabbitmqPassword=confy01 stable/rabbitmq

## Confy

    helm install --name=production  .
    [Änderungen]
    helm upgrade production .
