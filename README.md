# Innog Workshop

This is a simplified copy of [docker_open5gs](https://github.com/herlesupreeth/docker_open5gs) which we are using for [Workshop 3 of Innog8](
 https://innog.net/cyber-security-and-cryptography/)

This repository contains docker files deploy a simulated 5G network using docker as the underlying virtualisation technology :

- Core Network (4G/5G) - open5gs - <https://github.com/open5gs/open5gs>
- UERANSIM (5G gNB + 5G UE) - <https://github.com/aligungr/UERANSIM>

## Tested Setup

Requirements:
Any Machine with ability to run Docker Desktop, 4GBs of RAM, and 20GBs of storage.

## Prepare Docker images

### Get Pre-built Docker images

Pull base images:

```bash
docker pull ghcr.io/herlesupreeth/docker_open5gs:master
docker tag ghcr.io/herlesupreeth/docker_open5gs:master docker_open5gs

docker pull ghcr.io/herlesupreeth/docker_grafana:master
docker tag ghcr.io/herlesupreeth/docker_grafana:master docker_grafana

docker pull ghcr.io/herlesupreeth/docker_metrics:master
docker tag ghcr.io/herlesupreeth/docker_metrics:master docker_metrics
```

You can also pull the pre-built images for additional components
For UERANSIM components:

```bash
docker pull ghcr.io/herlesupreeth/docker_ueransim:master
docker tag ghcr.io/herlesupreeth/docker_ueransim:master docker_ueransim
```

### Build Docker images from source

#### Clone repository and build base docker image of open5gs, kamailio, srsRAN_4G, srsRAN_Project, ueransim

```bash
# Build docker images for open5gs EPC/5GC components
git clone https://github.com/herlesupreeth/docker_open5gs
cd docker_open5gs/base
docker build --no-cache --force-rm -t docker_open5gs .

# Build docker images for UERANSIM (gNB + UE)
cd ../ueransim
docker build --no-cache --force-rm -t docker_ueransim .
```

#### Build docker images for additional components

```bash
source .env
# For 5G deployment only
docker compose -f sa-deploy.yaml build
```

## Network and deployment configuration

### Single Host setup configuration

Edit only the following parameters in **.env** as per your setup

```txt
MCC
MNC
DOCKER_HOST_IP --> This is the IP address of the host running your docker setup
```

## Network Deployment

###### 5G SA deployment

```bash
# 5G Core Network
docker compose -f sa-deploy.yaml up

# UERANSIM gNB (RF simulated)
docker compose -f nr-gnb.yaml up -d && docker container attach nr_gnb

# UERANSIM NR-UE (RF simulated)
docker compose -f nr-ue.yaml up -d && docker container attach nr_ue
```

## Provisioning of SIM information

### Provisioning of SIM information in open5gs HSS as follows

Open (http://<DOCKER_HOST_IP>:9999) in a web browser, where <DOCKER_HOST_IP> is the IP of the machine/VM running the open5gs containers. Login with following credentials

```
Username : admin
Password : 1423
```

Using Web UI, add a subscriber

#### or using cli

```
sudo docker exec -it hss misc/db/open5gs-dbctl add 001010123456790 8baf473f2f8fd09487cccbd7097c6862 8E27B6AF0E692E750F32667A3B14605D
```
