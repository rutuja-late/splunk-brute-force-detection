<h1 align="center">Brute Force Attack Detection & Analysis using Splunk</h1>
<p align="center">
SOC Investigation Case Study • Log Analysis • Threat Detection • MITRE ATT&CK
</p>

---

## 📌 Overview
This project presents a Security Operations Center (SOC) investigation of a brute-force SSH attack using Splunk. It demonstrates how authentication logs can be analyzed to detect suspicious activity, identify attacker IPs, and map findings to the MITRE ATT&CK framework.

---

## 🎯 Objectives
- Detect repeated failed login attempts
- Identify attacker source IP using SPL rex command
- Analyze login patterns over time using timechart
- Apply threshold-based detection logic
- Map attack to MITRE ATT&CK framework
- Build SOC Dashboard for real-time monitoring

---

## 🛠️ Tech Stack
| Category | Tools |
|--------|------|
| SIEM | Splunk Enterprise (Developer License) |
| Attack Simulation | Hydra (on Kali Linux) |
| Target System | Kali Linux (localhost SSH) |
| Logs | /var/log/ssh-bruteforce.log |
| Framework | MITRE ATT&CK |

---

## ⚔️ Attack Scenario
A brute-force SSH attack was simulated in a controlled lab environment using Hydra on Kali Linux targeting localhost (127.0.0.1). The attacker generated multiple failed login attempts followed by a successful login, replicating a real-world credential access attack.

**Hydra Command Used:**

    hydra -l kali -P /home/kali/test_passwords.txt ssh://127.0.0.1

**Result:** Password found — kali:kali in under 5 seconds with 5 failed attempts before success.

![Hydra Attack Output](screenshots/hydra-attack-output.png)

---

## 🔍 SPL Queries Used

### Failed Login Detection

    index=main source="/var/log/ssh-bruteforce.log" "Failed password"
    | stats count by host

### Threshold-Based Detection (V2.0)

    index=main source="/var/log/ssh-bruteforce.log" "Failed password"
    | stats count by host
    | where count > 3

### Attacker IP Extraction (V2.0)

    index=main source="/var/log/ssh-bruteforce.log"
    | rex "from (?<attacker_ip>\d+\.\d+\.\d+\.\d+)"
    | stats count by attacker_ip

### Failed Login Timeline

    index=main source="/var/log/ssh-bruteforce.log"
    | timechart count

---

## 📊 Key Findings
| Finding | Detail |
|---------|--------|
| Attacker IP | 127.0.0.1 |
| Failed Attempts | 5 |
| Successful Login | 1 |
| Attack Duration | ~5 seconds |
| Detection Method | Threshold > 3 failed logins |

![Splunk Stats by Host](screenshots/splunk-stats-by-host.png)

![Attacker IP Breakdown](screenshots/splunk-attacker-ip.png)

---

## 📈 SOC Dashboard
Built using Splunk Dashboard Studio with two panels:
- **Failed Login Timeline** — timechart visualization of attack pattern
- **Attacker IP Breakdown** — table showing source IP and attack count

![SOC Dashboard](screenshots/soc-dashboard.png)

---

## 🎯 MITRE ATT&CK Mapping
| Field | Detail |
|-------|--------|
| Tactic | Credential Access |
| Technique | T1110 — Brute Force |
| Sub-technique | T1110.001 — Password Guessing |
| Detection | AN1275 — High volume failed logins followed by success |

**Mitigations:**
- Account lockout policy
- Multi-Factor Authentication (MFA)
- Strong password policy

---

## 🔐 Security Insight
Multiple failed login attempts from a single IP followed by a successful login is a strong indicator of a brute-force attack. Threshold-based detection (where count > 3) enables automated alerting in SOC environments.

---

## 🧠 Conclusion
This project demonstrates end-to-end SOC analyst skills — attack simulation using Hydra, log ingestion in Splunk, SPL query writing, threshold-based detection, dashboard creation, and MITRE ATT&CK mapping. These are core skills required in real-world SOC environments.

---

## 📁 Project Files
- screenshots/ → All visualizations and evidence
- README.md → Full investigation documentation
