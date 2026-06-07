## Security Monitoring Configuration

### Wazuh Deployment & Detection

This section details the deployment of the SIEM stack and validates detection coverage against two real-world attack techniques: Brute Force and Kerberoasting.

### Wazuh Manager Deployment (Linux)

A dedicated AlmaLinux VM serves as the centralized SIEM for log aggregation, correlation, and response orchestration.

**Primary Responsibilities:**
- Real-time event ingestion from Windows endpoints
- Agent health monitoring and management
- Rule-based alert correlation for TTP detection
- Log retention for forensic analysis

![Figure 1: Wazuh Manager Service Status](./screenshots/WAZUH_MANAGER.png)
*Figure 1: `wazuh-manager.service` confirmed active on the Linux host. The SIEM is operational and ready to ingest telemetry.*

### Endpoint Monitoring Configuration (Windows)

The Wazuh Agent was deployed on the Windows Server 2012 R2 Domain Controller to provide host-level visibility.

**Monitored Data Sources:**
- **Windows Security Events**: Event ID 4625 for failed logons, 4769 for Kerberos TGS requests
- **Authentication Activity**: Success and failure events for brute force detection
- **System Logs**: Service status and user activity for anomaly detection

![Figure 2: Agent Registration & Deployment](./screenshots/WAZUH_ON_WIN.png)
*Figure 2: Agent ID 001 - `CYBERSECVICTIM` successfully registered. `WazuhSvc` service running on the monitored Windows endpoint.*

### Detection Validation

Live attack simulations were executed to prove the monitoring stack can detect common Active Directory attacks.

#### Kerberoasting Activity (T1558.003)

**Observed Indicators:**
- Event ID 4769 generated during TGS requests with `Ticket Encryption Type: 0x17` (RC4)
- Weak encryption usage identified and forwarded to Wazuh for alerting

![Figure 3: Kerberoasting Detection Evidence](./screenshots/KERBEROASTING_HASHCAT.png)
*Figure 3: TGS ticket successfully extracted and cracked offline. Confirms the Kerberoasting attack path is viable and detectable via Event ID 4769.*

#### Brute Force Activity (T1110)

**Observed Indicators:**
- High volume of Event ID 4625 authentication failures from a single source
- 154 failed login attempts correlated by Wazuh within 30 minutes
- Rule 60122 "Logon failure - Unknown user or bad password" triggered

![Figure 4: Brute Force Dashboard](./screenshots/WAZUH_EVENTS.png)
*Figure 4: Wazuh dashboard for `CYBERSECLABDC` showing 297 total events, 154 authentication failures. The alert spike validates brute force detection.*

### Key Findings

The monitoring stack successfully demonstrated:

- **Centralized Event Collection**: Windows Security logs from the DC are reliably forwarded to the Wazuh Manager.
- **Endpoint Visibility**: Full coverage of authentication activity, service status, and Kerberos events on the monitored host.
- **TTP-Based Detection**: Confirmed detection for Brute Force via Event ID 4625 and Kerberoasting via Event ID 4769.
- **Actionable Alerting**: Correlated authentication failures trigger high-fidelity alerts, providing the foundation for automated response.

