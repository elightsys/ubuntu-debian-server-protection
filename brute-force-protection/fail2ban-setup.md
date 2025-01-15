# Fail2ban Configuration for a Web Server

**Objective:**  
Automatically ban IP addresses that exhibit suspicious login behavior after several failed attempts, enhancing server security against brute force attacks.

---

## Step 1: Install Fail2ban

On Ubuntu/Debian servers, Fail2ban can be installed using the following command:

```bash
sudo apt update && sudo apt install fail2ban -y
```

This installs the Fail2ban package and its dependencies.

---

## Step 2: Basic Fail2ban Configuration

1. **Create a local jail configuration file:**

   ```bash
   sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
   ```

   The `jail.local` file overrides the default settings in `jail.conf`, ensuring your custom settings remain intact during updates.

2. **Edit the `jail.local` file:**

   Open the file using your preferred text editor:

   ```bash
   sudo nano /etc/fail2ban/jail.local
   ```

   Update the following settings:

   ```ini
   [DEFAULT]
   bantime  = 10m
   findtime = 10m
   maxretry = 5
   ignoreip = 127.0.0.1/8 192.168.0.0/24
   
   [sshd]
   enabled = true
   port    = 2222
   logpath = /var/log/auth.log
   backend = systemd
   
   [vsftpd]
   enabled = true
   port    = 21
   logpath = /var/log/vsftpd.log

   [mysqld-auth]
   enabled = true
   port    = 3306
   logpath = /var/log/mysql.log

   [ufw]
   enabled = true
   logpath = /var/log/ufw.log
   ```

   - `bantime`: The duration for which an IP will be banned. Examples:
     - `10m` - 10 minutes
     - `1h` - 1 hour
     - `1d` - 1 day
     - `1w` - 1 week
     - `-1` - permanent ban
   - `findtime`: The time window within which the failed login attempts are counted.
   - `maxretry`: The maximum number of failed login attempts before an IP is banned.
   - `ignoreip`: Specifies IP addresses or ranges to be ignored by Fail2ban (e.g., localhost and internal network).
   - `enabled`: Enables Fail2ban for the specified service.
   - `port`: The port used by the service.
   - `logpath`: Path to the log file where Fail2ban monitors for suspicious activity.

3. **Save and close the file.**

---

## Step 3: Set Up Email Notifications

Fail2ban can be configured to send email alerts when an IP is banned. To enable email notifications:

1. **Install an SMTP client:**

   ```bash
   sudo apt install sendmail -y
   ```

2. **Configure Sendmail:**

   To configure Sendmail with a username and password for an external mail server (e.g., Gmail):

   - Create or edit the `/etc/mail/authinfo` file:

     ```bash
     sudo nano /etc/mail/authinfo
     ```
   
     Add the following content:
     
     ```plaintext
     AuthInfo:smtp.yourmailserver.com "U:root" "I:your-email@example.com" "P:your-email-password"
     ```
   
   - Run the following command to hash the file:
     
     ```bash
     sudo makemap hash /etc/mail/authinfo < /etc/mail/authinfo
     ```
   
   - Edit `/etc/mail/sendmail.mc` to include the SMTP relay:
     
     ```plaintext
     define(`SMART_HOST', `smtp.yourmailserver.com')
     FEATURE(`authinfo')
     ```
   
   - Rebuild the Sendmail configuration:
     
     ```bash
     sudo m4 /etc/mail/sendmail.mc > /etc/mail/sendmail.cf
     sudo systemctl restart sendmail
     ```

3. **Update the `jail.local` file:**

   Add or update the following lines under the `[DEFAULT]` section:

   ```ini
   destemail = your-email@example.com
   sender = fail2ban@yourdomain.com
   mta = sendmail
   action = %(action_mwl)s
   ```

   - `destemail`: The email address where notifications will be sent.
   - `sender`: The sender's email address.
   - `mta`: The mail transfer agent (set to `sendmail`).
   - `action`: Specifies the action to take when banning an IP. The `%(action_mwl)s` option sends an email with the log and whois information.

4. **Test Sendmail:**

   To verify that Sendmail is working correctly, run:

   ```bash
   echo "Test email from Fail2ban" | mail -s "Test Fail2ban" your-email@example.com
   ```

   Check your inbox to ensure the email was received.

---

## Step 4: Restart Fail2ban

After making the changes, restart the Fail2ban service to apply the new configuration:

```bash
sudo systemctl restart fail2ban
```

Check the status to ensure it is running:

```bash
sudo systemctl status fail2ban
```

---

## Step 5: Monitor Banned IPs

To view the list of currently banned IP addresses, use the following command:

```bash
sudo fail2ban-client status sshd
```

The output will show the number of currently banned IPs and their details.

To unban an IP address:

```bash
sudo fail2ban-client set sshd unbanip <banned-ip>
```

To manually ban an IP address:

```bash
sudo fail2ban-client set sshd banip <ip-to-ban>
```

---

## Step 6: Configure the [recidive] Jail

The `recidive` jail in Fail2ban is used to permanently or temporarily ban IPs that have been banned multiple times. This is particularly useful for preventing repeated attacks from persistent offenders.

1. **Enable the `recidive` jail:**

   Add the following to your `jail.local` file:

   ```ini
   [recidive]
   enabled  = true
   logpath  = /var/log/fail2ban.log
   bantime  = 1w
   findtime = 1d
   maxretry = 3
   ```

   - `bantime`: Sets the ban duration to 1 week.
   - `findtime`: Checks for repeated bans within 1 day.
   - `maxretry`: Bans the IP after 3 repeated bans.

2. **Restart Fail2ban:**

   ```bash
   sudo systemctl restart fail2ban
   ```

---

## Additional Recommendations

1. **Adjust ban time and retry limits:**  
   Depending on your server's security needs, you may want to increase the `bantime` or decrease the `maxretry` value for stricter protection.

2. **Protect other services:**  
   Fail2ban can be configured to protect other services, such as Nginx, Apache, and MySQL. Add additional jail configurations as needed.

3. **Regularly check logs:**  
   Regularly review Fail2ban logs to monitor unusual activity:

   ```bash
   sudo tail -f /var/log/fail2ban.log
   ```

---

## Conclusion

By following this guide, you have configured Fail2ban to automatically ban IPs exhibiting suspicious behavior, send email alerts, and handle persistent offenders. This setup greatly enhances your server's protection against brute force attacks and unauthorized access.

Continue to the next section for additional hardening techniques, such as using two-factor authentication (2FA) and intrusion detection systems.

