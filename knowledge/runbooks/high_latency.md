# Runbook: High Latency

## Thresholds
- Warning: avg latency > 50 ms
- Degraded: avg latency > 100 ms

## Likely Causes
- Network congestion between agent and target
- ISP routing change or BGP re-convergence
- DNS resolution delay inflating round-trip time
- Target host under load (CPU-bound response)
- QoS policy deprioritising ICMP on intermediate hops

## Checks
1. Run traceroute to identify which hop introduces the delay.
2. Compare latency across all monitored targets — isolated vs. widespread spike indicates local vs. upstream issue.
3. Check packet loss alongside latency; loss > 1% with high latency points to congestion, not just distance.
4. Review jitter: sustained high jitter + high latency = active congestion; stable high latency = routing distance.
5. Query InfluxDB for the last 30 minutes to determine if the spike is momentary or trending up.

## Actions
- If a single hop: note the hop IP, check if it is a known ISP transit node.
- If widespread across targets: escalate to ISP or check upstream link utilisation.
- If isolated to one target: check the target's health independently (HTTP probe, DNS probe).
- If persistent > 5 cycles: trigger alert and begin incident log.
