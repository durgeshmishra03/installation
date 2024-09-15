### Installation of APISIX on Docker Container

Follow these step-by-step instructions to install Apache APISIX in a Docker container.

### Step 1: Create a Directory for APISIX
```bash
mkdir APISIX
cd APISIX
```

### Step 2: Create the `docker-compose.yml` File
In the `APISIX` directory, create a `docker-compose.yml` file:
```bash
vim docker-compose.yml
```
Paste the following content into the file:
```yaml
version: "3"

services:
  apisix-dashboard:
    image: apache/apisix-dashboard:3.0.0-alpine
    restart: always
    volumes:
    - ./dashboard_conf/conf.yaml:/usr/local/apisix-dashboard/conf/conf.yaml
    ports:
    - "9000:9000"
    networks:
      apisix:

  apisix:
    image: apache/apisix:${APISIX_IMAGE_TAG:-3.1.0-debian}
    restart: always
    volumes:
      - ./apisix_conf/config.yaml:/usr/local/apisix/conf/config.yaml:ro
    depends_on:
      - etcd
    ports:
      - "9180:9180/tcp"
      - "9080:9080/tcp"
      - "9091:9091/tcp"
      - "9443:9443/tcp"
      - "9092:9092/tcp"
    networks:
      apisix:

  etcd:
    image: bitnami/etcd:3.4.15
    restart: always
    volumes:
      - etcd_data:/bitnami/etcd
    environment:
      ETCD_ENABLE_V2: "true"
      ALLOW_NONE_AUTHENTICATION: "yes"
      ETCD_ADVERTISE_CLIENT_URLS: "http://etcd:2379"
      ETCD_LISTEN_CLIENT_URLS: "http://0.0.0.0:2379"
    ports:
      - "2379:2379/tcp"
    networks:
      apisix:

networks:
  apisix:
    driver: bridge

volumes:
  etcd_data:
    driver: local
```

Save the file and verify it with:
```bash
cat docker-compose.yml
```

### Step 3: Create `dashboard_conf` Directory and Configuration File
```bash
mkdir dashboard_conf
vim dashboard_conf/conf.yaml
```
Insert the following content into the `conf.yaml` file:
```yaml
conf:
  listen:
    host: 0.0.0.0
    port: 9000
  allow_list:
    - 0.0.0.0/0
  etcd:
    endpoints:
      - "http://etcd:2379"
    mtls:
      key_file: ""
      cert_file: ""
      ca_file: ""
  log:
    error_log:
      level: warn
      file_path:
        logs/error.log
    access_log:
      file_path:
        logs/access.log
  security:
    content_security_policy: "default-src 'self'; script-src 'self' 'unsafe-eval' 'unsafe-inline'; style-src 'self' 'unsafe-inline'; frame-src *"
authentication:
  secret:
    secret
  expire_time: 3600
  users:
    - username: admin
      password: admin
    - username: user
      password: user
plugins:
  - api-breaker
  - authz-keycloak
  - basic-auth
  - batch-requests
  - consumer-restriction
  - cors
  - echo
  - fault-injection
  - grpc-transcode
  - hmac-auth
  - http-logger
  - ip-restriction
  - jwt-auth
  - kafka-logger
  - key-auth
  - limit-conn
  - limit-count
  - limit-req
  - openid-connect
  - prometheus
  - proxy-cache
  - proxy-mirror
  - proxy-rewrite
  - redirect
  - referer-restriction
  - request-id
  - request-validation
  - response-rewrite
  - serverless-post-function
  - serverless-pre-function
  - sls-logger
  - syslog
  - tcp-logger
  - udp-logger
  - uri-blocker
  - wolf-rbac
  - zipkin
  - server-info
  - traffic-split
```

Save and verify it:
```bash
cat dashboard_conf/conf.yaml
```

### Step 4: Create `apisix_conf` Directory and Configuration File
```bash
mkdir apisix_conf
vim apisix_conf/config.yaml
```
Paste the following into the `config.yaml` file:
```yaml
apisix:
  node_listen: 9080
  enable_ipv6: false

  enable_control: true
  control:
    ip: "0.0.0.0"
    port: 9092

deployment:
  admin:
    allow_admin:
      - 0.0.0.0/0

    admin_key:
      - name: "admin"
        key: edd1c9f034335f136f87ad84b625c8f1
        role: admin

      - name: "viewer"
        key: 4054f7cf07e344346cd3f287985e76a2
        role: viewer

  etcd:
    host:
      - "http://etcd:2379"
    prefix: "/apisix"
    timeout: 30

plugin_attr:
  prometheus:
    export_addr:
      ip: "0.0.0.0"
      port: 9091
```

Save and verify it:
```bash
cat apisix_conf/config.yaml
```

### Step 5: Start APISIX with Docker Compose
```bash
docker-compose up -d
```

### Step 6: Check if Containers are Running
```bash
docker-compose ps
```

### Step 7: Confirm APISIX is Running
Run this `curl` command:
```bash
curl "http://192.168.29.117:9180/apisix/admin/services/" -H 'X-API-KEY: edd1c9f034335f136f87ad84b625c8f1'
```
If successful, you should see this output:
```json
{"total":0,"list":[]}
```

This confirms that APISIX is running successfully.
