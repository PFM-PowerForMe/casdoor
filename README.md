# Casdoor

[![PFM-Upstream-Sync](https://github.com/PFM-PowerForMe/casdoor/actions/workflows/fork-sync.yml/badge.svg)](https://github.com/PFM-PowerForMe/casdoor/actions/workflows/fork-sync.yml)

## 简介
开源UI优先身份和访问管理（IAM）/单点登录（SSO）平台，Web UI支持OAuth 2.0、OIDC、SAML、CAS、LDAP、SCIM、WebAuthN、TOTP、MFA、Face ID、RADIUS、Google Workspace、Active Directory和Kerberos 

## 如何部署?

1. 前置条件:
```shell
mkdir -p /etc/containers/systemd
podman network create deploy
```

2. 部署 Casdoor
```
mkdir -p /opt/podman-data/env
mkdir -p /opt/podman-data/casdoor/conf
nvim /opt/podman-data/casdoor/conf/app.conf

```
```
appname = casdoor
httpport = 8000
runmode = prod
SessionOn=true
copyrequestbody=true
driverName = postgres
dataSourceName = user=user password=passwd host=localhost port=5432 sslmode=disable dbname=db
dbName = db
tableNamePrefix = casdoor_
showSql = false
redisEndpoint = valkey:6379,2
isCloudIntranet = false
authState = "casdoor"
verificationCodeTimeout = 10
initScore = 0
logPostOnly = true
isUsernameLowered = false
isDemoMode = false
batchSize = 100
enableErrorMask = false
enableGzip = true

```
```
nvim /etc/containers/systemd/casdoor.container
```
```
# /etc/containers/systemd/casdoor.container

[Unit]
Description=The casdoor container
Wants=postgresql.service valkey.service
After=postgresql.service valkey.service

[Container]
AutoUpdate=registry
ContainerName=casdoor
Timezone=local
Network=deploy
Volume=/opt/podman-data/casdoor/conf:/conf
PublishPort=127.0.0.1:6003:8000
Image=ghcr.io/pfm-powerforme/casdoor:latest

[Service]
Restart=on-failure
RestartSec=30s
StartLimitInterval=30
TimeoutStartSec=900
TimeoutStopSec=70

[Install]
WantedBy=multi-user.target default.target
```
