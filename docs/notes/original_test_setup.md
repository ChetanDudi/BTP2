# OpenAirInterface (OAI) — Local 5G Simulation Workflow

**Overview**
- **Purpose:** Step-by-step instructions to run a local end-to-end 5G simulation (rfsim) with Docker.
- **Assumption:** You are using simulation mode (`rfsim`) and Docker. All `docker compose` commands must be run from the workspace docker-compose directory below.

**Directory**
- **Use:** All commands are executed from the docker-compose directory.
- **Path:** `~/openairinterface5g/oai-cn5g-fed/docker-compose`

**Terminal Layout (recommended)**
- **Terminal 1:** Core Network
- **Terminal 2:** gNB + UE (RAN)
- **Terminal 3:** Testing & Debug

**Start Core Network (Terminal 1)**
- Change directory and start the core services:

```bash
cd ~/openairinterface5g/oai-cn5g-fed/docker-compose
docker compose -f docker-compose-basic-nrf.yaml up -d
sleep 30
```

- Verify core containers are running and healthy:

```bash
docker ps
```

- Expected containers (examples): `oai-amf`, `oai-smf`, `oai-upf`, `oai-udm`, `oai-ausf`, `oai-udr`, `oai-nrf`, `mysql`, `oai-ext-dn` — all should report healthy.

**Start gNB + UE (Terminal 2)**
- From same directory start the RAN simulation:

```bash
cd ~/openairinterface5g/oai-cn5g-fed/docker-compose
docker compose -f docker-compose-oai-rfsim-ebpf.yaml up -d
sleep 20
```

**Verify Control Plane (Terminal 3)**
- Check UE registration and PDU session on the UE container logs:

```bash
cd ~/openairinterface5g/oai-cn5g-fed/docker-compose
docker logs oai-nr-ue1 | grep -E "Registration|Accept"
```

- You should see lines like:
  - `Received Registration Accept`
  - `Received PDU Session Establishment Accept`

- Check AMF UE registration state:

```bash
docker logs oai-amf | tail -50
```

- You should see `5GMM-REGISTERED` indicating control-plane registration succeeded.

**Data Plane Tests (inside UE container)**
- Enter the UE container and inspect tunnel interface:

```bash
docker exec -it oai-nr-ue1 bash
ip a | grep oaitun
```

- Expected UE IP on `oaitun` interface: `inet 12.1.1.2/24`

Ping tests (run in this order):
1) Docker external DN (internal/host network test):

```bash
ping -c 3 192.168.70.4
```

- If this works, Docker networking between containers is OK.

2) Internet reachability (end-to-end data path):

```bash
ping -c 3 8.8.8.8
```

- If this works the full data path is working:
  UE → gNB → AMF → SMF → UPF → EXT-DN → Host → Internet

3) Additional IPs (verify NAT):

```bash
ping -c 3 1.1.1.1
ping -c 3 142.250.183.110
```

4) Confirm UE routing:

```bash
ip route
```

- You should see `default via 12.1.1.1 dev oaitun_ue1`.

**Optional: Check UPF Tunnel**
- In a new host terminal:

```bash
docker exec -it oai-upf bash
ip a
```

- Expect to see `tun0` with `12.1.1.1` assigned.

**LMF (Location Management Function) Test**
- From host (in project root or wherever LMF/HTTP endpoint is reachable on `localhost:8000`):

```bash
curl -X POST http://localhost:8000/nlmf-loc/v1/determine-location \
  -H "Content-Type: application/json" \
  -d '{"supi":"imsi-208950000000036","cellId":"0000e014e"}'
```

**Stop Everything (tear down)**
- Stop RAN first (Terminal 2):

```bash
cd ~/openairinterface5g/oai-cn5g-fed/docker-compose
docker compose -f docker-compose-oai-rfsim-ebpf.yaml down
```

- Then stop Core (Terminal 1):

```bash
docker compose -f docker-compose-basic-nrf.yaml down
```

**Rebuild LMF (if needed)**

```bash
docker compose -f docker-compose-basic-nrf.yaml build
```

-->See Dashboard for LMF: (in lmf directory): use command python3 dashboard.py

**What each component does**
- **UE:** Simulated mobile device
- **gNB:** Simulated radio access node
- **AMF:** Mobility & registration control
- **SMF:** PDU session management
- **UPF:** User plane forwarding
- **EXT-DN:** External data network / Internet gateway

**Quick Checklist**
- [ ] Docker installed and running
- [ ] Working network access for NAT (host internet)
- [ ] `~/openairinterface5g/oai-cn5g-fed/docker-compose` available in the host
- [ ] Ports required by services not blocked by firewall

**Troubleshooting tips**
- If containers fail to start, inspect logs:

```bash
docker compose -f docker-compose-basic-nrf.yaml logs --tail 100
```

- If UE doesn't obtain `oaitun` IP, ensure the RAN compose file ran successfully and check `docker logs oai-nr-ue1`.
- If pings to the internet fail while the DN ping works, check `oai-upf` NAT and host firewall rules.

---

