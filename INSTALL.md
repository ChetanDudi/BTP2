# Installation and Setup

This project depends on external OpenAirInterface repositories and Docker-based deployment. This repository documents the workflow; it does not vendor the full OAI source tree.

## Host prerequisites

Install the mandatory packages on Ubuntu/Linux:

```bash
sudo apt install -y \
  git \
  curl \
  wget \
  net-tools \
  iproute2 \
  iptables \
  python3 \
  python3-pip \
  build-essential \
  cmake \
  linux-headers-$(uname -r)
```

## Install Docker

```bash
curl -fsSL https://get.docker.com | sudo bash
sudo usermod -aG docker $USER
newgrp docker
docker run hello-world
sudo apt install docker-compose-v2
docker compose version
```

## Firewall note

The project notes indicate that OAI setup becomes unreliable with the firewall enabled:

```bash
sudo ufw disable
```

## Clone the external OAI repositories

```bash
git clone https://gitlab.eurecom.fr/oai/openairinterface5g.git
cd ~/openairinterface5g
git clone https://gitlab.eurecom.fr/oai/cn5g/oai-cn5g-fed.git
```

## Working directory used in the guides

Most commands in this repo assume:

```text
~/openairinterface5g/oai-cn5g-fed/docker-compose
```

## Next step

After the prerequisites are ready, follow:

- [`docs/guides/oai-rfsim-workflow.md`](docs/guides/oai-rfsim-workflow.md)
- [`docs/guides/lmf-experimentation.md`](docs/guides/lmf-experimentation.md)
