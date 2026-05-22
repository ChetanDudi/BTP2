# Reproducibility

## What is reproducible from this repository

This repository is designed to make the software-side project workflow easier to reproduce:

1. install prerequisites
2. clone the external OAI repositories
3. start the CN5G stack
4. start the RF-simulation RAN stack
5. validate registration, session setup, and tunnel behavior
6. test the LMF endpoint workflow

## What is not bundled here

- the full OpenAirInterface source tree
- a verified USRP-based hardware deployment
- a finalized PlutoSDR-based deployment path

## Reproducibility principle

The strongest reproducible outcome documented here is the Dockerized RF-simulation workflow. That is the part future students should expect to reproduce first.
