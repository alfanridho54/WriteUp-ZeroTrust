# WriteUp-ZeroTrust
# Implementing Cloudflare Zero Trust with Privileged Access Management for SMK Harapan Bangsa Virtual Lab:  
## Architecture, Security Analysis & Lessons Learned

# Section 1: Introduction

## The Challenge: Securing Education in a Perimeter-less World

When **SMK Harapan Bangsa** needed to provide secure remote access to its virtual laboratory for TKJ students, the traditional approach was clear: rely on VPN or direct SSH. However, in a modern threat landscape, these methods are no longer sufficient, especially when students access lab environments from unmanaged personal devices.

### The Traditional Problem: A False Sense of Security

The virtual lab at SMK Harapan Bangsa, built on **Proxmox VE**, is the heart of the students' technical learning. It hosts critical assets:
- Student practice virtual machines (vmsiswa1, vmsiswa2, etc.)
- Pre-configured networking and server administration environments.
- Teacher and administrative configurations.

Traditional remote access (VPN/Exposed SSH) created a massive **Attack Surface**:
1.  **Flat Network Risks:** Once connected via VPN, a user often gains broad network access, making **Lateral Movement** trivial for an attacker or a curious student.
2.  **Lack of Granularity:** There was no way to ensure a student could *only* access their assigned VM and nothing else.
3.  **Visibility Gap:** Traditional methods offer limited audit trails. We couldn't easily see *what* commands were being executed inside the sessions.
4.  **Command Abuse:** Risk of accidental or intentional destructive commands (e.g., `rm -rf /` or `shutdown`) affecting the entire host.
5.  **Authentication Weakness:** Relying solely on passwords left the lab vulnerable to credential stuffing and brute-force attacks.

### The Solution: Zero Trust + Privileged Access Management (PAM)

To solve this, we moved away from the "Trust, but Verify" model to **"Never Trust, Always Verify."** By implementing **Cloudflare Zero Trust** as the identity-aware proxy and a **Jump Server** as the PAM layer, we created a "Budget-Friendly" but "Enterprise-Grade" security stack:

1.  **Identity-Based Access:** Access is tied to the student's authenticated email (SSO), not just a shared password.
2.  **Cloudflare Tunnel:** Eliminates the need for open inbound ports, effectively hiding the laboratory from the public internet.
3.  **Controlled Bastion (Jump Server):** An Ubuntu 22.04 host acting as the single, monitored point of entry with a web-based terminal.
4.  **Custom PAM Layer:** Role-based access control (RBAC) that restricts students to specific VMs and logs every session for accountability.
5.  **Proxmox Backend:** Secured virtualization that only accepts traffic from the internal Jump Server.

Now, students visit `ssh.alfanlab.my.id`, authenticate via Cloudflare, and are automatically routed to their assigned environments. Dangerous commands are blocked, and every action is recorded.

### What You'll Learn in This Write-up

This documentation covers the end-to-end journey of this implementation:
1.  **Architecture Design:** How we integrated Cloudflare, the Jump Server, and Proxmox without expensive hardware.
2.  **Security Assessment:** Vulnerabilities we closed and the "Known Risks" that remain.
3.  **Operational Reality:** What happened during real-world testing with students.
4.  **Lessons Learned:** Practical trade-offs between security, budget, and usability in an educational setting.

This is more than a tutorial; it is a **production-ready Proof of Concept** designed to empower SMKs and small organizations to embrace secure, remote technical learning.

---
