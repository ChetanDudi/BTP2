# System Architecture

## Main components

- `UE`
  Simulated user equipment

- `gNB`
  Simulated next-generation base station

- `AMF`
  Handles mobility management and registration logic

- `SMF`
  Manages session establishment

- `UPF`
  Handles user-plane forwarding

- `NRF`
  Service discovery/registration for network functions

- `LMF`
  Supports positioning-related workflows

- `EXT-DN`
  External data network / gateway path

## High-level flow

1. Core network functions are started through Docker Compose.
2. Simulated RAN components are started in `rfsim` mode.
3. UE registers with AMF through the gNB.
4. PDU session establishment creates the user-plane path.
5. Tunnel interfaces are verified from the UE and UPF side.
6. LMF-related requests can be tested separately through its endpoint.

## Why RF simulation mattered here

RF simulation allowed the project to validate signaling and service interaction without requiring full USRP-based radio hardware. This made the software stack reproducible inside the available lab environment.
