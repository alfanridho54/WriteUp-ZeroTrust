# WriteUp-ZeroTrust
# Implementing Cloudflare Zero Trust with Privileged Access Management for SMK Harapan Bangsa Virtual Lab:  
## Architecture, Security Analysis & Lessons Learned

## Introduction

When **SMK Harapan Bangsa** needed to provide secure remote access to its virtual laboratory for TKJ students, the traditional approach was clear: rely on VPN or direct SSH. But both methods carried fundamental security flaws—especially when students access the lab from home using personal devices.

### The Traditional Problem

The virtual lab at SMK Harapan Bangsa, built on **Proxmox VE**, contains valuable learning assets:
- Student practice virtual machines (vmsiswa1, vmsiswa2, etc.)
- Pre-configured lab environments for networking, server administration, and cybersecurity practice
- Teacher/administration credentials and configurations

Traditional remote access methods (VPN or exposed SSH) created serious risks:
1. Once connected to the VPN, users gained broad network access
2. No granular control — a compromised student account could affect other VMs
3. Limited visibility and audit trail of student activities
4. High risk of lateral movement and command abuse (e.g., `shutdown`, `rm -rf`)
5. Dependency on weak password-only authentication

These issues became more critical as the school wanted to support flexible, location-independent practical learning while maintaining strong security.

### Enter Zero Trust + Privileged Access Management (PAM)

Zero Trust is a security model that assumes breach and verifies every access request. Combined with **Privileged Access Management (PAM)**, it provides:
- Identity-based authentication (never trust by location)
- Least privilege enforcement
- Session monitoring and command restriction
- Complete audit trail

This approach aligns with NIST SP 800-207 and modern remote access best practices.

### What We Implemented

For SMK Harapan Bangsa’s Virtual Lab, we designed and implemented a **Privileged Access Management system based on Cloudflare Zero Trust**. The solution replaces traditional VPN with a modern, browser-based secure access model consisting of:

1. **Cloudflare Zero Trust Access** as the identity-aware gateway (using One-Time PIN via email)
2. **Cloudflare Tunnel** as a secure private network connector (no inbound ports needed)
3. **Jump Server** (Ubuntu 22.04) acting as a controlled bastion host with web terminal
4. **Custom PAM Layer** including role-based VM access, command restriction, and session logging
5. **Proxmox VE** as the backend virtualization platform

Students now visit `ssh.alfanlab.my.id`, authenticate with their email via Cloudflare, and are automatically routed only to their assigned VM through the Jump Server. All activities are logged and dangerous commands are blocked in real-time.

### What You'll Learn

This writeup documents the complete journey of the implementation:
1. **Why Zero Trust + PAM** was chosen over traditional VPN for an educational environment
2. **How we architected** the full solution (Cloudflare + Jump Server + Proxmox)
3. **Security assessment** — vulnerabilities addressed and remaining risks
4. **Real operational experience** from deployment and testing
5. **Lessons learned** during design, implementation, and evaluation

By the end, you will understand:
- How to implement practical Zero Trust for educational virtual labs
- Integration between Cloudflare Zero Trust, Jump Server, and Proxmox
- Real-world trade-offs between security, usability, and maintainability in a school setting
- Actionable architecture that can be replicated in other SMKs or small organizations

This is not just theory — it’s a production-ready proof of concept that successfully secured remote access for virtual lab practical sessions at SMK Harapan Bangsa.

Let's dive in.
