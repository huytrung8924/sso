# Results and evidence boundaries

[← Project overview](../README.md)

| Result | Evidence | What it supports |
|---|---|---|
| Suricata network integration | Report Figures 32–35 | Sensor configuration, EVE JSON collection, and network alerts in Kibana |
| Centralized visualization | Report Figure 29 and scenario dashboards | Wazuh-derived events were available in Kibana |
| Kerberoasting alert | Report Figures 36, 42, 43 | Suspicious TGS detection and event investigation |
| File reputation enrichment | Report Figures 53–55 | A flagged file was associated with VirusTotal results |
| File removal | Report Figures 53–54 | Removal-response message and deletion event |
| SSH brute-force alert | SSH document images 4–5 | Authentication-failure monitoring and brute-force detection |
| IP containment | SSH document images 1, 4–7 | Configured response, response event, and DROP entry |

## Concrete observations

- Kerberos event: `4769`, encryption type `0x17`, source `192.168.112.145`.
- Flagged-file event: `/root/nc.exe`, 51 positive engines out of 71 in the recorded integration event.
- SSH target: `192.168.112.132:22`, attempted account `wazuh-agent`.
- SSH response: rule `5763`, `firewall-drop`, configured timeout 180 seconds.
- Firewall evidence: DROP entry for `192.168.112.145`.

## Not measured

Detection accuracy, false-positive rate, end-to-end latency, throughput, timed firewall rollback, and production-scale reliability are not quantified. Dashboard totals include background events and are not unique attack counts. The SSH dashboard and Discover images show different dates and are not treated as one synchronized timing experiment.

## Portfolio skills demonstrated

Security telemetry integration; endpoint and network visibility; Wazuh rule interpretation; SSH authentication monitoring; Active Response configuration; VirusTotal enrichment; Logstash forwarding; Kibana dashboards; event triage; and validation of containment.
