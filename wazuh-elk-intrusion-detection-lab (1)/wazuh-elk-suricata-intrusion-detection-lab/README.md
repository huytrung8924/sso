# 🛡️ Wazuh + ELK Stack + Suricata — Intrusion Detection Lab

![Wazuh](https://img.shields.io/badge/Wazuh-4.14-0078D4)
![Stack](https://img.shields.io/badge/Stack-ELK-005571)
![Network IDS](https://img.shields.io/badge/Network_IDS-Suricata-EF7B00)
![Demos](https://img.shields.io/badge/Documented_Demos-3-16803C)

**Centralized threat detection, investigation, and automated response with Wazuh, Suricata, and the Elastic Stack.**

This project implements a host-and-network intrusion detection system built around **three complementary components**:

- **Suricata — network intrusion detection:** inspects traffic on the monitored Ubuntu endpoint and writes network alerts to `eve.json`.
- **Wazuh — endpoint monitoring and centralized analysis:** collects Windows events, SSH authentication logs, file-integrity events, and Suricata logs; applies decoders and rules; coordinates Active Response.
- **ELK Stack — ingestion, search, and visualization:** Logstash forwards Wazuh alerts, Elasticsearch indexes them, and Kibana provides dashboards and event investigation.

**Academic topic:** Xây dựng hệ thống phát hiện xâm nhập dựa trên Wazuh và ELK Stack. The implemented architecture integrates **Suricata as its network IDS**, alongside Wazuh endpoint monitoring and the ELK analytics pipeline.

The project documents three scenarios: **Kerberoasting detection**, **malicious-file detection and removal**, and **SSH brute-force detection and IP blocking**.

> **Repository scope:** a documented lab portfolio with original experiment screenshots, architecture, detection logic, and observed results. It does not include a complete deployment bundle or exported Kibana saved objects.

## 📊 Results at a glance

| Scenario | Detection evidence | Observed response | Walkthrough |
|---|---|---|---|
| Kerberoasting | Suspicious TGS request, Windows Event 4769, service and source-IP context | Alert and investigation in Kibana | [Demo 01](demos/01-kerberoasting/README.md) |
| Malicious file | FIM event enriched with VirusTotal results for `/root/nc.exe` | `remove-threat.sh` removal event and file-deleted event | [Demo 02](demos/02-malicious-file/README.md) |
| SSH brute force | Repeated SSH authentication failures, Wazuh brute-force alert | `firewall-drop` response and an `iptables` DROP entry | [Demo 03](demos/03-ssh-brute-force/README.md) |

### What the lab demonstrates

- **Integrated network IDS:** Suricata events are collected by Wazuh and visible in Kibana, as demonstrated by the integration screenshots.
- **Centralized visibility:** endpoint and network events become searchable in Kibana.
- **Detection engineering:** Wazuh rules identify suspicious Kerberos activity and repeated SSH authentication failures; Suricata adds network visibility.
- **Threat enrichment:** VirusTotal results add file-reputation context to FIM events.
- **Automated containment:** file removal and temporary source-IP blocking are visible in response logs.
- **Investigation workflow:** dashboards summarize activity; Discover exposes the underlying event fields.

![Kibana security monitoring dashboard](screenshots/overview/kibana-dashboard.jpeg)

*Dashboard captured during the lab. Counts reflect the selected time range, not a measured detection rate.*

## 🏗️ Architecture

![Suricata to Wazuh to Logstash to Elasticsearch to Kibana](screenshots/architecture/system-architecture.jpeg)

| Layer | Component | Responsibility |
|---|---|---|
| Host telemetry | Wazuh Agent | Windows Security events, FIM, local log collection |
| Network telemetry | Suricata | Traffic inspection and JSON alerts in `eve.json` |
| Detection | Wazuh Server | Decoding, rules, alert generation, response orchestration |
| Enrichment | VirusTotal integration | File-hash reputation lookup |
| Ingestion | Logstash | Read Wazuh alerts and forward them to Elasticsearch |
| Search | Elasticsearch | Index security events for investigation |
| Visualization | Kibana | Dashboards, filtering, and event inspection |
| Containment | Wazuh Active Response | Remove a flagged file or block a source IP |

**Network detection pipeline:** Network traffic → Suricata → `eve.json` → Wazuh Agent → Wazuh Server → `alerts.json` → Logstash → Elasticsearch → Kibana.

**Endpoint detection pipeline:** Windows Security / SSH authentication / FIM events → Wazuh Agent → Wazuh Server → the same ELK pipeline.

**Response path:** Wazuh Server → Active Response on the endpoint → file removal or firewall IP blocking.

[Architecture and environment](docs/architecture.md) · [Detection logic](docs/detection-logic.md) · [Evidence index](docs/evidence-index.md)

## 🌐 Suricata integration — network visibility

Suricata runs on the Ubuntu endpoint with capture interface **`ens33`** and monitored network **`192.168.112.0/24`**. The Wazuh Agent reads **`/var/log/suricata/eve.json`** as JSON and sends the events to Wazuh Server for analysis. The resulting alerts reach Kibana through Logstash and Elasticsearch.

![Wazuh Agent configuration for collecting Suricata EVE JSON](screenshots/suricata/agent-log-collection.jpeg)

![Suricata events visible in Kibana](screenshots/suricata/elk-network-events.jpeg)

**Observed integration result:** Kibana displays events with `rule.groups` containing `ids` and `suricata`, together with network-alert fields. This establishes network telemetry as part of the implemented system.

During the malicious-file demo, the event list also contains Suricata alerts associated with the executable download, alongside VirusTotal and file-removal events. Suricata contributes network context; Wazuh FIM, VirusTotal, and Active Response drive the file-verdict and removal workflow.

[View Suricata integration and its role in the three demos →](docs/suricata-integration.md)

## 🔎 Demo 01 — Kerberoasting detection

**Objective:** detect suspicious Kerberos service-ticket requests and identify the requesting host and target service through Windows Security events collected by Wazuh.

### Dashboard

![Kerberoasting monitoring dashboard in Kibana](screenshots/kerberoasting/dashboard.jpeg)

*Kibana dashboard captured during the Kerberoasting simulation.*

### ELK logs and investigation

![Kerberoasting alert and Windows Security event details in Kibana Discover](screenshots/kerberoasting/alert-detail.jpeg)

*Discover shows the abnormal-TGS alert and the underlying Windows event, including the source IP, service name, and ticket properties.*

### Key results

| Evidence | Observed result |
|---|---|
| Detection | Alert for an abnormal TGS request that may indicate Kerberoasting |
| Windows event | Security Event **4769**, ticket encryption type **`0x17`** |
| Investigation context | Source **`192.168.112.145`**, target service **`mssql_svc`** |
| Outcome | Suspicious activity detected and available for investigation in Kibana |

[Full walkthrough and supporting evidence →](demos/01-kerberoasting/README.md)

## 🧬 Demo 02 — Malicious-file detection and removal

**Objective:** detect a file change with Wazuh FIM, enrich it with VirusTotal, and automatically remove the flagged file through Active Response.

### Dashboard

![Malicious-file monitoring dashboard in Kibana](screenshots/malware/dashboard.jpeg)

*Kibana dashboard captured during the malicious-file detection and removal demo.*

### ELK logs and investigation

![VirusTotal enrichment and file-removal events in Kibana Discover](screenshots/malware/alert-detail.jpeg)

*Discover shows the VirusTotal verdict, the file-removal response, and the subsequent file-deleted event. Suricata download-related alerts provide additional network context.*

### Key results

| Evidence | Observed result |
|---|---|
| Detection | FIM and VirusTotal identify the demonstration file **`/root/nc.exe`** |
| Reputation context | **51 positive engines out of 71** in the recorded VirusTotal integration event |
| Response evidence | `remove-threat.sh` reports removal of `/root/nc.exe`; the event list also contains `File deleted.` |
| Outcome | Flagged-file detection and automatic removal documented in Kibana |

[Full walkthrough and supporting evidence →](demos/02-malicious-file/README.md)

## 🔐 Demo 03 — SSH brute-force detection and IP blocking

**Objective:** detect repeated SSH authentication failures with Wazuh and block the attacking source through `firewall-drop` Active Response.

### Dashboard

![SSH brute-force monitoring dashboard in Kibana](screenshots/ssh-brute-force/dashboard.png)

*Kibana summarizes authentication failures, alert activity, targeted agents, and the host-blocked response.*

### ELK logs and investigation

![SSH authentication failures, brute-force alert, and firewall response in Kibana Discover](screenshots/ssh-brute-force/alert-detail.png)

*Discover shows repeated authentication failures, the SSH brute-force alert, and `Host Blocked by firewall-drop Active Response`.*

### Key results

| Evidence | Observed result |
|---|---|
| Detection | Repeated SSH authentication failures and a brute-force alert |
| Investigation context | Source **`192.168.112.145`**, target **`192.168.112.132:22`**, attempted user **`wazuh-agent`** |
| Response evidence | `firewall-drop` response event and a matching firewall DROP entry; rule **5763**, configured timeout **180 seconds** |
| Outcome | SSH brute-force activity detected and source-IP blocking verified on the endpoint |

**Additional verification — endpoint firewall**

![iptables DROP entry for source 192.168.112.145](screenshots/ssh-brute-force/iptables-drop.png)

*The INPUT-chain DROP entry provides direct endpoint evidence of blocking, complementing the ELK response log.*

[Full walkthrough and supporting evidence →](demos/03-ssh-brute-force/README.md)

*Evidence context: dashboard totals describe the selected time windows, not detection accuracy. The SSH dashboard and Discover captures come from different dates; they are not used to calculate response latency. The 180-second timeout is a configuration value, not a measured unblock result.*

## 📂 Repository guide

| Path | Contents |
|---|---|
| [`demos/01-kerberoasting/`](demos/01-kerberoasting/README.md) | Kerberos detection walkthrough |
| [`demos/02-malicious-file/`](demos/02-malicious-file/README.md) | FIM, VirusTotal, and removal workflow |
| [`demos/03-ssh-brute-force/`](demos/03-ssh-brute-force/README.md) | SSH detection and firewall response |
| [`docs/architecture.md`](docs/architecture.md) | Components, log flow, and environment |
| [`docs/suricata-integration.md`](docs/suricata-integration.md) | Network IDS configuration, collection, and ELK evidence |
| [`docs/detection-logic.md`](docs/detection-logic.md) | Detection conditions and interpretation |
| [`docs/results.md`](docs/results.md) | Evidence-backed outcome matrix |
| [`docs/evidence-index.md`](docs/evidence-index.md) | Screenshot-to-report mapping |
| [`docs/github-publishing.md`](docs/github-publishing.md) | Upload instructions and repository metadata |
| [`screenshots/`](screenshots/) | Original embedded report images |

## 🧠 Technical takeaways

- Combining host and network telemetry supports investigation across different sources.
- Detection and response need separate evidence: an alert alone does not prove containment.
- Suricata supplies network alerts while Wazuh combines them with endpoint telemetry for centralized analysis.
- FIM identifies file changes; reputation enrichment helps decide which changes need a response.
- Dashboard totals depend on time filters and can include unrelated background events.

## 🔭 Next steps

- Export and version the original rules, agent settings, and Kibana saved objects.
- Measure ingestion latency, false-positive rates, and response completion time.
- Test response rollback, firewall timeout expiry, and legitimate-traffic behavior.
- Tune detections against a documented benign baseline.

## Evidence basis

Architecture, Kerberoasting, and malicious-file evidence come from the project report, principally pages **27–49**. SSH brute-force evidence comes from the supplementary **`demossh.docx`** document. See the [evidence index](docs/evidence-index.md) for exact figure references. The screenshots preserve the captured lab state; no new execution or performance benchmark is implied.
