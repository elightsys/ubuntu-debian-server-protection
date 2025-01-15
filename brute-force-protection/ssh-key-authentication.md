# Brute Force Protection: SSH Key Authentication

**Objective:**\
Enhance server security by disabling password-based authentication and enforcing key-based authentication for SSH access.

---

## Step 1: Generate an SSH Key Pair

The `~/.ssh` directory is the recommended location for storing SSH keys on both macOS and Ubuntu/Debian systems because:

- **Default location:** Most SSH clients and software automatically look for keys in this directory.
- **Permissions:** When created with the correct permissions, it provides a secure location for private keys.

If this directory does not exist, create the \~/.ssh directory and set the correct permissions using the following commands:

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
```

Ensure that only you can read the private key file:

```bash
chmod 600 ~/.ssh/id_rsa
```

Next, generate a new SSH key pair:

```bash
ssh-keygen -t rsa -b 4096
```

- `-t rsa`: Specifies the key type (RSA).
- `-b 4096`: Sets the key length to 4096 bits for stronger encryption.
- When prompted for a file location, press `Enter` to use the default location (`~/.ssh/id_rsa`).
- If you want to secure the key with a passphrase, you can set it when prompted:

```plaintext
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
```

Leave it empty if you prefer a passwordless key.

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

2. Change the default port to enhance security (optional but recommended):

   ```bash
   Port 2222
   ```

   Using a non-default port can reduce automated attack attempts.

3. Disable password authentication by setting the following line to `no`:

   ```bash
   PasswordAuthentication no
   ```

4. Ensure the following settings are correctly configured:

   ```bash
   ChallengeResponseAuthentication no
   PubkeyAuthentication yes
   PermitRootLogin no
   UsePAM yes
   ```

   - `ChallengeResponseAuthentication no`: Disables challenge-response based authentication methods.
   - `PermitRootLogin no`: Prevents direct root login, enhancing security.
   - `UsePAM yes`: Enables PAM (Pluggable Authentication Module) for account management and session handling.

5. Save the file and restart the SSH service:

   ```bash
   sudo systemctl restart ssh
   ```

---

## Step 4: Verify the Setup

1. Open a new terminal window and attempt to connect to the server using your SSH key and the new port:

   ```bash
   ssh user@your-server-ip -p 2222
   ```

2. Ensure that the connection works without prompting for a password. If everything is set up correctly, you should be logged in using your SSH key.

---

## Step 5: Backup and Test

- **Backup:** Before disconnecting your existing session, ensure you can connect using a new session with the key. If something goes wrong, you still have access via the old session.
- **Test:** Attempt to connect without the SSH key to confirm that password-based authentication has been successfully disabled.

---

Following these steps will significantly reduce the risk of brute force attacks on your server. Continue to the next section for additional brute force protection techniques, such as using Fail2ban and configuring a firewall.



