# LMF Experimentation

## Why LMF mattered in this project

The project extends beyond basic core/RAN bring-up to look at Location Management Function (LMF) behavior in a modern 5G environment.

## Focus areas described in the report

- understanding the role of LMF in positioning workflows
- observing AMF-LMF related service interactions
- deploying and testing positioning-related components in Dockerized OAI environments
- using simulation-based validation rather than claiming full real-time hardware verification

## Example request from the setup notes

The notes include a sample HTTP request:

```bash
curl -X POST http://localhost:8000/nlmf-loc/v1/determine-location \
  -H "Content-Type: application/json" \
  -d '{"supi":"imsi-208950000000036","cellId":"0000e014e"}'
```

## Practical interpretation

This should be understood as an experimentation path within the software testbed, not as evidence of a completed carrier-grade positioning deployment. The report is careful about this distinction, and the repository keeps that same honesty.
