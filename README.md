# Enterprise-Detection-Monitoring-Lab
Enterprise detection and monitoring lab designed to simulate a segmented enterprise network with centralized logging, endpoint monitoring and threat detection capabilities.

## 1. Project Goals
- Test defense-in-depth with pfSense firewall segmentation
- Simulate brute-force attacks and monitor detection
- Correlate network and host logs in Wazuh SIEM

## 2. Lab Architecture
The lab simulates an enterprise environment with segmented networks.  
See [docs/network-architecture.md](docs/network-architecture.md) for details and topology diagram.

## 3. Technologies Used
- **Firewall**: pfSense CE 2.7.2
- **SIEM**: Wazuh
- **Target**: Windows Server 2012 R2 DC
- **Attacker**: Kali Linux 2024
- **Hypervisor**: VirtualBox 7.0

## 4. Network Topology
A segmented architecture with:
- OPT1 (10.0.20.0/24): Attacker network
- LAN (10.0.10.0/24): Domain Controller + Win10 client
See diagram: [screenshots/network-topology.png]

## 5. Security Monitoring Configuration
- Wazuh agent installed on DC and Win10
- Log forwarding to Wazuh manager
- Custom rules for 4625 brute-force detection
See: [docs/siem-configuration.md](docs/siem-configuration.md)

## 6. Attack Simulation
- Brute-force attempt from Kali to DC (blocked by pfSense)
- Commands used: [docs/attack-simulation.md](docs/attack-simulation.md)

## 7. Detection Results
- pfSense blocked traffic: [screenshots/pfsense-deny-log.png](screenshots/pfsense-deny-log.png)
- No 4625 events reached Wazuh → Attack prevented
