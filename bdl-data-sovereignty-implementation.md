# 🇱🇧 Data Sovereignty Implementation Guide

## Executive Summary
This guide provides technical and operational requirements for implementing BDL-compliant data sovereignty controls. Sensitive financial data covered by Lebanese banking regulation is expected to remain within Lebanese jurisdiction. In practice, BDL supervisory access, the Banking Secrecy Law, and related circulars mean production data and core regulated activities should be hosted in Lebanon, unless a clear legal basis and explicit supervisory approval exists for an alternative.

---

## 🔴 Core Sovereignty Requirements

### Legal Foundation
- **Banking Secrecy Law (1956, as amended 2022-2025)** — criminal penalties can apply when secrecy obligations are breached.
- **BDL Supervisory Access Requirements** — institutions must ensure regulators can access required data in Lebanon.
- **Criminal penalties for unauthorized data export**

### Effectively Prohibited
| Practice (for production / secrecy-covered data)                 | Why it’s generally not acceptable without approval                                                       |
| ---------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| Hosting on **foreign public clouds** (AWS/Azure/GCP)             | Foreign jurisdiction & limits on supervisory access; conflicts with secrecy obligations                  |
| **Cross-border transfers** of secrecy-protected data             | Banking Secrecy Law requires a legal basis & supervisory approval; client consent alone isn’t sufficient |
| **International backup/replication**                             | Data leaves Lebanon; control & access issues                                                             |
| **Foreign processing** (including remote admin without controls) | Supervisory access & secrecy obligations may be undermined                                               |

BDL/BCC may impose supervisory measures and sanctions **case-by-case** under applicable laws and circulars. **When in doubt, seek written guidance/approval**.
---

## 📊 Data Classification Framework

The following classes are **our implementation mapping** to support BDL/BCC access and secrecy obligations.

### Class 1: Banking Secrecy Protected (RED) 🔴
**Mandatory Lebanese Hosting - Zero Exceptions**

#### Data Types
- Customer personal information (names, IDs, addresses)
- KYC documentation and verification data
- Account details and balances
- Transaction history and patterns
- Financial statements and reports
- Credit information and scores

#### Technical Requirements
```yaml
# Organization baseline aligned to ISO/IEC 27001/27017.
# Confirm details with BDL/BCC where applicable.
# BDL 144 stresses cybersecurity capabilities and governance, not specific cipher suites
encryption:
  at_rest: AES-256-GCM
  in_transit: TLS 1.3 (minimum 1.2)
  key_management: HSM FIPS 140-2 Level 3 or Level 2 for certain cases
  
access_control:
  authentication: Multi-factor mandatory
  authorization: Role-based with approval workflow
  logging: Immutable audit trail
  monitoring: Real-time anomaly detection
  
infrastructure:
  location: Lebanese data center only
  segregation: Physical or logical isolation
  backup: Lebanese secondary site
  disaster_recovery: In-country only
```

### Class 2: Regulatory Protected (ORANGE) 🟠
**Lebanese Hosting Required for Supervisory Access**

#### Data Types
- System logs and audit trails
- Configuration management data
- Regulatory reports and submissions
- Compliance documentation
- Risk assessments and controls

#### Technical Requirements
```yaml
retention:
# Retain audit and system logs for multiple years, consistent with BDL/BCC expectations, AML/CFT record-keeping, and business needs; confirm periods in your compliance plan.
  audit_logs: x years minimum
  system_logs: y years minimum
  configuration: Version controlled
  
access:
  bdl_supervisory: Read-only on demand
  bcc_audit: Full access during audits
  internal_audit: Scheduled access
```

### Class 3: Business Protected (YELLOW) 🟡
**Preferably Lebanese Hosted**

#### Data Types
- Internal documentation
- Training materials
- Non-sensitive operational data
- Development/test data (anonymized)

### Class 4: Public (GREEN) 🟢
**No Residency Restrictions**

#### Data Types
- Marketing materials
- Public website content
- Product documentation
- Press releases

---

## 🏗️ Technical Architecture

### Network Architecture
```
┌─────────────────────────────────────────────────┐
│            LEBANESE TERRITORY ONLY              │
├─────────────────────────────────────────────────┤
│                                                 │
│  ┌──────────────┐        ┌──────────────┐     │
│  │   Internet   │────────│   Lebanese   │     │
│  │   Gateway    │        │   ISP Only   │     │
│  └──────────────┘        └──────────────┘     │
│           │                                    │
│           ▼                                    │
│  ┌──────────────────────────────────┐         │
│  │         DMZ (Lebanon)            │         │
│  │  ┌────────┐    ┌────────┐       │         │
│  │  │  WAF   │    │  IDS   │       │         │
│  │  └────────┘    └────────┘       │         │
│  └──────────────────────────────────┘         │
│           │                                    │
│           ▼                                    │
│  ┌──────────────────────────────────┐         │
│  │     Application Layer            │         │
│  │  ┌─────────────┐ ┌─────────────┐│         │
│  │  │   App       │ │   API       ││         │
│  │  │   Servers   │ │   Gateway   ││         │
│  │  └─────────────┘ └─────────────┘│         │
│  └──────────────────────────────────┘         │
│           │                                    │
│           ▼                                    │
│  ┌──────────────────────────────────┐         │
│  │      Data Layer                  │         │
│  │  ┌─────────────┐ ┌─────────────┐│         │
│  │  │  Primary    │ │  Encrypted  ││         │
│  │  │  Database   │ │   Storage   ││         │
│  │  └─────────────┘ └─────────────┘│         │
│  └──────────────────────────────────┘         │
│           │                                    │
│           ▼                                    │
│  ┌──────────────────────────────────┐         │
│  │    Backup & DR (Lebanon)         │         │
│  │  ┌─────────────┐ ┌─────────────┐│         │
│  │  │   Backup    │ │     DR      ││         │
│  │  │   Site 1    │ │   Site 2    ││         │
│  │  └─────────────┘ └─────────────┘│         │
│  └──────────────────────────────────┘         │
│                                                 │
└─────────────────────────────────────────────────┘
```

### Implementation Controls

#### 1. Geo-Fencing Controls
```javascript
// Example geo-fencing implementation
const GeoFenceControl = {
  allowedCountry: 'LB',
  allowedCoordinates: {
    north: 34.6920,
    south: 33.0550,
    east: 36.6250,
    west: 35.1040
  },
  
  validateAccess: function(request) {
    const clientIP = request.headers['x-forwarded-for'];
    const geoLocation = getGeoLocation(clientIP);
    
    if (geoLocation.country !== this.allowedCountry) {
      logSecurityEvent('GEOFENCE_VIOLATION', request);
      return {
        allowed: false,
        reason: 'Access denied - Outside Lebanese territory'
      };
    }
    return { allowed: true };
  }
};
```

#### 2. Data Residency Validation
```python
# Example data residency check
class DataResidencyValidator:
    def __init__(self):
        self.allowed_datacenters = [
            'beirut-dc1',
            'beirut-dc2',
            'lebanon-backup'
        ]
    
    def validate_storage_location(self, data_object):
        storage_location = data_object.get_storage_location()
        
        if storage_location not in self.allowed_datacenters:
            raise DataSovereigntyViolation(
                f"Data cannot be stored in {storage_location}. "
                f"Must use Lebanese datacenter."
            )
        
        # Additional validation
        self.verify_no_replication_outside_lebanon(data_object)
        self.audit_log_validation(data_object)
        
        return True
```

#### 3. Encryption Implementation
```yaml
# Encryption configuration template
encryption_config:
  data_at_rest:
    algorithm: AES-256-GCM
    key_derivation: PBKDF2
    iterations: 100000
    key_rotation: 90_days
    
  data_in_transit:
    protocols:
      - TLSv1.3
      - TLSv1.2  # fallback only
    cipher_suites:
      - TLS_AES_256_GCM_SHA384
      - TLS_AES_128_GCM_SHA256
    certificate: 
      type: EV SSL
      key_size: 4096
      
  key_management:
    hsm:
      vendor: "Lebanese-approved HSM vendor"
      certification: FIPS 140-2 Level 3
      backup: Secondary HSM in Lebanon
    master_key:
      storage: HSM only
      access: Dual control
      audit: Every access logged
```

---

## 🔍 Monitoring & Compliance

### Continuous Monitoring Requirements

#### Real-Time Monitoring Dashboard
```
┌─────────────────────────────────────────┐
│     DATA SOVEREIGNTY DASHBOARD          │
├─────────────────────────────────────────┤
│                                         │
│  Data Location Status:  ✅ COMPLIANT    │
│  ├─ Primary DC:        Beirut DC1      │
│  ├─ Secondary DC:      Beirut DC2      │
│  └─ DR Site:          Lebanon Backup   │
│                                         │
│  Cross-Border Attempts: 0               │
│  Geo-Fence Violations:  0               │
│  Encryption Status:     100%            │
│                                         │
│  Last BDL Audit:       2025-08-01      │
│  Next Review:          2025-09-01      │
│                                         │
└─────────────────────────────────────────┘
```

### Audit Trail Requirements
- Every data access logged with user, time, location
- Immutable audit logs stored for 7 years
- Real-time alerting for sovereignty violations
- Monthly compliance reports to management
- Quarterly reports to BDL (if requested)

---

## 🚨 Incident Response

Activate the BDL/BCC reporting workflow according to your approved incident plan.

### Data Sovereignty Violation Response

#### Immediate Actions (0-15 minutes)
1. **ISOLATE** - Block all cross-border connections
2. **IDENTIFY** - Determine scope of violation
3. **CONTAIN** - Prevent further data exposure
4. **ALERT** - Notify security team and management

#### Short-term Actions (15 minutes - 4 hours)
1. **ASSESS** - Full impact assessment
2. **DOCUMENT** - Complete incident documentation
3. **REPORT** - Prepare BDL notification (if required)
4. **REMEDIATE** - Implement immediate fixes

#### Follow-up Actions (4-24 hours)
1. **NOTIFY** - Report **promptly** to BDL/BCC; many institutions target **within 24 hours of confirmation** as an internal objective. Notify the **SIC** when incidents intersect **ML/TF** or suspicious activity obligations.
2. **REVIEW** - Root cause analysis
3. **UPDATE** - Strengthen controls
4. **TEST** - Verify remediation effectiveness

---

## 📋 Compliance Checklist

Organization checklist aligned to ISO 27001/27017 and our BDL mapping; confirm frequencies with BCC.

### Daily Checks
- [ ] Verify all data remains in Lebanese datacenters
- [ ] Review geo-fence violation alerts
- [ ] Check encryption status for all sensitive data
- [ ] Monitor cross-border connection attempts

### Weekly Reviews
- [ ] Audit data classification compliance
- [ ] Review access logs for anomalies
- [ ] Verify backup locations
- [ ] Test geo-fencing controls

### Monthly Assessments
- [ ] Complete data residency audit
- [ ] Review and update data flow diagrams
- [ ] Validate DR site readiness
- [ ] Generate compliance reports

### Quarterly Validations
- [ ] Third-party residency attestation
- [ ] Penetration testing of controls
- [ ] BDL compliance self-assessment
- [ ] Board reporting on sovereignty status

---

## 🔗 References

- Banking Secrecy Law (1956, as amended Oct 2022) — official BDL English PDF. 
- Bank of Lebanon and the Middle East
- BDL Basic Circular 69/2000 – Electronic Banking & Financial Operations (scope & controls for e-operations). 
- Bank of Lebanon and the Middle East
- sic.gov.lb
- BDL Basic Circular 144/2017 – Prevention of Cybercrime (cybersecurity governance/controls). 
- Bank of Lebanon and the Middle East
- blog.blominvestbank.com
- BDL Basic Circular 141/2017 – Recovery/Resolution planning elements (continuity, planning; useful for DR posture). 
- Bank of Lebanon and the Middle East
- BDL Basic/Intermediate Circular 128/2013 (selected governance/outsourcing constraints; review the official Arabic/English copy before quoting). 
- Bank of Lebanon and the Middle East
- ISO/IEC 27001 & 27017 (your baseline control source).

NIST SP 800-53 (supplementary, non-BDL).
---
This guide reflects **our implementation approach** to meet **BDL/BCC supervisory access, Banking Secrecy obligations**, and relevant circulars (e.g., 69/2000, 144/2017). It does **not** introduce new legal obligations and should be read alongside the official texts. **In practice, we host secrecy-covered production data in Lebanon, avoid cross-border transfers unless a clear legal basis and explicit supervisory approval exist**, and maintain strong cybersecurity controls aligned with **ISO/IEC 27001/27017**. Institutions should consult BDL/BCC and legal counsel for **case-specific approvals**.
---
**Document Classification:** CONFIDENTIAL  
**Last Updated:** August 2025  
**Version:** 1.0  
**Owner:** Information Security Department
