# OAI RF Simulation Workflow

This guide is the cleaned GitHub-friendly version of the attached setup notes.

## Working directory

```bash
cd ~/openairinterface5g/oai-cn5g-fed/docker-compose
```

## Recommended terminal split

- Terminal 1: Core Network
- Terminal 2: gNB + UE
- Terminal 3: Validation and debugging

## Start the core network

```bash
docker compose -f docker-compose-basic-nrf.yaml up -d
sleep 30
docker ps
```

Expected healthy services include:

- `oai-amf`
- `oai-smf`
- `oai-upf`
- `oai-udm`
- `oai-ausf`
- `oai-udr`
- `oai-nrf`
- `mysql`
- `oai-ext-dn`

## Prepare and verify Docker networks

The notes indicate the public network should exist and the N3 network may need to be created manually:

```bash
docker network ls
docker network create --subnet 192.168.71.0/24 demo-oai-n3-net
docker network ls
```

## Important compose-file note

In the `rfsim` compose file, the notes say `public_net` and `n3_net` should be marked `external: true` and linked to:

- `demo-oai-public-net`
- `demo-oai-n3-net`

## Start simulated gNB + UE

```bash
docker compose -f docker-compose-oai-rfsim-ebpf.yaml up -d
sleep 20
```

## Validate control plane

```bash
docker logs oai-nr-ue1 | grep -E "Registration|Accept"
docker logs oai-amf | tail -50
docker logs oai-gnb --tail 50
```

Good signs include:

- `Received Registration Accept`
- `Received PDU Session Establishment Accept`
- `5GMM-REGISTERED`

## Validate data plane

Enter the UE container:

```bash
docker exec -it oai-nr-ue1 bash
ip a | grep oaitun
ip route
```

Expected UE tunnel behavior:

- `oaitun` interface present
- UE IP similar to `12.1.1.2/24`
- default route via `12.1.1.1`

Connectivity tests:

```bash
ping -c 3 192.168.70.4
ping -c 3 8.8.8.8
ping -c 3 1.1.1.1
ping -c 3 142.250.183.110
```

## Check UPF tunnel

```bash
docker exec -it oai-upf bash
ip a
```

The notes expect `tun0` with an address like `12.1.1.1`.

## Tear down

```bash
docker compose -f docker-compose-oai-rfsim-ebpf.yaml down
docker compose -f docker-compose-basic-nrf.yaml down
```
