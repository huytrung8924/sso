# Evidence index

[← Project overview](../README.md)

Images are extracted directly from the supplied PDF report and supplementary `demossh.docx`. Original embedded bytes are preserved. Figures spanning two images are kept as separate dashboard and event-table files. PDF page numbers are 1-based; SSH image numbers follow their order in the Word document.

## Architecture, Kerberoasting, and malicious-file evidence

| Image | PDF page | Figure | Description |
|---|---|---|---|
| [system-architecture](../screenshots/architecture/system-architecture.jpeg) | 27 | 6 | System architecture |
| [kibana-dashboard](../screenshots/overview/kibana-dashboard.jpeg) | 39 | 29 | Kibana dashboard |
| [detection-rule](../screenshots/kerberoasting/detection-rule.jpeg) | 42 | 36 | Wazuh detection rule |
| [dashboard](../screenshots/kerberoasting/dashboard.jpeg) | 44 | 42 | Kerberoasting dashboard |
| [dashboard-events](../screenshots/kerberoasting/dashboard-events.jpeg) | 44 | 42 | Dashboard event table |
| [alert-detail](../screenshots/kerberoasting/alert-detail.jpeg) | 45 | 43 | Kerberos event and source IP |
| [fim-monitoring](../screenshots/malware/fim-monitoring.jpeg) | 45 | 44 | FIM monitored directory |
| [fim-rules](../screenshots/malware/fim-rules.jpeg) | 46 | 46 | File change detection rules |
| [active-response-config](../screenshots/malware/active-response-config.jpeg) | 47 | 48 | Remove-threat active response |
| [response-rules](../screenshots/malware/response-rules.jpeg) | 47 | 49 | Active response result rules |
| [dashboard](../screenshots/malware/dashboard.jpeg) | 48 | 52 | Malware dashboard |
| [dashboard-events](../screenshots/malware/dashboard-events.jpeg) | 48 | 52 | Dashboard event table |
| [detection-removal](../screenshots/malware/detection-removal.jpeg) | 48 | 53 | Detection and removal event timeline |
| [alert-detail](../screenshots/malware/alert-detail.jpeg) | 49 | 54 | VirusTotal result and file removal |
| [virustotal-result](../screenshots/malware/virustotal-result.jpeg) | 49 | 55 | VirusTotal file analysis |

## Suricata integration evidence

| Image | PDF page | Figure | Description |
|---|---|---|---|
| [rule-path](../screenshots/suricata/rule-path.jpeg) | 41 | 32 | rule path |
| [capture-interface](../screenshots/suricata/capture-interface.jpeg) | 41 | 33 | capture interface |
| [agent-log-collection](../screenshots/suricata/agent-log-collection.jpeg) | 41 | 34 | agent log collection |
| [elk-network-events](../screenshots/suricata/elk-network-events.jpeg) | 41 | 35 | elk network events |

## SSH brute-force evidence — demossh.docx

| Image | Document image order | Description |
|---|---|---|
| [active-response-config](../screenshots/ssh-brute-force/active-response-config.png) | 1 | Active response config |
| [ssh-port-scan](../screenshots/ssh-brute-force/ssh-port-scan.png) | 2 | Ssh port scan |
| [hydra-simulation](../screenshots/ssh-brute-force/hydra-simulation.png) | 3 | Hydra simulation |
| [dashboard](../screenshots/ssh-brute-force/dashboard.png) | 4 | Dashboard |
| [alert-detail](../screenshots/ssh-brute-force/alert-detail.png) | 5 | Alert detail |
| [connectivity-check](../screenshots/ssh-brute-force/connectivity-check.png) | 6 | Connectivity check |
| [iptables-drop](../screenshots/ssh-brute-force/iptables-drop.png) | 7 | Iptables drop |

Only images relevant to the architecture and selected demos are included. Installation credentials and integration-secret screenshots are not included. Full source documents are not bundled.
