# Firewall Rules Configuration

**Objective:**  
Set up firewall rules to block known malicious IP addresses, control traffic flow, and enhance server security against DDoS attacks and unauthorized access.

---

## What is a Firewall?

A firewall is a network security system that monitors and controls incoming and outgoing traffic based on predetermined rules. It serves as the first line of defense, preventing unauthorized access to your server while allowing legitimate traffic.

---

## Recommended Firewalls for Ubuntu/Debian

1. **UFW (Uncomplicated Firewall):**
   - User-friendly interface for managing iptables rules.
   - Ideal for simple setups and beginner administrators.

2. **iptables:**
   - A powerful, low-level tool for configuring Linux kernel firewall rules.
   - Offers granular control but requires advanced knowledge.

3. **CSF (ConfigServer Security & Firewall):**
   - Feature-rich firewall that integrates with multiple control panels (e.g., cPanel).
   - Includes login failure detection and advanced configurations.

4. **Firewalld:**
   - Dynamic firewall management tool suitable for complex setups.
   - Ideal for systems requiring frequent rule changes.

**Recommendation:**
- For simple and efficient management: **UFW**.
- For advanced control: **iptables** or **CSF**.

---

## Setting Up UFW

### Step 1: Install and Enable UFW

1. Install UFW:

   ```bash
   sudo apt update && sudo apt install ufw -y
   ```

2. Enable UFW:

   ```bash
   sudo ufw enable
   ```

### Step 2: Configure Basic Rules

1. **Allow SSH:**

   ```bash
   sudo ufw allow 2222/tcp
   ```
   Replace `2222` with your SSH port if it's different from the default (22).

2. **Allow Web Traffic:**

   ```bash
   sudo ufw allow 80/tcp
   sudo ufw allow 443/tcp
   ```

3. **Deny All Incoming Traffic by Default:**

   ```bash
   sudo ufw default deny incoming
   sudo ufw default allow outgoing
   ```

4. **Block a Specific IP Address:**

   ```bash
   sudo ufw deny from 203.0.113.0
   ```

5. **Allow a Specific IP Range:**

   ```bash
   sudo ufw allow from 192.168.1.0/24 to any port 3306
   ```

### Step 3: Verify and Monitor UFW Status

Check the status of UFW and its active rules:

```bash
sudo ufw status verbose
```

---

## Setting Up iptables

### Step 1: Install iptables

Most systems come with iptables pre-installed. Verify its installation:

```bash
sudo iptables --version
```

### Step 2: Configure Basic Rules

1. **Allow SSH:**

   ```bash
   sudo iptables -A INPUT -p tcp --dport 2222 -j ACCEPT
   ```

2. **Allow Web Traffic:**

   ```bash
   sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
   sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT
   ```

3. **Block a Specific IP Address:**

   ```bash
   sudo iptables -A INPUT -s 203.0.113.0 -j DROP
   ```

4. **Log Dropped Packets:**

   ```bash
   sudo iptables -A INPUT -j LOG --log-prefix "iptables-dropped: "
   ```

5. **Save Rules:**

   Persist iptables rules across reboots:

   ```bash
   sudo apt install iptables-persistent -y
   sudo netfilter-persistent save
   ```

---

## Case Study: Firewall Rules for a Web Application

### Scenario
A medium-sized e-commerce site experienced slowdowns due to suspicious traffic targeting specific ports and IPs. The server was running Ubuntu with Apache and MySQL.

### Solution
1. **Deployed UFW:**
   - Configured default deny rules for all incoming traffic.
   - Allowed specific ports for HTTP, HTTPS, and MySQL only from trusted IP ranges.

2. **Implemented Rate Limiting:**
   - Limited SSH connections to prevent brute force attacks.

3. **Monitored Logs:**
   - Used `ufw logs` and `tail -f /var/log/ufw.log` to identify patterns in malicious traffic.

### Outcome
- Server downtime reduced by 80%.
- Identified and blocked two IP ranges responsible for the attacks.
- Improved user experience with faster response times.

---

## Additional Tips for Firewall Management

1. **Enable Logging:**
   - Enable UFW logging to monitor blocked attempts:

     ```bash
     sudo ufw logging on
     ```

2. **Integrate with Fail2ban:**
   - Use Fail2ban to dynamically block IPs triggering suspicious activity.

3. **Regularly Update Rules:**
   - Periodically review and update firewall rules based on traffic patterns.

4. **Use Dynamic Firewalls for Larger Environments:**
   - Tools like Firewalld allow for dynamic changes without downtime.

---

## Conclusion

A well-configured firewall is a critical component of server security. Whether you choose UFW for simplicity or iptables/CSF for advanced setups, ensure your firewall rules are tailored to your server’s needs. Regular monitoring and updates will keep your system protected against evolving threats.

