# Incident Pattern: Transient Congestion

## Description
Short-duration spikes in latency and jitter, typically 1–5 collection cycles (10–50 seconds), that self-resolve without operator intervention.

## Signature
- Latency spikes to warning or degraded level for 1–3 cycles, then returns to baseline
- Jitter rises simultaneously with latency
- Packet loss may remain at 0% or briefly reach warning level (1–2%)
- Quality score drops transiently then recovers
- No persistent alert; event logged but auto-cleared

## Common Causes
- Burst traffic from a background job (backup, OS update, large file transfer) saturating the uplink momentarily
- ISP traffic engineering re-routing a path briefly during load balancing
- ICMP probe crossing a congested segment during a traffic burst
- Time-of-day congestion (e.g. peak hours on a shared access network)

## How to Distinguish from a Real Incident
| Signal                         | Transient Congestion | Real Incident       |
|--------------------------------|----------------------|---------------------|
| Duration                       | 1–5 cycles           | > 5 consecutive     |
| Affects all targets            | Sometimes            | Usually             |
| Loss accompanies latency spike | Rarely               | Frequently          |
| Self-resolves                  | Yes                  | No                  |
| Traceroute stable after event  | Yes                  | No — hop RTTs differ|

## Response
- Single transient event: no action required; event is logged automatically by NetMonitor.
- Repeated transients at the same time of day: investigate scheduled jobs or peak-hour policy.
- Transients increasing in frequency over days: trend may indicate link saturation; review capacity.
