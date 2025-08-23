# 🇱🇧 Lebanese FinTech BDL Compliance Guide v2.1

<div align="center">

[![Version](https://img.shields.io/badge/Version-2.1-ff6b6b)](https://github.com/yourusername/lebanon-fintech-bdl-compliance/releases/tag/v2.1)
[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)
[![Last Updated](https://img.shields.io/badge/Updated-December%202024-blue)]()
[![Amendments](https://img.shields.io/badge/Banking%20Secrecy-Amended%202025-orange)]()

### **The First Comprehensive Public Reference Guide for Lebanese FinTech BDL Compliance**
### **Now Updated with 2022/2025 Banking Secrecy Law Amendments**

[📖 Read the Guide](https://yourusername.github.io/lebanon-fintech-bdl-compliance/) | 
[🌐 Interactive Map v2.1](https://yourusername.github.io/lebanon-fintech-bdl-compliance/visual-map/) | 
[📥 Download PDF](https://github.com/yourusername/lebanon-fintech-bdl-compliance/releases/latest) |
[🔄 What's New in v2.1](#whats-new-in-v21)

</div>

---

## 🆕 What's New in v2.1 (December 2024)

### 📜 Banking Secrecy Law Amendments
- **Law No. 306/2022 & 2025 Amendments** now permit authorized access for:
  - Judiciary and Special Investigation Commission (SIC)
  - Tax authorities for compliance verification
  - BDL and Banking Control Commission (BCC) for supervision
  - National Authority for Fighting Corruption
  - Appointed independent auditors

### 🔍 Key Clarifications
- **Cloud Prohibition:** Clarified as practical effect of data sovereignty requirements, not explicit circular
- **RTO/RPO Targets:** Clearly marked as recommendations, not BDL mandates
- **HSM Requirements:** FIPS 140-2 Level 2 acceptable for certain use cases, Level 3 preferred
- **Official References:** Added direct links to BDL circular documents

---

## 🚨 Critical Compliance Alert

> **⚠️ IMPORTANT CLARIFICATION:** While BDL has not issued a standalone circular explicitly prohibiting all forms of cloud computing, its stringent data sovereignty requirements, coupled with the Banking Secrecy Law provisions and BDL's supervisory oversight, effectively mandate that all sensitive financial data for regulated activities must remain within Lebanese jurisdiction.

### ❌ **Absolute Prohibitions (Practical Effect)**
- International cloud hosting (AWS, Azure, GCP) for production data
- Data export without explicit client consent and legal framework
- Outsourcing of compliance monitoring (Circular 128/2013)
- Remote access from outside Lebanon to production systems
- Data processing outside Lebanese territory
- Backup replication to foreign data centers

### ✅ **Mandatory Requirements**
- Prior BDL approval for all digital banking applications
- 24-hour incident reporting to BDL
- Two-factor authentication for all system access
- Board-approved business continuity plans
- Lebanese jurisdiction for all data
- End-to-end encryption for all transmissions

---

## 📚 Documentation Structure

### Core Documents
- **[Main Guide v2.1](docs/guide/bdl-compliance-guide-v2.1.md)** - Complete compliance framework
- **[Banking Secrecy Amendments](docs/guide/amendments/banking-secrecy-amendments-2022-2025.md)** - Detailed analysis of recent changes
- **[Cloud Prohibition Explained](docs/guide/clarifications/cloud-prohibition-explained.md)** - Why cloud is effectively prohibited
- **[Official BDL Links](docs/guide/references/official-bdl-links.md)** - Direct links to all circulars

### Quick References
- **[Executive Summary](docs/guide/executive-summary.md)** - For C-level executives
- **[Quick Start Guide](docs/guide/quick-start-guide.md)** - For implementation teams
- **[Compliance Checklist v2.1](templates/compliance-checklist-v2.1.xlsx)** - Updated with amendments

---

[Rest of README continues with standard sections...]
