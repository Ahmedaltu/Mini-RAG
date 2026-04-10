# Runbook: Packet Loss

## Thresholds
- Warning: loss > 1%
- Degraded: loss > 3%

## Likely Causes
- Congested uplink dropping packets at queue tail
- Faulty cable or transceiver introducing CRC errors
- Firewall or ACL rate-limiting ICMP probes specifically
- Wireless medium contention or poor SNR
- Target host dropping ICMP under load (not true loss — verify with HTTP probe)

## Checks
1. Check if loss is consistent across all targets. Loss only to one target likely means that target is filtering ICMP.
2. Run an HTTP probe to the same target — if HTTP succeeds with 0 loss, the target is dropping ICMP only (not a real path problem).
3. Run traceroute: loss appearing at a specific hop that then recovers at the next hop is ICMP rate-limiting on that device, not true loss.
4. Check packet loss trend in InfluxDB: burst loss (single cycle) vs. sustained loss (multiple consecutive cycles) require different responses.
5. Check interface error counters on the monitoring host for CRC or input errors.

## Actions
- Burst loss (1–2 cycles, < 3%): log the event, monitor for recurrence. No immediate escalation needed.
- Sustained loss > 3% for > 3 cycles: open incident, check upstream link, contact ISP if path-level issue confirmed.
- Loss to all targets simultaneously: suspect local interface, local router, or uplink issue.
- Loss to one target only with HTTP succeeding: mark as ICMP-filter, suppress ICMP-only alerts for that target.
- Loss > 10%: treat as outage-level; check availability metric and escalate immediately.
