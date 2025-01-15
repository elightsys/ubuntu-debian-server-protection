# UFW Firewall Rules for a Web Server

**Objective:**  
To set up a secure firewall on Ubuntu/Debian servers using UFW (Uncomplicated Firewall) to restrict access to critical ports and allow only necessary traffic.

---

## Step 1: Keep the System Updated

Before configuring the firewall, ensure that your system and all installed packages are up to date. Regular updates reduce the risk of exploiting known vulnerabilities.

Run the following commands:

```bash
sudo apt update && sudo apt upgrade -y
```

This command updates the package list and upgrades all installed packages to their latest versions.

---

## Step 2: Enable UFW and Set Default Policies

1. Enable UFW if it is not already enabled:

   ```bash
   sudo ufw enable
   ```

2. Set the default policies to deny all incoming connections and allow all outgoing connections:

   ```bash
   sudo ufw default deny incoming
   sudo ufw default allow outgoing
   ```

   This ensures that no unsolicited incoming traffic is allowed, while all outgoing traffic is permitted.

---

## Step 3: Allow Essential Services

1. **Allow SSH on a non-default port:**

   If you have configured SSH to use a non-default port (e.g., 2222), allow it through the firewall:

   ```bash
   sudo ufw allow 2222/tcp
   ```

   If you are using the default port (22), use:

   ```bash
   sudo ufw allow 22/tcp
   ```

2. **Allow HTTP and HTTPS traffic:**

   Since this is a web server, you need to allow HTTP (port 80) and HTTPS (port 443) traffic:

   ```bash
   sudo ufw allow 80/tcp
   sudo ufw allow 443/tcp
   ```

3. **Allow specific IPs if necessary:**

   To allow SSH access only from specific IP addresses, use:

   ```bash
   sudo ufw allow from <your-ip-address> to any port 2222
   ```

   Replace `<your-ip-address>` with your actual IP.

   Similarly, if you have a database service (e.g., MySQL) running on port 3306 and you want to restrict its access to a specific IP:

   ```bash
   sudo ufw allow from <your-ip-address> to any port 3306
   ```

4. **Allow dynamic IP ranges:**

   If your IP address changes frequently but stays within a certain range, you can allow access from a dynamic IP range using CIDR notation. For example:

   ```bash
   sudo ufw allow from 192.168.1.0/24 to any port 2222
   ```

   This command allows access from any IP in the range `192.168.1.0` to `192.168.1.255`. Similarly, for larger ranges:

   ```bash
   sudo ufw allow from 203.0.113.0/16 to any port 3306
   ```

   This allows access from any IP in the range `203.0.0.0` to `203.0.255.255`.

---

## Step 4: Consider Using a VPN for Enhanced Security

For even greater security, consider setting up a VPN (e.g., OpenVPN) and restricting access to sensitive services, such as SSH and MySQL, through the VPN. This way, only devices connected to the VPN can access those ports, greatly reducing the attack surface.

By requiring VPN access, you add an additional layer of security, ensuring that even if an attacker discovers the open port, they cannot connect without first establishing a VPN connection.

---

## Step 5: Check UFW Status and Rules

After setting up the rules, check the status of UFW to ensure everything is correctly configured:

```bash
sudo ufw status
```

The output should display the allowed ports and their status.

Example output:
```plaintext
Status: active

To                         Action      From
--                         ------      ----
2222/tcp                   ALLOW       Anywhere
80/tcp                     ALLOW       Anywhere
443/tcp                    ALLOW       Anywhere
2222/tcp (v6)              ALLOW       Anywhere (v6)
80/tcp (v6)                ALLOW       Anywhere (v6)
443/tcp (v6)               ALLOW       Anywhere (v6)
```

---

## Step 6: Additional Recommendations

1. **Limit Connection Attempts:**

   To prevent brute force attacks, you can limit the number of connection attempts to SSH:

   ```bash
   sudo ufw limit 2222/tcp
   ```

   This will allow up to 6 connections within 30 seconds from the same IP, after which further attempts will be blocked temporarily.

2. **Enable logging:**

   Enable logging to monitor blocked attempts and other firewall activities:

   ```bash
   sudo ufw logging on
   ```

   Logs can be viewed in the `/var/log/ufw.log` file.

3. **Disable UFW (if needed):**

   If you ever need to disable UFW temporarily, you can do so with:

   ```bash
   sudo ufw disable
   ```

   Remember to re-enable it afterward.

---

## Conclusion

By following these steps, you have set up a basic firewall to protect your web server from unauthorized access. Regularly review your firewall rules and update them as necessary to maintain optimal security.

Continue to the next section for further security enhancements, such as setting up Fail2ban for intrusion detection.

