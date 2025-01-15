# Brute Force Protection: SSH Key Authentication

**Objective:**  
Enhance server security by disabling password-based authentication and enforcing key-based authentication for SSH access.

---

## Step 1: Generate an SSH Key Pair

On your local machine, run the following command to generate a new SSH key pair:

```bash
ssh-keygen -t rsa -b 4096
```

- `-t rsa`: Specifies the key type (RSA).
- `-b 4096`: Sets the key length to 4096 bits for stronger encryption.
- You will be prompted to choose a location to save the key. The default is `~/.ssh/id_rsa`.
- Optionally, you can set a passphrase for additional security.

---

## Step 2: Copy the Public Key to the Server

Use the `ssh-copy-id` command to copy your public key to the server:

```bash
ssh-copy-id user@your-server-ip
```

Alternatively, if `ssh-copy-id` is not available, you can manually copy the key using:

```bash
cat ~/.ssh/id_rsa.pub | ssh user@your-server-ip "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

---

## Step 3: Disable Password-Based Authentication

1. Open the SSH configuration file on the server:

   ```bash
   sudo nano /etc/ssh/sshd_config
   ```

2. Find the following line and set it to `no`:

   ```bash
   PasswordAuthentication no
   ```

3. Ensure the following line is also set to `yes` to allow key-based authentication:

   ```bash
   PubkeyAuthentication yes
   ```

4. Save the file and restart the SSH service:

   ```bash
   sudo systemctl restart ssh
   ```

---

## Step 4: Verify the Setup

1. Open a new terminal window and attempt to connect to the server using your SSH key:

   ```bash
   ssh user@your-server-ip
   ```

2. Ensure that the connection works without prompting for a password. If everything is set up correctly, you should be logged in using your SSH key.

---

## Step 5: Backup and Test

- **Backup:** Before disconnecting your existing session, ensure you can connect using a new session with the key. If something goes wrong, you still have access via the old session.
- **Test:** Attempt to connect without the SSH key to confirm that password-based authentication has been successfully disabled.

---

Following these steps will significantly reduce the risk of brute force attacks on your server. Continue to the next section for additional brute force protection techniques, such as using Fail2ban and configuring a firewall.

