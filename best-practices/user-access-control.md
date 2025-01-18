# User Access Control Guide

**Objective:**  
Manage user access rights carefully and limit administrative privileges to necessary users only. This guide provides steps for configuring user permissions, enhancing security, and monitoring access on Linux servers.

---

## Why User Access Control is Important

1. **Minimizes Risk:** Restricting privileges reduces the risk of accidental or intentional misuse.
2. **Improves Accountability:** User-specific accounts enable tracking of actions.
3. **Enhances Security:** Limiting access to essential users minimizes attack vectors.
4. **Regulatory Compliance:** Many security standards require controlled access to sensitive systems.

---

## Step 1: Creating and Managing Users

1. **Add a New User:**

   ```bash
   sudo adduser username
   ```

   - Replace `username` with the desired user name.
   - Follow the prompts to set a password and user details.

2. **Assign a User to a Group:**

   ```bash
   sudo usermod -aG groupname username
   ```

   - Replace `groupname` with an existing group (e.g., `sudo`, `www-data`).

3. **List Users:**

   View all users on the system:

   ```bash
   cut -d: -f1 /etc/passwd
   ```

4. **List Groups:**

   View all groups:

   ```bash
   cat /etc/group
   ```

---

## Step 2: Restricting Administrative Privileges

1. **Remove a User from the `sudo` Group:**

   ```bash
   sudo deluser username sudo
   ```

2. **Grant Temporary Privileges:**

   Use the `sudo` command with expiration:

   ```bash
   sudo -u username command
   ```

3. **Limit Root Access:**

   Disable root login via SSH by editing `/etc/ssh/sshd_config`:

   ```plaintext
   PermitRootLogin no
   ```

   Restart SSH to apply changes:

   ```bash
   sudo systemctl restart sshd
   ```

---

## Step 3: Monitoring User Activity

1. **Check Login History:**

   View recent login attempts:

   ```bash
   last
   ```

2. **Monitor Active Sessions:**

   List currently logged-in users:

   ```bash
   who
   ```

3. **Track User Commands:**

   Review command history:

   ```bash
   cat /home/username/.bash_history
   ```

4. **Enable Auditing:**

   Install and configure `auditd` for detailed logging:

   ```bash
   sudo apt install auditd -y
   sudo systemctl enable auditd
   ```

   Example audit rule:

   ```bash
   sudo auditctl -a always,exit -F arch=b64 -S execve -k commands
   ```

---

## Step 4: Using Access Control Tools

1. **Set User Password Expiry Policies:**

   Update password expiry for a user:

   ```bash
   sudo chage -E 2025-01-01 username
   ```

   Check password expiry:

   ```bash
   sudo chage -l username
   ```

2. **Enable Two-Factor Authentication (2FA):**

   Install `google-authenticator`:

   ```bash
   sudo apt install libpam-google-authenticator -y
   ```

   Configure 2FA for a user:

   ```bash
   google-authenticator
   ```

3. **Limit SSH Access:**

   Allow specific users to log in via SSH by editing `/etc/ssh/sshd_config`:

   ```plaintext
   AllowUsers username1 username2
   ```

   Restart SSH:

   ```bash
   sudo systemctl restart sshd
   ```

---

## Step 5: Best Practices for User Access Control

1. **Principle of Least Privilege:**
   - Grant users only the permissions they need to perform their tasks.

2. **Regularly Audit User Accounts:**
   - Disable accounts of inactive users:

     ```bash
     sudo usermod -L username
     ```

3. **Implement Access Levels:**
   - Use groups to define access levels (e.g., admin, developer, auditor).

4. **Rotate Passwords Regularly:**
   - Enforce password changes every 90 days.

5. **Use Centralized Authentication:**
   - Implement LDAP or Active Directory for larger environments.

---

## Case Study: User Access Control for a Web Server

### Scenario
A company’s web server had multiple administrators with root access, leading to confusion and potential security risks.

### Solution
1. **Defined Roles and Permissions:**
   - Created distinct user groups (e.g., `admins`, `developers`, `auditors`).

2. **Implemented 2FA:**
   - Enabled 2FA for all administrative accounts.

3. **Disabled Root Login:**
   - Restricted SSH access to non-root users.

4. **Audited User Activity:**
   - Used `auditd` to track critical commands and login attempts.

### Outcome
- Improved accountability by identifying who performed specific actions.
- Reduced security risks by limiting administrative access.
- Streamlined user management with clearly defined roles.

---

## Conclusion

Effective user access control is essential for securing server environments. By carefully managing permissions, monitoring activity, and following best practices, you can enhance accountability and minimize risks. Regular audits and adherence to the principle of least privilege will ensure robust security.

