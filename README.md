# Introduction

This image is used for automatic database backup on any S3 endpoint.

You can easily setup a cron on a kubernetes cluster for any kind of databases.

Image : `skyloud/job-backup-db` available on [docker hub](https://hub.docker.com/r/skyloud/job-backup-db) !

👉 https://hub.docker.com/r/skyloud/job-backup-db

| Kind         |    Status     |
| ------------ | :-----------: |
| **Postgres** |    ✔ Done     |
| **Mysql**    | ⚡️ In progress |
| **MariaDB**  |   ✔ Done      |
| **MongoDB**  |   ✔ Done      |

You can help us to contribute on our repos 🚀

## Other settings

How to follow backup jobs to be done with a heartbeat system. Use `BAGCLI_HEARTBEAT_URL` that will make a GET curl request at the end of job when succeed. Job will fail if request fail too.

## Postgres
# Configure S3 

You'll need to setup a policy for a newly created user which will be used for this backup.

Copy this policy in `postgres-prod-backup-policy.json` :

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::project/",
        "arn:aws:s3:::project/*"
      ]
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:DeleteObject",
        "s3:GetObject",
        "s3:ListBucket",
        "s3:PutObject"
      ],
      "Resource": [
        "arn:aws:s3:::project/prod/postgres/*",
        "arn:aws:s3:::project/prod/postgres"
      ]
    }
  ]
}
```

Configure your policy on minio :

```bash
mc admin policy add s3 project-prod-postgres-policy s3-postgres-prod-policy.json
mc admin user add s3 project-prod-postgres-user my_very_strong_key_uuid_v4
mc admin policy set s3 project-prod-postgres-policy user=project-prod-postgres-user
```
## MongoDB

You'll need to setup a policy for a newly created user which will be used for this backup.

Copy this policy in `mongodb-prod-backup-policy.json` :

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::project/",
        "arn:aws:s3:::project/*"
      ]
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:DeleteObject",
        "s3:GetObject",
        "s3:ListBucket",
        "s3:PutObject"
      ],
      "Resource": [
        "arn:aws:s3:::project/prod/mongodb/*",
        "arn:aws:s3:::project/prod/mongodb"
      ]
    }
  ]
}
```

Configure your policy on minio :

```bash
mc admin policy add s3 project-prod-mongodb-policy s3-mongodb-prod-policy.json
mc admin user add s3 project-prod-mongodb-user my_very_strong_key_uuid_v4
mc admin policy set s3 project-prod-mongodb-policy user=project-prod-mongodb-user
```

## MariaDB

You'll need to setup a policy for a newly created user which will be used for this backup.

Copy this policy in `mariadb-prod-backup-policy.json` :

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::project/",
        "arn:aws:s3:::project/*"
      ]
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:DeleteObject",
        "s3:GetObject",
        "s3:ListBucket",
        "s3:PutObject"
      ],
      "Resource": [
        "arn:aws:s3:::project/prod/mariadb/*",
        "arn:aws:s3:::project/prod/mariadb"
      ]
    }
  ]
}
```

Configure your policy on minio :

```bash
mc admin policy add s3 project-prod-mariadb-policy s3-mariadb-prod-policy.json
mc admin user add s3 project-prod-mariadb-user my_very_strong_key_uuid_v4
mc admin policy set s3 project-prod-mariadb-policy user=project-prod-mariadb-user
```

# How to use

## With docker

### Postgres Case

```bash
docker run --rm -it \
  -e BAGCLI_DATABASE_HOST="postgres.namespace.svc.cluster.local" \
  -e BAGCLI_DATABASE_USER="postgres" \
  -e BAGCLI_DATABASE_PASS="db_password" \
  -e BAGCLI_BUCKET_PATH="bucket/prod/postgres" \
  -e MC_HOST_s3="https://user:password@minio:9000" \
  skyloud/job-backup-db postgres database-name
```
### MongoDB case

```bash
docker run --rm -it \
  -e BAGCLI_DATABASE_URI="mongodb://mongoadmin:secret@mongodb-arbiter-0.mongodb-arbiter-headless.database:27017,mongodb-0.mongodb-headless.database:27017,mongodb-1.mongodb-headless.database:27017/mongodb_d_test?replicaSet=rs0&authSource=admin&retryWrites=true&w=majority" \
  -e BAGCLI_DATABASE_NAME="mongodb_d_test" \
  -e BAGCLI_BUCKET_PATH="bucket/prod/mongodb" \
  -e MC_HOST_s3="https://user:password@minio:9000" \
  skyloud/job-backup-db mongodb
```

### MariaDB case

```bash
docker run --rm -it \
  -e BAGCLI_MONGODB_HOST="mariadb.namespace.svc.cluster.local" \
  -e BAGCLI_MONGODB_PORT="3306" \
  -e BAGCLI_DATABASE_USER="test" \
  -e BAGCLI_DATABASE_PASS="test" \
  -e BAGCLI_BUCKET_PATH="bucket/prod/mariadb" \
  -e MC_HOST_s3="https://user:password@minio:9000" \
  skyloud/job-backup-db mongodb
```
## With Docker-compose

WIP
## With kubernetes

# PostgreSQL

> **Note :** Please update the file `kubernetes.yaml` with your own values.

```bash
kubectl apply -f kubernetes.yaml
```

# MongoDB

> **Note :** Please update the file `kubernetes_mongodb.yaml` with your own values.

```bash
kubectl apply -f kubernetes_mongodb.yaml
```

# MariaDB

> **Note :** Please update the file `kubernetes_mariadb.yaml` with your own values.

```bash
kubectl apply -f kubernetes_mariadb.yaml
```

Enjoy !

# ChangeLog

View our changelog [here](CHANGELOG.md)
