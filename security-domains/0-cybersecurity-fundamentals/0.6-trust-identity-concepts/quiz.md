### Q1 — Real-World Scenario: Certificate Validation (ONE-BLOCK VERSION)

**Question**  
Explain step by step how a browser validates the certificate chain when visiting `https://bank.com`.  
Include Root CA, Intermediate CA, and End‑Entity cert. What if the Intermediate is missing or the Root is untrusted?

**Answer (Summary)**  
1) **Root CA** → Trusted roots are pre-installed in the OS/Browser trust store. The chain must terminate at one of these.  
2) **Intermediate CA** → Signed by the Root; issues end‑entity certs. The browser verifies the Intermediate’s signature. If the Intermediate is **missing**, validation fails unless the server supplies it (or the client fetches via AIA).  
3) **End‑Entity (bank.com)** → Signed by the Intermediate. Browser checks:  
   - Signature validity  
   - Expiry/NotBefore  
   - Revocation (OCSP/CRL/OCSP stapling)  
   - Hostname matches CN/SAN (`bank.com`)  
4) **Failure cases** → Missing Intermediate or untrusted Root breaks the chain → browser shows “Connection not private / Untrusted issuer”.

**ASCII Flow (Chain of Trust + Validation)**  
            [ Root CA ]  ──trusted in OS/Browser──► (Trust Store)
                 │
                 ▼
        [ Intermediate CA ]  ──“signed by Root”
                 │
                 ▼
     [ End‑Entity Cert: bank.com ]
                 │
                 ▼
   Browser Validation: signature ✔ | expiry ✔ | revocation ✔ | hostname ✔
                 │
       ┌─────────┴─────────┐
       ▼                   ▼
 [ Padlock 🔒 Shown ]   [ ⚠️ Warning: Not Private ]

**Interview One‑Liner**  
“Browsers validate HTTPS by chaining leaf → Intermediate → Root CA in the trust store and checking signature, expiry, hostname, and revocation; missing Intermediate or untrusted Root = trust failure.”

