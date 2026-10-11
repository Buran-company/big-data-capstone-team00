| Data Asset or Zone | Classification | Allowed Roles | Protection | Retention / Deletion | Evidence |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Raw Telemetry & Webhooks (Bronze Zone) | Restricted Personal Data (PII) | Ingestion Service Identity, Platform Engineers | Encryption at rest (AES-256), in-transit (TLS 1.3), raw storage isolation, no analyst access | Short-term raw retention (30 days), automated lifecycle expiration | Data catalogue entry, ingestion pipeline logs, access control list (ACL) policy |
| Cleaned & Masked Events (Silver Zone) | Internal / Pseudonymized | Data Engineers, Analytics Engineers, ML Engineers | Field-level tokenization, IP address hashing, pseudonymization of user identifiers, RBAC enforcement | 1-year operational retention, automated PII purge workflows linked to user deletion requests | Lineage documentation, data quality test results (Great Expectations), audit logs |
| Curated Aggregates & Marts (Gold Zone) | Business Confidential (Non-PII) | Live-Ops Managers, Data Analysts, Finance Team, Executive Stakeholders | Aggregated metrics only, masking of granular identifiers, role-based database permissions (ClickHouse/BI) | Long-term retention (up to 3 years) for historical trend analysis and financial compliance | BI access logs, semantic layer definitions, periodic access review reports |

# Governance & Privacy Rules

### Data Ownership:

The owner of the game data domain is the Client Engineering Team, the financial data is the Finance & Monetization Team, and the platform infrastructure is the Data Engineering Team

### Privacy & Minimization:

All personal data (email, IP addresses, payment tokens) must be masked at the border of the transition from the Bronze to Silver zone in accordance with GDPR requirements

### Access Control:

Human roles (analysts, engineers) and service accounts (Airflow, Spark, Flink) are separated according to the principle of least Privilege. Direct access to the raw zone (Bronze) is strictly limited to pipeline technical accounts