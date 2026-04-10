# Runbook: High Jitter

## Thresholds
- Warning: jitter > 20 ms
- Degraded: jitter > 30 ms

## Likely Causes
- Congested intermediate link causing variable queuing delays
- Wi-Fi interference or signal instability on the monitoring host
- Competing traffic on the local interface (backup job, streaming, large transfer)
- Policer or rate-limiter on the path applying bursty shaping
- ICMP deprioritisation on a router causing irregular probe timing

## Checks
1. Check if jitter is consistent across all targets or isolated to one — isolated jitter often points to the target or a single path segment.
2. Cross-reference with latency trend: rising jitter with stable latency suggests local queue pressure; both rising together suggests path congestion.
3. Run traceroute to see if a specific hop shows variable RTT across the three probes (e.g. `* 14ms 30ms`).
4. Check the monitoring host's own CPU and interface queue stats — a busy host can manufacture artificial jitter.
5. Look for packet loss co-occurring with jitter spikes: loss + jitter > 20 ms is a strong congestion signal.

## Actions
- If local interface is suspect: check `ip -s link` or interface counters for drops and errors.
- If a mid-path hop is suspect: note the hop IP and test from a second vantage point if available.
- If jitter stays elevated > 10 consecutive cycles: open incident, review upstream link utilisation.
- For VoIP or real-time workloads: jitter > 20 ms degrades quality; escalate faster than for bulk traffic.
