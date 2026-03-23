# RCM_Healthcare_Application
Integrating healthcare systems effectively into a cohesive architecture directly revenue life cycles, operational efficiency, patient experience, and finacial  sustainability.
# MediLink RCM — Unified Healthcare Revenue Cycle Management Integration Platform

> An intelligent middleware layer integrating healthcare providers, insurers, and government identity systems into a single, fraud-resilient Revenue Cycle Management (RCM) platform — powered by LLM-driven anomaly detection and real-time IPRS verification.

---

## Table of Contents

- [Overview](#overview)
- [Key Stakeholders](#key-stakeholders)
- [System Architecture](#system-architecture)
- [Core Modules](#core-modules)
- [Technology Stack](#technology-stack)
- [Government IPRS Integration](#government-iprs-integration)
- [Fraud Detection System](#fraud-detection-system)
- [LLM-Powered Intelligence Layer](#llm-powered-intelligence-layer)
- [Data Flow](#data-flow)
- [Security & Compliance](#security--compliance)
- [API Reference](#api-reference)
- [Deployment](#deployment)
- [Roadmap](#roadmap)

---

## Overview

**MediLink RCM** is a Cloverleaf-inspired enterprise integration platform that acts as a neutral, intelligent broker between disparate healthcare and insurance systems. It normalizes data across HL7 FHIR, X12 EDI, and proprietary payer formats — enabling seamless claims submission, adjudication, and settlement while actively defending the revenue cycle against fraud, waste, and abuse.

```
┌──────────────────────────────────────────────────────────────────────┐
│                        MEDILINK RCM PLATFORM                         │
│                                                                      │
│  Healthcare Providers ◄──► Integration Engine ◄──► Insurance Payers │
│                                    │                                 │
│                         ┌──────────┴──────────┐                     │
│                         │   Fraud Detection   │                     │
│                         │   LLM AI Engine     │                     │
│                         │   IPRS Gateway      │                     │
│                         └─────────────────────┘                     │
└──────────────────────────────────────────────────────────────────────┘
```

---

## Key Stakeholders

| Stakeholder | Role | Interaction |
|---|---|---|
| **Hospitals & Clinics** | Primary data producers | Submit claims, receive remittance advice |
| **Insurance Payers (Sha & Shif/Private)** | Claims adjudicators & payers | Receive validated claims, issue EOBs |
| **Patients** | Beneficiaries | Identity verified via IPRS; access to claim status |
| **Government (IPRS / MOH)** | Regulatory & identity authority | Provide national ID & biometric verification |
| **Compliance Officers** | Internal watchdogs | Access fraud dashboards & audit trails |
| **Revenue Cycle Managers** | Operational owners | Monitor KPIs, denial trends, cash flow |
| **System Integrators / DevOps** | Platform operators | Manage connectors, pipelines, deployments |
| **Regulators (IRA / AKI)** | Legal oversight | Receive regulatory reports; enforce compliance |

---

## System Architecture

### High-Level Architecture

```
                           ┌─────────────────────────────────┐
                           │        API Gateway Layer         │
                           │  (Kong / AWS API GW / Nginx)     │
                           └──────────────┬──────────────────┘
                                          │
            ┌─────────────────────────────┼──────────────────────────┐
            │                             │                           │
   ┌────────▼────────┐         ┌──────────▼────────┐      ┌─────────▼────────┐
   │  Provider Side  │         │  Integration Bus   │      │  Payer Side      │
   │  HL7 FHIR R4    │◄───────►│  Apache Kafka      │◄────►│  X12 EDI 837/835 │
   │  DICOM / REST   │         │  Message Queues    │      │  REST / SOAP     │
   └────────┬────────┘         └──────────┬─────────┘      └─────────┬────────┘
            │                             │                           │
            └─────────────────────────────┼───────────────────────────┘
                                          │
                        ┌─────────────────▼──────────────────┐
                        │         Core RCM Engine             │
                        │                                      │
                        │  ┌──────────┐  ┌─────────────────┐  │
                        │  │ Eligibi- │  │ Claims          │  │
                        │  │ lity     │  │ Processing      │  │
                        │  │ Engine   │  │ & Adjudication  │  │
                        │  └──────────┘  └─────────────────┘  │
                        │  ┌──────────┐  ┌─────────────────┐  │
                        │  │ Prior    │  │ Remittance      │  │
                        │  │ Auth     │  │ & ERA/EOB       │  │
                        │  │ Engine   │  │ Processing      │  │
                        │  └──────────┘  └─────────────────┘  │
                        └─────────────────┬──────────────────┘
                                          │
               ┌──────────────────────────┼──────────────────────────┐
               │                          │                           │
    ┌──────────▼──────────┐  ┌────────────▼───────────┐  ┌──────────▼──────────┐
    │   IPRS Identity     │  │   Fraud Detection      │  │   Data Warehouse    │
    │   Verification GW   │  │   & LLM AI Engine      │  │   & Analytics       │
    │   (Govt. API)       │  │   (Anomaly + Risk)     │  │   (BI Dashboard)    │
    └─────────────────────┘  └────────────────────────┘  └─────────────────────┘
```

### Microservices Architecture

Each domain is an independently deployable service communicating over event-driven messaging:

```
medilink-rcm/
├── services/
│   ├── gateway/              # API Gateway + Auth (OAuth2/SMART on FHIR)
│   ├── identity/             # IPRS verification + patient matching
│   ├── eligibility/          # Real-time benefit verification
│   ├── prior-auth/           # Pre-authorization workflows
│   ├── claims/               # 837 ingestion, validation, routing
│   ├── adjudication/         # Rules engine + payer logic
│   ├── remittance/           # 835 parsing + ERA posting
│   ├── fraud-detection/      # Rules + ML + LLM scoring
│   ├── notifications/        # Alerts, webhooks, SMS/email
│   ├── audit/                # Immutable audit trail (event sourcing)
│   └── analytics/            # Reporting + dashboards
├── integrations/
│   ├── hl7-fhir/             # FHIR R4 translator
│   ├── x12-edi/              # EDI 270/271/276/277/278/835/837
│   ├── iprs-gateway/         # Govt. IPRS API adapter
│   ├── SHA & Shif/                 # SHA & Shif connector
│   └── private-payers/       # Insurance API connectors
├── infrastructure/
│   ├── kafka/                # Event streaming
│   ├── postgres/             # Transactional data
│   ├── redis/                # Caching + session
│   ├── elasticsearch/        # Claims search + fraud indexing
│   └── vault/                # Secrets management
└── ml/
    ├── fraud-models/         # Trained ML anomaly models
    ├── llm-agent/            # LLM orchestration layer
    └── embeddings/           # Clinical code vectorization
```

---

## Core Modules

### 1. Integration Engine
- **Protocol Adapters:** HL7 v2.x, HL7 FHIR R4, X12 EDI, DICOM, REST, SOAP
- **Message Transformation:** Mirth Connect / custom adapters for bi-directional translation
- **Routing Logic:** Content-based routing using Apache Camel rules
- **Data Normalization:** Canonical data model mapped to FHIR resources

### 2. Eligibility & Benefits Verification
- Real-time 270/271 transactions with payers
- Sha & Shif member validation and benefit limits lookup
- Coverage gap alerts before service delivery
- Patient cost-share calculation engine

### 3. Claims Processing
- Automated 837I/837P claim generation from clinical data
- Code validation: ICD-10-CM, CPT, HCPCS, DRG
- Business rules engine for payer-specific edits
- Claim scrubbing and clearinghouse routing

### 4. Adjudication Engine
- Auto-adjudication of clean claims (target >85%)
- Payer contract fee schedule application
- Coordination of Benefits (COB) handling
- Denial management with root-cause categorization

### 5. Remittance & Payment Posting
- 835 ERA automated posting to provider systems
- Unapplied payment resolution workflow
- Reconciliation dashboards with variance alerts

### 6. Prior Authorization
- 278 transaction support
- Gold-carding logic for high-approval providers
- LLM-assisted clinical note summarization for auth requests

---

## Technology Stack

### Backend
| Layer | Technology |
|---|---|
| **Runtime** | Node.js 20 (services) + Python 3.11 (ML/AI) |
| **API Framework** | FastAPI (Python), Express.js (Node) , Laravel
| **Message Broker** | Apache Kafka + Kafka Streams |
| **Integration Engine** | Apache Camel + Mirth Connect |
| **Workflow Orchestration** | Apache Airflow / Temporal.io |
| **Task Queue** | Celery + Redis |

### Data
| Layer | Technology |
|---|---|
| **Primary Database** | PostgreSQL 16 (HIPAA-compliant, encrypted) |
| **Cache** | Redis Cluster |
| **Search & Analytics** | Elasticsearch 8 |
| **Time Series** | TimescaleDB (metrics, audit logs) |
| **Data Warehouse** | Apache Iceberg on S3 + dbt |
| **Vector Store** | Pgvector / Pinecone (LLM embeddings) |

### AI / ML
| Layer | Technology |
|---|---|
| **LLM Provider** | Anthropic Claude API / OpenAI GPT-4o |
| **ML Framework** | scikit-learn, XGBoost, PyTorch |
| **Model Serving** | BentoML / TorchServe |
| **Feature Store** | Feast |
| **Experiment Tracking** | MLflow |
| **Embeddings** | SentenceTransformers + medical BERT |

### Infrastructure
| Layer | Technology |
|---|---|
| **Container Runtime** | Docker + Kubernetes (EKS/GKE) |
| **Service Mesh** | Istio |
| **API Gateway** | Kong Enterprise |
| **Secrets Management** | HashiCorp Vault |
| **IaC** | Terraform + Helm |
| **CI/CD** | Jenkins |
| **Observability** | Prometheus + Grafana + Jaeger (tracing) |
| **Logging** | ELK Stack (Elasticsearch + Logstash + Kibana) |

### Security
| Layer | Technology |
|---|---|
| **Identity & Access** | Keycloak (OAuth2 / OpenID Connect) |
| **SMART on FHIR** | SMART App Launch Framework |
| **Encryption** | AES-256 at rest, TLS 1.3 in transit |
| **PKI** | Let's Encrypt + internal CA |
| **WAF** | AWS WAF / Cloudflare |

---

## Government IPRS Integration

### Overview
The **Integrated Population Registration Services (IPRS)** gateway provides real-time national identity verification to eliminate ghost beneficiaries, identity theft, and fraudulent claims.

### Verification Flow

```
Patient Encounter
      │
      ▼
┌─────────────────────┐
│ Capture National ID │  ← National ID / Passport / Alien ID
│ or Biometric Data   │  ← Fingerprint / Facial (optional)
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐      ┌──────────────────────────┐
│  IPRS Gateway       │─────►│  IPRS Government API     │
│  (Adapter Service)  │◄─────│  (eCitizen / NIIMS)      │
└──────────┬──────────┘      └──────────────────────────┘
           │
           ▼
┌─────────────────────────────────────────────────────────┐
│              Identity Verification Response              │
│  ✓ Full Name  ✓ Date of Birth  ✓ Gender  ✓ Photo       │
│  ✓ Active Status  ✓ KRA PIN match  ✓ Sha & Shif Number       │
└──────────────────────────────────────────────────────────┘
           │
    ┌──────┴──────┐
    │             │
  Match        No Match
    │             │
    ▼             ▼
Proceed       Flag + Alert
with claim    Compliance Officer
```

### IPRS API Adapter

```python
# iprs_gateway/adapter.py

class IPRSGateway:
    """
    Adapter for Kenya IPRS / eCitizen identity verification.
    Implements circuit breaker pattern for resilience.
    """

    async def verify_identity(self, national_id: str) -> IdentityVerificationResult:
        """
        Verify patient identity against IPRS.
        Returns verified demographics or raises VerificationError.
        """
        payload = {
            "idNumber": national_id,
            "idType": "NATIONAL_ID",  # | PASSPORT | ALIEN_ID | BIRTH_CERT
            "requestId": str(uuid4()),
            "timestamp": datetime.utcnow().isoformat()
        }

        async with self.circuit_breaker:
            response = await self.http_client.post(
                url=settings.IPRS_API_URL,
                json=payload,
                headers=self._signed_headers(),
                timeout=5.0
            )

        return IdentityVerificationResult(
            verified=response["status"] == "FOUND",
            full_name=response.get("fullName"),
            dob=response.get("dateOfBirth"),
            gender=response.get("gender"),
            photo_base64=response.get("photo"),
            Sha & Shif_number=response.get("Sha & ShifNumber"),
            confidence_score=response.get("matchScore", 0.0)
        )

    async def bulk_verify(self, national_ids: List[str]) -> List[IdentityVerificationResult]:
        """Batch verification for eligibility roster validation."""
        tasks = [self.verify_identity(nid) for nid in national_ids]
        return await asyncio.gather(*tasks, return_exceptions=True)
```

### IPRS Integration Use Cases

| Use Case | Trigger | Action |
|---|---|---|
| **Patient Registration** | New patient enrollment | Verify ID; auto-populate demographics |
| **Claims Submission** | Before claim is sent to payer | Validate beneficiary exists and is active |
| **Eligibility Check** | Pre-service verification | Confirm Sha & Shif membership + coverage |
| **Ghost Beneficiary Audit** | Scheduled batch job | Flag inactive/deceased members in payer databases |
| **Provider Credentialing** | Onboarding new providers | Verify practitioner identity + licensing |

---

## Fraud Detection System

### Multi-Layer Defense Model

```
Claim Submitted
      │
      ▼
Layer 1: Rules Engine (milliseconds)
  ├── Duplicate claim detection
  ├── Date/logic anomalies (service before admission, etc.)
  ├── Unbundling & upcoding rule patterns
  ├── Place-of-service mismatches
  └── Provider-NPI validation
      │
      ▼
Layer 2: Statistical ML Models (< 1 second)
  ├── Isolation Forest (unsupervised anomaly detection)
  ├── XGBoost classifier (trained on labeled fraud cases)
  ├── Peer comparison models (provider vs. cohort benchmarks)
  └── Network graph analysis (provider-patient-claim rings)
      │
      ▼
Layer 3: LLM Semantic Analysis (2-5 seconds)
  ├── Clinical note ↔ diagnosis code consistency check
  ├── Procedure narrative plausibility scoring
  ├── Temporal sequence coherence analysis
  └── Cross-claim narrative fingerprinting
      │
      ▼
┌─────────────────────────────────────────────┐
│            Risk Score Aggregation           │
│  Combined Score = w1*Rules + w2*ML + w3*LLM │
│                                              │
│  0-30   → AUTO APPROVE                      │
│  31-65  → EXPEDITED REVIEW                  │
│  66-85  → MANUAL REVIEW QUEUE               │
│  86-100 → AUTO DENY + ALERT COMPLIANCE      │
└─────────────────────────────────────────────┘
```

### Fraud Pattern Library

The rules engine contains curated patterns for known fraud schemes:

```yaml
# fraud_rules/patterns.yaml

patterns:
  - id: FRD-001
    name: Duplicate Claim
    description: Same provider, patient, service date, and procedure code
    severity: HIGH
    action: AUTO_DENY

  - id: FRD-002
    name: Phantom Billing
    description: Service billed but not documented in clinical record
    severity: CRITICAL
    action: AUTO_DENY + ALERT

  - id: FRD-003
    name: Upcoding
    description: Billed code inconsistent with documented complexity/time
    severity: HIGH
    action: REVIEW + DOWNCODE

  - id: FRD-004
    name: Unbundling
    description: Billing component codes when a comprehensive code applies
    severity: MEDIUM
    action: REVIEW + BUNDLE

  - id: FRD-005
    name: Impossible Dates
    description: Service date before birth date or after death date
    severity: CRITICAL
    action: AUTO_DENY + IPRS_CHECK

  - id: FRD-006
    name: Ghost Beneficiary
    description: Patient identity unverifiable via IPRS
    severity: CRITICAL
    action: AUTO_DENY + COMPLIANCE_ALERT

  - id: FRD-007
    name: Provider Outlier
    description: Provider billing >3 SD above peer cohort for procedure
    severity: MEDIUM
    action: PEER_REVIEW

  - id: FRD-008
    name: Kickback Network
    description: Graph analysis detects unusual provider-referral clustering
    severity: HIGH
    action: INVESTIGATION_QUEUE
```

### ML Model Pipeline

```python
# fraud_detection/ml_pipeline.py

class FraudScoringPipeline:
    """
    Ensemble ML pipeline for claim-level fraud risk scoring.
    """

    def __init__(self):
        self.isolation_forest = joblib.load("models/isolation_forest_v3.pkl")
        self.xgb_classifier = joblib.load("models/xgb_fraud_v5.pkl")
        self.feature_extractor = ClaimFeatureExtractor()
        self.graph_analyzer = ProviderNetworkAnalyzer()

    async def score_claim(self, claim: ClaimDTO) -> FraudScore:
        # Extract structured features
        features = self.feature_extractor.extract(claim)

        # Unsupervised anomaly score
        anomaly_score = self.isolation_forest.decision_function([features])[0]
        anomaly_normalized = self._normalize_anomaly(anomaly_score)

        # Supervised fraud probability
        fraud_proba = self.xgb_classifier.predict_proba([features])[0][1]

        # Network/graph risk
        graph_risk = await self.graph_analyzer.provider_risk_score(
            provider_npi=claim.provider_npi,
            patient_id=claim.patient_id
        )

        # Weighted ensemble
        combined_score = (
            0.30 * anomaly_normalized +
            0.50 * fraud_proba * 100 +
            0.20 * graph_risk
        )

        return FraudScore(
            claim_id=claim.id,
            total_score=round(combined_score, 2),
            anomaly_score=anomaly_normalized,
            ml_fraud_probability=fraud_proba,
            network_risk_score=graph_risk,
            triggered_rules=self._check_rules(claim),
            recommendation=self._recommendation(combined_score)
        )
```

---

## LLM-Powered Intelligence Layer

The LLM agent acts as a **clinical reasoning layer** — reading clinical documentation and cross-referencing it against submitted codes to detect semantic inconsistencies that rule-based systems miss.

### LLM Fraud Detection Agent

```python
# llm_agent/fraud_analyst.py

class LLMFraudAnalyst:
    """
    LLM agent for semantic analysis of clinical claims.
    Uses Claude claude-sonnet-4-6 for medical reasoning.
    """

    SYSTEM_PROMPT = """
    You are a senior healthcare fraud analyst with deep expertise in medical coding,
    clinical documentation, and insurance claims adjudication.

    Your job is to analyze a submitted insurance claim alongside supporting clinical
    notes and determine if the claim is consistent, plausible, and correctly coded.

    Respond ONLY in valid JSON with this schema:
    {
      "consistency_score": 0-100,
      "plausibility_score": 0-100,
      "coding_accuracy_score": 0-100,
      "red_flags": ["list of specific concerns"],
      "recommendation": "APPROVE | REVIEW | ESCALATE | DENY",
      "reasoning": "concise clinical explanation"
    }
    """

    async def analyze_claim(
        self,
        claim: ClaimDTO,
        clinical_notes: str,
        prior_claims: List[ClaimDTO]
    ) -> LLMFraudAnalysis:

        user_message = self._build_analysis_prompt(claim, clinical_notes, prior_claims)

        response = await self.anthropic_client.messages.create(
            model="claude-sonnet-4-6",
            max_tokens=1024,
            system=self.SYSTEM_PROMPT,
            messages=[{"role": "user", "content": user_message}]
        )

        return LLMFraudAnalysis.parse_raw(response.content[0].text)

    def _build_analysis_prompt(self, claim, notes, prior_claims) -> str:
        return f"""
        ## Claim Under Review
        - Claim ID: {claim.id}
        - Provider NPI: {claim.provider_npi}
        - Service Date: {claim.service_date}
        - Diagnosis Codes: {', '.join(claim.diagnosis_codes)}
        - Procedure Codes: {', '.join(claim.procedure_codes)}
        - Billed Amount: KES {claim.billed_amount:,.2f}

        ## Clinical Documentation
        {clinical_notes}

        ## Prior Claims History (Last 12 Months)
        {self._format_prior_claims(prior_claims)}

        Analyze this claim for fraud, waste, or abuse indicators.
        """
```

### LLM Use Cases Beyond Fraud

| Use Case | Description |
|---|---|
| **Prior Auth Summarization** | Summarize clinical notes for faster PA decisions |
| **Denial Appeal Drafting** | Auto-generate appeal letters from denial reason codes |
| **Code Suggestion** | Recommend ICD-10/CPT codes from clinical note content |
| **Contract Analysis** | Extract payer contract terms for fee schedule loading |
| **Patient Communication** | Generate plain-language EOB explanations |
| **Audit Report Generation** | Produce narrative compliance audit reports |

---

## Data Flow

### End-to-End Claim Journey

```
1. REGISTRATION
   Patient presents → IPRS identity verified → Eligibility checked (270/271)
   → Benefits confirmed → Pre-auth requested if required (278)

2. SERVICE DELIVERY
   Clinical documentation created (FHIR Encounter + Observation resources)
   → Codes assigned (ICD-10, CPT, DRG)

3. CLAIM CREATION
   837P/837I generated → Claim scrubbed (edits applied)
   → Fraud pre-screening (Layer 1 rules)

4. FRAUD SCORING
   ML scoring pipeline runs → LLM semantic analysis (if score > 30)
   → Final risk score assigned → Routing decision made

5. SUBMISSION
   Clean claims → Payer gateway → Real-time acknowledgment (277CA)
   → Status tracking (276/277)

6. ADJUDICATION
   Payer adjudicates → ERA received (835)
   → Auto-posted to provider system → Remittance reconciliation

7. EXCEPTIONS
   Denials → Root-cause analysis → Appeal workflow initiated
   Fraud flags → Compliance dashboard → Investigation workflow
```

---

## Security & Compliance

### Standards & Regulations

| Standard | Scope |
|---|---|
| **Kenya Health Act 2017** | National healthcare data governance |
| **Kenya Data Protection Act 2019** | Patient data privacy & consent |
| **HIPAA (where applicable)** | International payer requirements |
| **IRA Regulations** | Insurance Regulatory Authority compliance |
| **ISO 27001** | Information security management |
| **HL7 FHIR R4** | Clinical data interoperability |
| **PCI DSS** | Payment card data security |

### Security Controls

- **Zero-Trust Architecture** — No implicit trust; every service authenticates
- **End-to-End Encryption** — AES-256 at rest; TLS 1.3 in transit
- **Role-Based Access Control (RBAC)** — Least-privilege across all services
- **Immutable Audit Logs** — Event-sourced, tamper-evident audit trail
- **Data Masking** — PII masked in non-production environments
- **Penetration Testing** — Quarterly third-party security assessments
- **PHI Tokenization** — Patient identifiers tokenized in analytics pipelines
- **SMART on FHIR** — Granular patient data access scopes

---

## API Reference

### Core Endpoints

```
# Identity Verification
POST   /api/v1/identity/verify              Verify patient via IPRS
POST   /api/v1/identity/verify/biometric    Biometric verification

# Eligibility
POST   /api/v1/eligibility/check            Real-time eligibility (270/271)
GET    /api/v1/eligibility/{patient_id}     Cached eligibility status

# Claims
POST   /api/v1/claims/submit                Submit new claim
GET    /api/v1/claims/{claim_id}/status     Claim status (276/277)
GET    /api/v1/claims/{claim_id}/eob        Explanation of Benefits

# Fraud
GET    /api/v1/fraud/score/{claim_id}       Fraud risk score
GET    /api/v1/fraud/alerts                 Active fraud alerts
POST   /api/v1/fraud/review/{claim_id}      Manual review decision

# Prior Authorization
POST   /api/v1/auth/request                 Submit PA request (278)
GET    /api/v1/auth/{auth_id}/status        PA status

# Analytics
GET    /api/v1/analytics/denial-trends      Denial trend analysis
GET    /api/v1/analytics/fraud-summary      Fraud detection summary
GET    /api/v1/analytics/rcm-kpis           Revenue cycle KPIs
```

---

## Deployment

### Prerequisites
- Kubernetes 1.28+
- Helm 3.12+
- PostgreSQL 16
- Apache Kafka 3.6
- Redis 7.2

### Quick Start

```bash
# Clone repository
git clone https://github.com/your-org/medilink-rcm.git
cd medilink-rcm

# Configure environment
cp .env.example .env
# Edit .env with your IPRS credentials, payer endpoints, LLM API keys

# Deploy infrastructure
terraform -chdir=infrastructure/terraform init
terraform -chdir=infrastructure/terraform apply

# Deploy services via Helm
helm repo add medilink https://charts.medilink.io
helm install medilink-rcm medilink/rcm \
  --namespace rcm-prod \
  --values values.production.yaml

# Verify deployment
kubectl get pods -n rcm-prod
kubectl run health-check --image=curlimages/curl --rm -it -- \
  curl http://medilink-rcm-gateway/health
```

### Environment Variables

```bash
# Identity
IPRS_API_URL=https://api.iprs.go.ke/v2
IPRS_API_KEY=<your-api-key>
IPRS_SIGNING_CERT=<path-to-cert>

# LLM
ANTHROPIC_API_KEY=<your-claude-key>
LLM_FRAUD_THRESHOLD=65           # Score above which LLM analysis is triggered

# Payers
Sha & Shif_API_URL=https://api.Sha & Shif.or.ke
Sha & Shif_API_KEY=<Sha & Shif-key>

# Database
DATABASE_URL=postgresql://user:pass@postgres:5432/medilink
REDIS_URL=redis://redis:6379/0

# Kafka
KAFKA_BOOTSTRAP_SERVERS=kafka:9092
KAFKA_SECURITY_PROTOCOL=SASL_SSL
```

---

## Roadmap

| Phase | Timeline | Deliverables |
|---|---|---|
| **Phase 1 — Core Integration** | Q1 2025 | FHIR adapter, X12 EDI, Sha & Shif connector, basic eligibility |
| **Phase 2 — Fraud Foundation** | Q2 2025 | Rules engine, ML models, IPRS gateway, audit logging |
| **Phase 3 — LLM Intelligence** | Q3 2025 | LLM fraud agent, PA summarization, denial appeal automation |
| **Phase 4 — Advanced Analytics** | Q4 2025 | BI dashboards, predictive denials, provider scorecards |
| **Phase 5 — National Scale** | Q1 2026 | MOH integration, county health systems, national fraud registry |

---

## Contributing

Please read [CONTRIBUTING.md](CONTRIBUTING.md) for our development workflow and code standards. All contributors must sign our CLA and complete healthcare data privacy training.

## License

Proprietary — © 2025 MediLink Systems. All rights reserved.

---

*Built with care for Kenya's healthcare system — improving access, reducing waste, and protecting every shilling in the revenue cycle.*
