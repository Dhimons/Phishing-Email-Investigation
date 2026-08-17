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

* **Variant A (Failed Authentication & Relays):** Used the lookalike domain (`cloudora-hr-portal.example`) but failed all authentication checks (`spf=fail`, `dkim=fail`) because they were sent from unauthorized infrastructure across multiple IP addresses in the same network neighborhood.

  > **Evidence 1:**
<img width="1920" height="1080" alt="IP where it camefrom" src="https://github.com/user-attachments/assets/68f9f350-5647-48e2-b551-15e3936bd9cd" />

  > *Explanation:* Displays the raw email header showing the specific sender IP address (`198.18.44.10`) from which the initial wave of Variant A phishing originated.

  > **Evidence 2:**
<img width="1920" height="1080" alt="Authenticaiton results the SPF, DKIM" src="https://github.com/user-attachments/assets/8ef08c58-e507-4004-8367-38bb1a088416" />

  > *Explanation:* Highlights the failed authentication results (`spf=fail`, `dkim=fail`) indicating that the message source was not authorized to send mail on behalf of the domain.

  > **Evidence 3:**
<img width="1920" height="1080" alt="3 differet ip but in same neighbouthood" src="https://github.com/user-attachments/assets/8c99e8dc-461e-45aa-ad53-57dd41aebaf0" />

  > *Explanation:* Demonstrates that multiple distinct IP addresses within the same subnet block were used to dispatch these phishing emails, revealing attacker infrastructure scaling.

* **Variant B (Passed Authentication / Lookalike Domain):** Sent from an attacker-controlled lookalike domain (`cloudora-hr-portal.example`) that successfully passed authentication checks (`spf=pass`, `dkim=pass`), proving that successful technical authentication alone does not guarantee message safety.

  > **Evidence 1:**
<img width="1920" height="1080" alt="variemt B authenticatio pass but a phish" src="https://github.com/user-attachments/assets/5498aa6f-df54-4448-9958-a57f09b17038" />

  > *Explanation:* Shows authentication headers successfully passing cryptographic checks despite belonging to a fraudulent lookalike domain infrastructure.

  > **Evidence 2:**
<img width="1920" height="1080" alt="2 diff server" src="https://github.com/user-attachments/assets/ea27b0ce-2bd2-4ce3-b255-2530c8568161" />

  > *Explanation:* Illustrates the routing headers across two different intermediate mail relay servers involved in transmitting Variant B.

  > **Evidence 3:**
<img width="1920" height="1080" alt="One same domain" src="https://github.com/user-attachments/assets/ef2372ca-1fdf-432c-b7f1-3d912c335bdd" />

  > *Explanation:* Highlights header configurations where the return-path points back to the lookalike domain structure.

  > **Evidence 4:**
<img width="1920" height="1080" alt="Third indictor different adress" src="https://github.com/user-attachments/assets/befde754-a4aa-4c0b-9e1c-777f0c34d997" />

  > *Explanation:* Details discrepancies found within alternate reply-to routing indicators.

* **Benign False Positive (Mailchimp Newsletter):** An employee-forwarded "Cloudora Monthly" newsletter sent via Mailchimp (`mail105.suw16.mcsv.net`, IP `198.18.60.5`) was fully verified as a legitimate marketing communication.

  > **Evidence 1:**
<img width="1920" height="1080" alt="newsletter authentication pass" src="https://github.com/user-attachments/assets/bc3ab018-dd26-4d49-be28-511ab39a8144" />

  > *Explanation:* Displays successful authentication results (`spf=pass`, `dkim=pass`, `dmarc=pass`) for the legitimate Mailchimp delivery server.

  > **Evidence 2:**
  > <img width="1920" height="1080" alt="comparison on real email and fake one" src="https://github.com/user-attachments/assets/8d650333-a336-4b90-a8c7-62aed6b1b19b" />

  > *Explanation:* A side-by-side technical comparison contrasting cryptographic signatures of authentic marketing mail against simulated phishing attempts.

  > **Evidence 3:**
<img width="1920" height="1080" alt="cloudora known IP on letter one" src="https://github.com/user-attachments/assets/a01a5ad6-0ff1-46e4-a8b8-9f6df1800495" />

  > *Explanation:* Confirms the authorized Mailchimp IP address (`198.18.60.5`) mapped against known corporate communication standards.

  > **Evidence 4:**
<img width="1920" height="1080" alt="sends back to same domain letter onr" src="https://github.com/user-attachments/assets/1eee40db-13f8-40fe-827a-42700c2a6c6d" />

  > *Explanation:* Illustrates `From` and `Reply-To` headers pointing correctly to official internal identifiers.

---

### 2. Campaign Delivery Scope & Impact
* **Delivery Metrics:** Analysis of mail flow tracking showing how many accounts were reached.

  > **Evidence 1:**
<img width="1920" height="1080" alt="No of acc it was delieverd to" src="https://github.com/user-attachments/assets/600437cd-50b5-4eb7-b657-b276f84a0c75" />

  > *Explanation:* Email gateway logs showing the exact volume of recipient mailboxes targeted and successfully reached across the organization before mitigation rules took effect.

* **Credential Harvesting & Compromise:** Across the targeted distribution, users interacted with malicious links. 

  > **Evidence 1:**
<img width="1920" height="1080" alt="account who got compromised" src="https://github.com/user-attachments/assets/826b8736-9691-4dd9-acd7-1a73538904cd" />

  > *Explanation:* Directory log identifying user accounts (`freya.lynn` and `ryan.boyd`) whose sessions were hijacked following credential submission.

  > **Evidence 2:**
 <img width="1920" height="1080" alt="2 victim" src="https://github.com/user-attachments/assets/f996abb3-f141-4f49-a9b4-2b535a901e2f" />

  > *Explanation:* Security alert telemetry detailing subsequent anomalous sign-in events originating from foreign IP infrastructure (`198.18.7.200` in the Netherlands).

  > **Evidence 3:**
<img width="1920" height="1080" alt="Victim" src="https://github.com/user-attachments/assets/70461caa-ca19-450d-9eba-2f22ff1a7dc6" />

  > *Explanation:* Consolidated session log showing multi-factor interaction tracking and account lockout confirmations.

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
