# Reverse Proxy Configuration

**Objective:**  
To use a reverse proxy (e.g., Nginx or HAProxy) to distribute incoming requests, filter malicious traffic, enhance security, and improve server performance.

---

## What is a Reverse Proxy?

A reverse proxy acts as an intermediary between clients (e.g., browsers) and backend servers. When a client sends a request, the reverse proxy forwards it to the appropriate backend server and returns the response to the client. This setup provides several benefits:

- **Load Balancing:** Distributes incoming traffic across multiple backend servers to prevent overload.
- **DDoS Mitigation:** Filters malicious traffic and limits excessive requests.
- **Security:** Masks the backend server's IP address, adds SSL/TLS encryption, and blocks unwanted requests.
- **Caching:** Stores frequently requested resources to reduce backend load and improve response times.
- **Centralized Monitoring:** Provides a single point to log and analyze traffic.

---

## Reverse Proxy Setup Using Nginx

### Step 1: Install Nginx

1. Update the package list and install Nginx:

   ```bash
   sudo apt update && sudo apt install nginx -y
   ```

2. Ensure Nginx is running:

   ```bash
   sudo systemctl status nginx
   ```

### Step 2: Configure Nginx as a Reverse Proxy

1. Open the Nginx configuration file for your site:

   ```bash
   sudo nano /etc/nginx/sites-available/default
   ```

2. Update the configuration with reverse proxy settings:

   ```nginx
   server {
       listen 80;
       server_name yourdomain.com;

       location / {
           proxy_pass http://backend-server-ip:backend-port;
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
       }
   }
   ```

   Explanation:
   - `proxy_pass`: Forwards requests to the backend server.
   - `proxy_set_header`: Ensures client information is forwarded to the backend.

3. Test the Nginx configuration:

   ```bash
   sudo nginx -t
   ```

4. Reload Nginx to apply changes:

   ```bash
   sudo systemctl reload nginx
   ```

---

## Reverse Proxy Setup Using HAProxy

### Step 1: Install HAProxy

1. Install HAProxy:

   ```bash
   sudo apt update && sudo apt install haproxy -y
   ```

2. Check the status of HAProxy:

   ```bash
   sudo systemctl status haproxy
   ```

### Step 2: Configure HAProxy as a Reverse Proxy

1. Edit the HAProxy configuration file:

   ```bash
   sudo nano /etc/haproxy/haproxy.cfg
   ```

2. Add the following configuration:

   ```haproxy
   frontend http_front
       bind *:80
       default_backend servers

   backend servers
       balance roundrobin
       server server1 backend-server-ip:backend-port check
       server server2 backend-server-ip2:backend-port check
   ```

   Explanation:
   - `frontend`: Defines how incoming requests are handled.
   - `backend`: Lists backend servers and specifies the load balancing method.
   - `check`: Ensures HAProxy monitors server health.

3. Test the HAProxy configuration:

   ```bash
   sudo haproxy -c -f /etc/haproxy/haproxy.cfg
   ```

4. Restart HAProxy to apply changes:

   ```bash
   sudo systemctl restart haproxy
   ```

---

## Case Study: Using Nginx for DDoS Mitigation

### Scenario
A small e-commerce website experienced frequent downtime due to malicious traffic spikes. The website used a single backend server that couldn’t handle the load during attacks.

### Solution
1. **Implemented Nginx as a Reverse Proxy:**
   - Deployed Nginx in front of the backend server.
   - Configured rate limiting to prevent excessive requests from a single IP.

2. **Enabled SSL/TLS Encryption:**
   - Used Let’s Encrypt for free SSL certificates.

3. **Load Balancing:**
   - Distributed traffic between two backend servers using Nginx.

4. **Outcome:**
   - Reduced downtime by 90%.
   - Improved user experience with faster response times.
   - Blocked malicious traffic automatically using rate limiting and Fail2ban.

---

## Additional Tips for a Secure Reverse Proxy Setup

1. **Enable HTTPS:**
   - Secure communication by adding SSL/TLS certificates.

   Example for Nginx:

   ```nginx
   server {
       listen 443 ssl;
       server_name yourdomain.com;

       ssl_certificate /etc/ssl/certs/yourdomain.com.crt;
       ssl_certificate_key /etc/ssl/private/yourdomain.com.key;

       location / {
           proxy_pass http://backend-server-ip;
       }
   }
   ```

2. **Use a Web Application Firewall (WAF):**
   - Tools like ModSecurity can block malicious traffic.

3. **Monitor Traffic:**
   - Use tools like Netdata or Grafana for real-time monitoring.

4. **Combine with CDN:**
   - Services like Cloudflare can provide an additional layer of DDoS protection.

---

## Conclusion

A reverse proxy is a powerful tool for enhancing server performance and security. By deploying Nginx or HAProxy as a reverse proxy, you can mitigate DDoS attacks, distribute traffic, and centralize monitoring. Regularly review and update your configurations to adapt to changing traffic patterns and threats.

