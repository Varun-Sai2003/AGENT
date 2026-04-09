---
skill: data-security-privacy
version_covered: general
depth: expert
last_updated: 2026-03-25
source: web-research
use_when: >
  Use this skill to design enterprise-grade cloud security architectures, implement robust cryptography
  (PQC, JWE) and identity protocols (OIDC, WebAuthn), mitigate critical OWASP vulnerabilities,
  and lead comprehensive compliance strategies (SOC2, ISO 27001).
---

# Data Security & Privacy — Expert Reference

> **Quick Summary**: Data Privacy governs legal rights and ethical handling of personal data. Data Security provides the technical implementation (cryptography, architecture, AppSec) protecting that data. This guide covers the highest level of mastery, from Post-Quantum Cryptography and Zero Trust Cloud Architecture to DevSecOps and advanced OWASP defense.

---

## 1. Overview & Core Philosophy

The paradigm has shifted from perimeter-based firewalls to **Identity as the New Perimeter**. 
- **Zero Trust Architecture (ZTA)**: "Never trust, always verify." No entity—internal or external, human or machine—is trusted implicitly. Every access request is strongly authenticated, contextually evaluated, and granted Least Privilege.
- **Privacy by Design**: Embedding privacy controls (data minimization, granular consent, pseudonymization) natively into the system's architecture rather than bolting them on.

---

## 2. Advanced Identity & Authentication Protocols

Understanding the nuance between *who you are* (Authentication) and *what you can do* (Authorization) is critical.

### The Protocols
- **OAuth 2.0**: An **Authorization** framework. Issues *Access Tokens* to third parties allowing them limited access to resources. Not for authentication.
- **OpenID Connect (OIDC)**: An **Authentication** layer built on top of OAuth 2.0. Issues *ID Tokens* (JWTs) serving as proof of identity. Powers modern Single Sign-On (SSO).
- **SAML 2.0**: XML-based standard for SSO, primarily used in legacy Enterprise settings. Heavyweight compared to OIDC but ubiquitous in B2B.
- **FIDO2 & WebAuthn**: The gold standard for **passwordless authentication**. Uses public-key cryptography and hardware/platform authenticators (FaceID, YubiKey) to eliminate passwords and effectively neutralize phishing globally.

### Token Standards (JOSE Suite)
- **JWT (JSON Web Token)**: Signed JSON holding claims. Used for integrity and non-repudiation. (Visible payload).
- **JWE (JSON Web Encryption)**: Encrypted JSON. Used when the token payload contains highly sensitive PII that the client should not be able to read or tamper with.
- *Best Practice*: Use **Nested JWTs** — sign the token for authenticity (JWS), then encrypt the signed token (JWE) for confidentiality.

---

## 3. Advanced Cryptography & Key Management

### The Future of Crypto
- **Post-Quantum Cryptography (PQC)**: Algorithms (e.g., Kyber, Dilithium) designed to withstand attacks from quantum computers. Transitioning architectures toward **Crypto-Agility** (the ability to rapidly swap algorithms) is essential now.
- **Homomorphic Encryption (HE)**: Allows mathematical computation directly on ciphertext without decrypting it. Ideal for privacy-preserving AI and analytics on untrusted cloud providers.

### Enterprise Key Architecture
- **KMS (Key Management System)**: The control plane orchestrating the lifecycle of keys (generation, rotation, destruction).
- **HSM (Hardware Security Module)**: The physical data plane. A FIPS 140-2 Level 3 tamper-resistant hardware boundary where master keys are generated and cryptographic operations execute. 
- *Best Practice*: Your KMS should always be backed by a dedicated HSM (CloudHSM or physical). Master keys never leave the HSM in plaintext.

---

## 4. Cloud Security & Zero Trust Architecture

Operating in AWS/GCP/Azure requires rigorous guardrails.

- **Identity as Perimeter**: AWS IAM is the primary security boundary. Network isolation (VPCs, Security Groups) is defense-in-depth, not the main shield.
- **IAM Permissions Boundaries**: Advanced IAM policies setting the *maximum* possible permissions an entity can have. Prevents privilege escalation when delegating IAM administration.
- **Service Control Policies (SCPs)**: AWS Organizations guardrails. E.g., An SCP can explicitly deny creating resources outside of approved regions or disabling CloudTrail, overriding any local admin permissions.
- **Micro-segmentation**: Using VPC Endpoints (PrivateLink) to route traffic to AWS services over the internal backbone, keeping sensitive data off the public internet.

---

## 5. DevSecOps & Secure CI/CD (Shift-Left)

Security must be automated inside the pipeline to scale.

- **SAST (Static Application Security Testing)**: Scans uncompiled source code for vulnerabilities (SQLi, hardcoded secrets). Fast, implemented in pre-commit hooks or PR checks.
- **DAST (Dynamic Application Security Testing)**: Black-box testing against a running application to find runtime issues, auth bypasses, and server misconfigurations.
- **SCA (Software Composition Analysis)**: Scans package.json/requirements.txt against CVE databases. Secures the Software Supply Chain.
- **Image Signing**: Using tools like Cosign to cryptographically sign container images. Kubernetes admission controllers reject unsigned or compromised images.

---

## 6. Advanced OWASP Mitigations

Beyond basic input validation, mitigating modern web attacks requires structural defenses.

### SSRF (Server-Side Request Forgery)
*Attack*: Tricking the server into querying internal APIs or cloud metadata services.
*Mitigation*: 
- Strict positive allow-listing of domains/IPs. 
- In Cloud, enforce **IMDSv2** (requires session tokens for metadata access).
- Network segmentation: The backend service fetching URLs should have egress blocked to the internal VPC.

### CSRF (Cross-Site Request Forgery)
*Attack*: Forcing an authenticated browser to execute unwanted actions.
*Mitigation*: 
- `SameSite=Strict` or `Lax` cookie attributes.
- Enforce the **Synchronizer Token Pattern** (stateful) or **Double Submit Cookie** (stateless).
- Note: XSS completely bypasses CSRF protections.

### Insecure Deserialization
*Attack*: Modifying serialized objects (Java, Python Pickle) to execute remote code during deserialization.
*Mitigation*: 
- **DO NOT deserialize untrusted data.**
- Use data-only formats (JSON, Protobuf).
- If native deserialization is mandatory, implement strict class allow-lists and digitally sign (HMAC) the serialized data before transmission.

### XSS (Cross-Site Scripting)
*Mitigation*: 
- Context-aware output encoding (React/Angular do this by default).
- Strict **Content Security Policy (CSP)** headers limiting script execution to trusted domains and disabling `unsafe-inline`.

---

## 7. Global Compliance & Governance

Managing data security formally requires mapping controls to frameworks.

- **SOC 2 Type II**: Validates the operational effectiveness of controls over time (usually 6-12 months). Evaluated against AICPA's Trust Services Criteria: Security (required), Availability, Processing Integrity, Confidentiality, Privacy.
- **ISO 27001**: Formal certification of an Information Security Management System (ISMS). Focuses heavily on continuous risk assessment and improvement.
- **The DPO (Data Protection Officer) Role**: Mandated by GDPR. Serves as an independent officer overseeing compliance, conducting Data Protection Impact Assessments (DPIA), and liaising with regulators. The DPO works with engineering to ensure *Privacy by Design*.

---

## 8. Quick Reference Cheat Sheet

| Threat / Requirement | Expert Standard Implementation |
| :--- | :--- |
| **Data at Rest** | AES-256-GCM via KMS backed by FIPS-140-2 Level 3 HSM. |
| **Data in Transit** | TLS 1.3 only, strict cipher suites, HSTS preloading. |
| **Password Storage** | Don't. Use WebAuthn/Passkeys. If forced: Argon2id. |
| **Session Tracking** | HTTPOnly, Secure, SameSite=Strict cookies. |
| **Cloud Blast Radius** | Separate AWS Accounts per environment via Organizations + SCPs. |
| **AuthZ / Tokens** | Nested JWE (Signed then Encrypted). Short TTLs + Rotating Refresh Tokens. |

---

## 9. Worked Examples

### Example 1: Creating a Nested JWT (JWS + JWE) in Node.js

Provides both non-repudiation (Signature) and absolute confidentiality (Encryption).

```javascript
const jose = require('node-jose');

async function createSecureNestedToken(payload, signingKey, encryptionKey) {
  // 1. Sign the payload (JWS)
  const signedToken = await jose.JWS.createSign({ format: 'compact' }, signingKey)
    .update(JSON.stringify(payload))
    .final();

  // 2. Encrypt the signed token (JWE)
  const nestedJwe = await jose.JWE.createEncrypt({ format: 'compact' }, encryptionKey)
    .update(signedToken)
    .final();

  return nestedJwe;
}
```

### Example 2: Robust SSRF Defense (Node.js)

Mitigating SSRF by strictly validating the parsed URL object against an allow-list, preventing DNS rebinding and IP obfuscation.

```javascript
const { URL } = require('url');
const dns = require('dns').promises;

const ALLOWED_DOMAINS = ['api.trustedpartner.com'];

async function fetchExternalResource(userSubmittedUrl) {
  let parsedUrl;
  try {
    parsedUrl = new URL(userSubmittedUrl);
  } catch (err) {
    throw new Error('Invalid URL format');
  }

  // 1. Protocol Validation
  if (parsedUrl.protocol !== 'https:') {
    throw new Error('Only HTTPS is allowed');
  }

  // 2. Domain Allow-listing
  if (!ALLOWED_DOMAINS.includes(parsedUrl.hostname)) {
    throw new Error('Domain not in allow-list');
  }
  
  // 3. Prevent DNS Rebinding (Resolve and check internal IPs before fetching)
  const resolvedIps = await dns.resolve(parsedUrl.hostname);
  for (const ip of resolvedIps) {
     if (isPrivateIP(ip)) { // Custom function checking 10.x, 192.168.x, 169.254.x.x
         throw new Error('Resolved to internal network');
     }
  }

  // Proceed with safe fetch using a timeout
  return secureFetch(parsedUrl.href);
}
```

---

## 10. Go Deeper — Resources

- **Cryptography**: [NIST Post-Quantum Cryptography Standardization](https://csrc.nist.gov/projects/post-quantum-cryptography)
- **Identity**: [WebAuthn Guide](https://webauthn.guide/) | [OAuth 2.0 & OIDC Specs](https://oauth.net/2/)
- **AppSec**: [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/) (Crucial for secure coding)
- **Cloud ZTA**: [AWS Zero Trust Architecture](https://aws.amazon.com/security/zero-trust/)
- **Compliance**: [AICPA SOC Suite of Services](https://www.aicpa-cima.com/resources/landing/system-and-organization-controls-soc-suite-of-services)

---

*Generated by skill-learning. Reload this file to restore expert-level knowledge of data-security-privacy.*
*Source: advanced web-research*
