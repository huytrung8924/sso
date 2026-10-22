# Detection logic and investigation

[← Project overview](../README.md)

This page summarizes the recorded configuration. Configuration and response screenshots are linked from each demo; no complete runtime configuration is reconstructed.

| Scenario | Trigger described or shown | Investigation pivot | Response |
|---|---|---|---|
| Kerberoasting | Event 4769, ticket encryption `0x17`, ticket-options pattern | Requesting IP, service, user, event time | Investigate alert |
| Malicious file | FIM change followed by malicious VirusTotal result | File path, hash, positives, endpoint | Remove file and inspect response events |
| SSH brute force | Repeated SSH authentication failures; response bound to rule `5763` | Source IP, target endpoint, attempted user, event time | `firewall-drop`, timeout 180 seconds |

## Network telemetry foundation

Suricata inspects traffic and writes EVE JSON, which the Wazuh Agent forwards to Wazuh Server. The resulting network alerts are searchable in Kibana. This integration remains part of the architecture across the selected scenarios. The SSH finding is based on authentication events, and the file-removal decision is based on FIM and VirusTotal, rather than assigning every detection to Suricata. See [Suricata integration](suricata-integration.md).

## Evidence chain

Distinguish **telemetry → detection → response → verification**. Kerberoasting covers telemetry and detection. The file and SSH demos add response evidence. A dashboard count alone does not prove any of these individual steps.

## Configuration details

- The Kerberoasting rule screenshot shows ID `110002`.
- SSH Active Response selects rule `5763`, command `firewall-drop`, location `local`, and timeout `180`.
- The SSH document does not contain the full rule definition or a verified attempt threshold.
- Correct source-IP extraction connects the SSH alert to the firewall action.
- The file workflow uses reputation enrichment after a file-change event. It does not demonstrate pre-execution blocking.
- Screenshot field names and exact rule syntax must be checked against raw decoded events before rebuilding the lab.

## Analyst review sequence

1. Select the relevant experiment time window in Kibana.
2. Open the alert in Discover.
3. Confirm the endpoint, event source, and detection description.
4. Inspect Kerberos ticket properties, the VirusTotal verdict, or SSH source-IP and user fields.
5. For response demos, find the related response event and inspect the corresponding endpoint evidence.

## Reproducibility

The supplied documents do not provide full configuration exports, raw event exports, rule-test output, or saved dashboard objects. A future runnable release should add these artifacts, pin compatible component versions, and test expected alerts against benign events as well as attack simulations.
