# Cloudora Security Operations - Incident Report: Payroll Phishing Campaign (CLD-IR-0002)

## 📌 Project Overview
This project simulates a Security Operations Center (SOC) incident response investigation conducted as part of the MyFirstHack training scenario. It details the detection, containment, eradication, and post-incident analysis of a targeted payroll-themed phishing campaign affecting Cloudora B2B HR Software.

* **Incident ID:** `CLD-IR-0002`
* **Severity:** P1 (2 staff accounts compromised via active attacker sign-in)
* **Analyst:** Dhimons Thapa
* **Status:** Contained, Eradicated, and Verified

---

## 🔍 Executive Summary & Key Findings
On August 25, 2026, a payroll-themed phishing campaign targeted Cloudora staff, impersonating Cloudora HR and urging employees to update credentials on a fake lookalike website. 

### 1. Phishing Variants & Authentication Analysis
Two distinct phishing variants were utilized in this attack campaign alongside legitimate communications:

* **Variant A (Failed Authentication & Relays):** Spoofed the real `cloudora.io` domain but failed all authentication checks (`spf=fail`, `dkim=fail`, `dmarc=fail`). It utilized multiple relay servers within the same network block.
<img width="1920" height="1080" alt="IP where it camefrom" src="https://github.com/user-attachments/assets/6d1548ae-8826-4bd8-a226-b11fbc797fcc" />

<img width="1920" height="1080" alt="Authenticaiton results the SPF, DKIM" src="https://github.com/user-attachments/assets/b08eacb9-dde6-4910-bd5d-c069175eae71" />

<img width="1920" height="1080" alt="3 differet ip but in same neighbouthood" src="https://github.com/user-attachments/assets/45a8d5f2-1264-42e6-b286-3ece0fc7ae6c" />


* **Variant B (Passed Authentication / Lookalike Domain):** Sent from an attacker-controlled lookalike domain (`cloudora-hr-portal.example`) and successfully passed authentication checks (`spf=pass`, `dkim=pass`, `dmarc=pass`), proving that successful authentication alone does not guarantee message safety.
<img width="1920" height="1080" alt="2 diff server" src="https://github.com/user-attachments/assets/1f9ffabb-4327-4dfd-a562-c41f99d82b1c" />

 <img width="1920" height="1080" alt="One same domain" src="https://github.com/user-attachments/assets/7d934280-8e7d-4d48-8cb1-39b18aa8dace" />


* **Benign False Positive (Mailchimp Newsletter):** An employee-forwarded "Cloudora Monthly" newsletter sent via Mailchimp (`mail105.suw16.mcsv.net`, IP `198.18.60.5`) was fully verified as a legitimate, authenticated marketing message.
<img width="1920" height="1080" alt="newsletter authentication pass" src="https://github.com/user-attachments/assets/a893a7e7-4d78-4680-aa41-9005b7d07a55" />

  <img width="1920" height="1080" alt="comparison on real email and fake one" src="https://github.com/user-attachments/assets/1ace4bcb-8c45-4f92-9d73-c8553cdeaf18" />

 <img width="1920" height="1080" alt="cloudora known IP on letter one" src="https://github.com/user-attachments/assets/7913e8ba-57f2-4ea4-8dbc-76da8a35b15f" />

 <img width="1920" height="1080" alt="sends back to same domain letter onr" src="https://github.com/user-attachments/assets/258b7452-e075-402b-9526-72d92c694104" />


---

### 2. Campaign Delivery Scope & Impact
* **Delivery Metrics:** Analysis of mail flow tracking shows how many accounts were reached and whether messages were delivered or quarantined across the organization.
 <img width="1920" height="1080" alt="No of acc it was delieverd to" src="https://github.com/user-attachments/assets/751f8218-0a3f-4aab-9c2a-123417a247ed" />


* **Credential Harvesting & Compromise:** Across the targeted distribution, users interacted with links. 2 accounts (`freya.lynn` and `ryan.boyd`) submitted credentials and were subsequently logged into by the attacker from the Netherlands (`198.18.7.200`).
<img width="1920" height="1080" alt="account who got compromised" src="https://github.com/user-attachments/assets/336b407b-5192-447d-bcc5-709eba7c3411" />
<img width="1920" height="1080" alt="2 victim" src="https://github.com/user-attachments/assets/b3451e34-7a5f-4354-8d24-cea85af75ad9" />


---

## 🛠️ Incident Timeline (UTC)
* **06:58:** Legitimate Cloudora newsletter delivered successfully with verified DKIM signatures.
* **07:54 – 08:47:** Waves of Variant A payroll phishing emails sent from IPs `198.18.44.10` and `198.18.44.23` (authentication failed).
* **08:35 – 09:08:** Variant B phishing emails sent from lookalike domain via IP `198.18.51.7` (authentication passed).
* **08:39 – 09:05:** Initial victim click events recorded; `freya.lynn` and `ryan.boyd` submit credentials.
* **10:34 – 13:25:** Attacker authenticates to compromised accounts using IP `198.18.7.200` from the Netherlands.
* **14:00 – 15:00:** SOC response executed—active sessions revoked, credentials reset, MFA re-registered, attacker IPs blocked, and malicious emails purged from mailboxes.

---

## 🛡️ MITRE ATT&CK Mapping

| Tactic | Technique ID | Technique name | Evidenced by |
| :--- | :--- | :--- | :--- |
| **Initial Access** | `T1566.002` | Phishing: Spearphishing Link | Variants A and B contained malicious links targeting staff inboxes. |
| **Credential Access** | `T1598.003` | Phishing for Information: Spearphishing Link | Links directed users to credential-harvesting login pages. |
| **Initial Access** | `T1078.004` | Valid Accounts: Cloud Accounts | Attacker successfully signed into compromised user accounts (`freya.lynn` and `ryan.boyd`) using stolen credentials. |

---

## 🚨 Indicators of Compromise (IOCs)

| Type | Value | First Seen (UTC) | Context |
| :--- | :--- | :--- | :--- |
| **Domain** | `cloudora-hr-portal.example` | Aug 25, 2026, 07:54 | Lookalike phishing and credential-harvesting domain |
| **URL** | `hxxps://cloudora-hr-portal[.]example/payroll/login` | Aug 25, 2026, 07:54 | Variant A harvesting link (defanged) |
| **IPv4** | `198.18.44.10` | Aug 25, 2026, 07:54 | Variant A sender IP |
| **IPv4** | `198.18.44.23` | Aug 25, 2026, 08:31 | Variant A wave-2 sender IP |
| **IPv4** | `198.18.51.7` | Aug 25, 2026, 08:35 | Variant B lookalike relay server IP |
| **IPv4** | `198.18.7.200` | Aug 25, 2026, 10:34 | Attacker sign-in IP (Netherlands) |

---

## 📈 Recommendations & Lessons Learned
1. **DMARC & Mail Gateway Enforcement:** Implement mail gateway filters to automatically flag external emails attempting to spoof internal domains like `cloudora.io`.
2. **Behavioral Alerting:** Configure alerts for sign-in anomalies occurring shortly after a user clicks external URLs.
3. **Staff Awareness:** Emphasize reporting speed while supplementing training since email authentication alone does not guarantee message safety.
