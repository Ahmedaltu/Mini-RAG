# Network Baselines and Thresholds

## Latency (ICMP round-trip, ms)
| Level    | Threshold       | Meaning                               |
|----------|-----------------|---------------------------------------|
| Healthy  | ≤ 50 ms         | Normal operation                      |
| Warning  | 51 – 100 ms     | Elevated; monitor for trend           |
| Degraded | > 100 ms        | Investigate; user-impacting likely    |
| Critical | > 300 ms        | Severe; real-time apps will fail      |

Typical baselines:
- LAN target (same subnet): < 1 ms
- Regional internet target (e.g. 8.8.8.8): 5 – 30 ms
- Intercontinental target: 80 – 200 ms

## Jitter (ms)
| Level    | Threshold  |
|----------|------------|
| Healthy  | ≤ 20 ms    |
| Warning  | 21 – 30 ms |
| Degraded | > 30 ms    |

Note: jitter > 20 ms is noticeable for VoIP; > 50 ms causes audible degradation.

## Packet Loss (%)
| Level    | Threshold  |
|----------|------------|
| Healthy  | 0%         |
| Warning  | > 1%       |
| Degraded | > 3%       |
| Critical | > 10%      |

Note: 0% loss is the expected baseline for a healthy monitored path.

## Quality Score (composite, 0–100)
| Level    | Threshold  |
|----------|------------|
| Healthy  | ≥ 90       |
| Warning  | 75 – 89    |
| Degraded | < 75       |

The quality score is derived from a weighted combination of latency, jitter, packet loss, and availability. A score drop without a single metric spiking often indicates a multi-factor degradation.

## Availability (ratio, 0–1)
| Level    | Threshold  |
|----------|------------|
| Healthy  | ≥ 0.99     |
| Warning  | 0.95 – 0.989 |
| Degraded | < 0.95     |

Availability is computed as successful probes / total probes over the measurement window.

## Probe Intervals
- Default collection interval: 10 seconds
- Ping count per cycle: 4 probes
- Typical metrics window for AI analysis: 30 minutes
