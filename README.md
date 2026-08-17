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

  * **Screenshot Evidence:**
  <img width="1920" height="1080" alt="IP where it camefrom" src="https://github.com/user-attachments/assets/5e59dd96-702a-498f-9dcb-9a24696b97f3" />
  
      *Explanation:* Displays the raw email header showing the specific sender IP address (`198.18.44.10`) from which the initial wave of Variant A phishing originated.
     <img width="1920" height="1080" alt="Authenticaiton results the SPF, DKIM" src="https://github.com/user-attachments/assets/13312ec5-941c-45d8-87f3-dd83993b885e" />
 
      *Explanation:* Highlights the failed authentication results (`spf=fail`, `dkim=fail`) indicating that the message source was not authorized to send mail on behalf of the domain.
   <img width="1920" height="1080" alt="3 differet ip but in same neighbouthood" src="https://github.com/user-attachments/assets/8ec8e658-3de7-49d2-84c1-98db08d48735" />
 
      *Explanation:* Demonstrates that multiple distinct IP addresses within the same subnet block were used to dispatch these phishing emails, revealing attacker infrastructure scaling.
  
* **Variant B (Passed Authentication / Lookalike Domain):** Sent from an attacker-controlled lookalike domain (`cloudora-hr-portal.example`) that successfully passed authentication checks (`spf=pass`, `dkim=pass`), proving that successful technical authentication alone does not guarantee message safety.

  * **Screenshot Evidence:**
    <img width="1920" height="1080" alt="2 diff server" src="https://github.com/user-attachments/assets/d305b9b2-5efe-4d21-8896-c764929e3b89" />
       *Explanation:* Shows the routing headers across two different intermediate mail relay servers involved in transmitting Variant B.
   <img width="1920" height="1080" alt="One same domain" src="https://github.com/user-attachments/assets/fb3ceaa9-e29e-4132-a943-8345ad469a92" />
  
      *Explanation:* Highlights the header configuration where the return-path and envelope sender point back to the lookalike domain structure.

* **Benign False Positive (Mailchimp Newsletter):** An employee-forwarded "Cloudora Monthly" newsletter sent via Mailchimp (`mail105.suw16.mcsv.net`, IP `198.18.60.5`) was fully verified as a legitimate marketing communication.

  * **Screenshot Evidence:**
  <img width="1920" height="1080" alt="newsletter authentication pass" src="https://github.com/user-attachments/assets/09410a6f-13f1-43a1-be8d-54d74aea2eed" />
       *Explanation:* Displays the successful authentication results (`spf=pass`, `dkim=pass`, `dmarc=pass`) for the legitimate Mailchimp delivery server.
  <img width="1920" height="1080" alt="comparison on real email and fake one" src="https://github.com/user-attachments/assets/301f3f2f-1f58-4c98-842c-c09ade1b2e0e" />
  
      *Explanation:* A side-by-side technical comparison contrasting the cryptographic signatures of authentic marketing mail against simulated phishing attempts.
    <img width="1920" height="1080" alt="cloudora known IP on letter one" src="https://github.com/user-attachments/assets/24a7811f-9abb-40b8-9eb8-fab61b5e9498" />
  
      *Explanation:* Confirms the authorized Mailchimp IP address (`198.18.60.5`) mapped against known corporate communication standards.
  <img width="1920" height="1080" alt="sends back to same domain letter onr" src="https://github.com/user-attachments/assets/71e50696-bd86-4d7f-bb6c-4f25380a54d4" />
  
      *Explanation:* Illustrates the expected `From` and `Reply-To` headers pointing correctly to official internal identifiers.

---

### 2. Campaign Delivery Scope & Impact
* **Delivery Metrics:** Analysis of mail flow tracking tracking how many accounts were reached.

  * **Screenshot Evidence:**
<img width="1920" height="1080" alt="No of acc it was delieverd to" src="https://github.com/user-attachments/assets/845461f2-9234-40b5-b361-67b7672bc64a" />
  
      *Explanation:* Email gateway logs showing the exact volume of recipient mailboxes targeted and successfully reached across the organization before mitigation rules took effect.

* **Credential Harvesting & Compromise:** Across the targeted distribution, users interacted with malicious links. 

  * **Screenshot Evidence:**
  <img width="1920" height="1080" alt="account who got compromised" src="https://github.com/user-attachments/assets/fac67687-2810-4799-8173-3239ca7c93c8" />
  
      *Explanation:* Directory log identifying the user accounts (`freya.lynn` and `ryan.boyd`) whose sessions were hijacked following credential submission.
  <img width="1920" height="1080" alt="2 victim" src="https://github.com/user-attachments/assets/4940e24a-6eba-412a-8df3-6e249f64d5ba" />
  
      *Explanation:* Security alert telemetry detailing the subsequent anomalous sign-in events originating from foreign IP infrastructure (`198.18.7.200` in the Netherlands).

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
