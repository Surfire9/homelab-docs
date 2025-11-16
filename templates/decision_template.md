# Architecture Decision Records

## ADR-###: ?Title/Description of Decision (YYY-MM-DD)

### Status
Accepted

### Context
-

###Options considered:
- 

### Decision
-

### Rationale
**Pros:**
- 

**Cons:**
- 

### Consequences
- 

### Alternatives Considered
- 

---
- **Docker container:** More flexibility but requires managing the host OS, manual add-ons, and additional maintenance overhead  
- **Home Assistant Core:** Most complex; no add-on system; harder to maintain long-term  
- **Dedicated physical device (Pi / Mini PC):** Good isolation but less efficient than using existing Proxmox resources  
- **General-purpose Linux VM + Home Assistant Supervised:** Not officially supported; higher breakage risk  

---

