# Brute Force Protection

This section provides techniques and configurations to protect Ubuntu and Debian servers from Brute Force attacks. Brute Force attacks attempt to gain unauthorized access by systematically trying different passwords or credentials.

## Applied Techniques

1. **SSH Key Authentication**  
   - Disable password-based authentication and enforce key-based authentication for SSH access.  
   - [Step-by-step guide for setting up SSH key authentication](ssh-key-authentication.md)

2. **Fail2ban Configuration**  
   - Automatically ban IP addresses that exhibit suspicious login behavior after several failed attempts.  
   - [Guide to configuring Fail2ban](fail2ban-setup.md)

3. **UFW Firewall Rules**  
   - Restrict access to critical ports (e.g., SSH) by setting up firewall rules.  
   - [How to set up UFW rules](ufw-setup.md)
