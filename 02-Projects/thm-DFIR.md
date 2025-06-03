📅 **Date**: 28 May 25  
📂 **Category**: TryHackMe 
📝 **Status**: Completed  
🔒 **Room**: Digital Forensics and Incident Response
🏷️ Tags: #tryhackme #tryhackme/learn 

---
# DFIR (Digital Forensics and Incident Response)

## What is DFIR?
**DFIR** stands for **Digital Forensics and Incident Response**. It involves:
- Collecting forensic artifacts from digital devices (computers, media devices, smartphones).
- Investigating incidents by identifying attacker footprints.
- Determining the extent of compromise.
- Restoring the environment to its pre-incident state.
---
## The Need for DFIR
DFIR assists security professionals in:
- Finding evidence of attacker activity in networks.
- Distinguishing false alarms from actual incidents.
- Removing attacker presence robustly.
- Identifying breach scope and time frame for stakeholder communication.
- Understanding the root causes and fixing loopholes to prevent future attacks.
- Gaining insights into attacker behavior for proactive defense.
- Sharing threat intelligence with the community.

---
## Who Performs DFIR?

DFIR requires expertise in:
- **Digital Forensics**: Identifying forensic artifacts and evidence on digital devices.
- **Incident Response**: Cyber security skills to interpret forensic data and pinpoint security incidents.
DFIR professionals combine **Digital Forensics** and **Incident Response** knowledge, as both fields are interdependent:
- IR defines investigation scope.
- Forensics provides evidence for IR.
---
## Core Concepts in DFIR
### Artifacts
- **Artifacts** = Pieces of evidence indicating activity on a system.
- Collected to support claims (e.g., a registry key used for persistence).
- Sources: Endpoint/Server file system, memory, or network.
### Evidence Preservation
- Preserve **evidence integrity**:
  - Collect and **write-protect**.
  - Analyze **copies** only.
  - Original evidence remains untouched for future reference.
### Chain of Custody
- **Secure handling** of evidence at all times.
- Prevent unauthorized access to avoid contamination.
- Maintain **clear documentation** of evidence transfer.
### Order of Volatility
- Capture volatile evidence first (RAM, network connections).
- Less volatile evidence later (disk, logs).
- Example order:
  1. RAM
  2. Network connections
  3. Disk
### Timeline Creation
- Organize events **chronologically**.
- Helps reconstruct attack flow and provides **context**.
- Essential for effective analysis.
---
## Incident Response Process
### NIST (SP-800-61) Process
1. Preparation
2. Detection and Analysis
3. Containment, Eradication, and Recovery
4. Post-incident Activity
### SANS (PICERL) Process
1. Preparation
2. Identification
3. Containment
4. Eradication
5. Recovery
6. Lessons Learned
### PICERL Explained
- **Preparation**: Build a response plan, team, tools, and training.
- **Identification**: Detect incidents, analyze indicators, document, and notify.
- **Containment**: Limit attack impact (short-term/long-term).
- **Eradication**: Remove attacker access and fix vulnerabilities.
- **Recovery**: Restore systems to pre-incident state.
- **Lessons Learned**: Review incident, improve defenses, and update plans.
---
## DFIR Tools Overview
### Eric Zimmerman's Tools
- Tools for Windows forensics: registry analysis, file system, timelines, etc.
- See **Windows Forensics 1 & 2** rooms on TryHackMe for details.
### KAPE
- **Kroll Artifact Parser and Extractor**: Automates artifact collection and parsing.
- Supports timeline creation.
### Autopsy
- Open-source forensics platform.
- Analyze digital media (mobile devices, hard drives, USBs).
- Supports plugins for extended functionality.
### Volatility
- Memory forensics tool for Windows & Linux.
- Extract valuable information from RAM dumps.
### Redline
- Incident response tool by FireEye.
- Collect and analyze forensic data from systems.
### Velociraptor
- Open-source endpoint monitoring, forensics, and response platform.
- Supports advanced investigations and live monitoring.