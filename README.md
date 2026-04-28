# WriteUp-ZeroTrust
# Implementing Cloudflare Zero Trust with Privileged Access Management for SMK Harapan Bangsa Virtual Lab:  
## Architecture, Security Analysis & Lessons Learned

# Section 1: Introduction

## Securing Education in a Perimeter-less World: The SMK Harapan Bangsa Story

Imagine it’s Monday morning. A student is at home, trying to access the school’s virtual laboratory for a networking practice session. The teacher needs to ensure the student can only access their assigned VM, while the IT staff is worried that one wrong command or one compromised personal laptop could take down the entire lab infrastructure.

In the past, the answer was simple: "Just use a VPN" or "Open an SSH port." But in today’s world, those answers are not just outdated—they are dangerous. 

This is the story of how **SMK Harapan Bangsa** transformed its digital laboratory from a vulnerable network into a secure, identity-driven environment.

### The Traditional Problem: A False Sense of Security

The virtual lab at SMK Harapan Bangsa, powered by **Proxmox VE**, is the heart of technical learning for TKJ students. It hosts critical assets:
- Individual student practice VMs (`vmsiswa1`, `vmsiswa2`, etc.)
- Pre-configured server administration and cybersecurity environments.
- Teacher and administrative configurations.

When we analyzed traditional remote access (VPN/Exposed SSH), we found a massive **Attack Surface** that put the entire school’s lab at risk:

1.  **The "Flat Network" Trap:** Once a student connects via VPN, they often gain broad access to the internal network. This makes **Lateral Movement** trivial a curious student or an attacker could easily "jump" from their VM to the teacher’s server.
2.  **The Visibility Gap:** We had no way to see what was happening *inside* a session. If a critical file was deleted or a server was shut down, there was no granular audit trail to identify who did it or how.
3.  **Command Abuse & "Fat Finger" Errors:** In a learning environment, mistakes happen. Without restrictions, a student could accidentally (or intentionally) run destructive commands like `rm -rf /` or `shutdown`, disrupting the lab for everyone.
4.  **Authentication Weakness:** Relying on simple passwords from unmanaged personal devices left the lab wide open to credential stuffing and brute-force attacks.

### The Solution: "Budget-Friendly" Enterprise-Grade Security

We realized that "Trust" was the vulnerability. So, we moved to a **Zero Trust** model: **"Never Trust, Always Verify."**

By combining **Cloudflare Zero Trust** with a **Custom Jump Server (PAM)**, we built a security stack that is both production-ready and affordable for educational institutions:

* **Identity-Aware Access:** Access is no longer just about a password. It’s tied to the student's authenticated SSO email.
* **Invisible Infrastructure:** Using **Cloudflare Tunnels**, we completely closed all inbound ports. The lab is now invisible to the public internet, you can’t hack what you can’t see.
* **The Controlled Bastion:** All traffic flows through a single **Jump Server** acting as a Privileged Access Management (PAM) layer.
* **Granular RBAC:** Students are automatically routed *only* to their specific assigned environments. Their permissions are restricted, and every keystroke is logged.

Today, students simply visit `ssh.alfanlab.my.id`, authenticate, and start learning. The experience is seamless for them, but the control is absolute for the school.

### What You’ll Learn in This Write-up

This documentation is more than just a tutorial; it’s a blueprint for any SMK or small organization to implement high-level security on a limited budget. We will dive deep into:

1.  **Architecture Design:** How we integrated Cloudflare, the Jump Server, and Proxmox.
2.  **Security Assessment:** The specific vulnerabilities we closed and the risks we mitigated.
3.  **Operational Reality:** Lessons learned from real-world testing with students.
4.  **The Blueprint:** Practical steps to replicate this setup in your own institution.

Let’s dive in.
