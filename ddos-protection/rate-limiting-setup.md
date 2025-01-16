# DDoS Protection: Rate Limiting

**Objective:**  
Limit the number of requests allowed from a single IP address within a specified time period to prevent Distributed Denial of Service (DDoS) attacks from overwhelming the server.

---

## Step 1: Set Up Nginx for Rate Limiting

Nginx provides built-in support for rate limiting, which can be configured using the `limit_req` module. This helps mitigate DDoS attacks by limiting the number of requests from a single IP address.

1. **Open the Nginx configuration file:**

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

2. **Define a rate limiting zone:**

   Add the following line inside the `http` block:

   ```nginx
   http {
       ...
       limit_req_zone $binary_remote_addr zone=one:10m rate=10r/s;
       ...
   }
   ```

   Explanation:
   - `$binary_remote_addr`: Represents the IP address of the client.
   - `zone=one:10m`: Creates a shared memory zone named `one` with 10MB of memory, which can store about 160,000 unique IP addresses.
   - `rate=10r/s`: Limits requests to 10 requests per second per IP address.

3. **Apply rate limiting to a specific location:**

   Inside the appropriate `server` block, add:

   ```nginx
   server {
       ...
       location / {
           limit_req zone=one burst=20 nodelay;
           ...
       }
       ...
   }
   ```

   Explanation:
   - `limit_req zone=one`: Applies the rate limiting zone named `one`.
   - `burst=20`: Allows up to 20 requests to be queued before starting to drop excess requests.
   - `nodelay`: Ensures that queued requests are processed immediately without delay.

4. **Test the Nginx configuration:**

   Before reloading Nginx, test the configuration for syntax errors:

   ```bash
   sudo nginx -t
   ```

5. **Reload Nginx to apply the changes:**

   ```bash
   sudo systemctl reload nginx
   ```

---

## Step 1 (Alternative): Set Up Apache2 for Rate Limiting

If you are using Apache2 instead of Nginx, you can implement rate limiting using the `mod_ratelimit` module.

1. **Enable the `mod_ratelimit` module:**

   ```bash
   sudo a2enmod ratelimit
   sudo systemctl restart apache2
   ```

2. **Apply rate limiting to a specific directory or virtual host:**

   Edit the relevant virtual host configuration file or `.htaccess` file:

   ```apache
   <VirtualHost *:80>
       ServerName yourdomain.com
       DocumentRoot /var/www/html

       <Location />
           SetOutputFilter RATE_LIMIT
           SetEnv rate-limit 1024
       </Location>
   </VirtualHost>
   ```

   Explanation:
   - `SetOutputFilter RATE_LIMIT`: Enables rate limiting for the specified location.
   - `SetEnv rate-limit 1024`: Limits the data transfer rate to 1024 bytes per second.

3. **Restart Apache2:**

   ```bash
   sudo systemctl restart apache2
   ```

---

## Step 2: Set Up UFW Rate Limiting

UFW (Uncomplicated Firewall) can be used to limit connections on specific ports, such as SSH, to prevent brute force or DDoS attacks.

1. **Enable UFW rate limiting on SSH:**

   ```bash
   sudo ufw limit ssh
   ```

   This command limits incoming SSH connections to 6 per 30 seconds from a single IP address.

2. **Verify UFW status:**

   ```bash
   sudo ufw status
   ```

   You should see an entry similar to:

   ```plaintext
   To                         Action      From
   --                         ------      ----
   22/tcp                     LIMIT       Anywhere
   ```

---

## Step 3: Monitor and Test Rate Limiting

1. **Monitor Nginx or Apache logs:**

   To check if rate limiting is working, monitor the server access logs:

   - **Nginx:**
     ```bash
     sudo tail -f /var/log/nginx/access.log
     ```
   - **Apache:**
     ```bash
     sudo tail -f /var/log/apache2/access.log
     ```

   Look for HTTP status code `503` in Nginx or slow response behavior in Apache, indicating that requests were dropped or delayed due to rate limiting.

2. **Test rate limiting:**

   Use a tool like `ab` (Apache Benchmark) or `curl` to simulate multiple requests:

   ```bash
   ab -n 100 -c 20 http://your-server-ip/
   ```

   This command sends 100 requests with a concurrency level of 20. If rate limiting is correctly configured, some requests should be rejected.

---

## Additional Recommendations

1. **Adjust rate limits:**
   Depending on your server's capacity and expected traffic, you may need to fine-tune the rate and burst values.

2. **Use Cloudflare or another CDN:**
   For additional DDoS protection, consider using a content delivery network (CDN) like Cloudflare, which provides advanced DDoS mitigation features.

3. **Combine with Fail2ban:**
   Integrate Fail2ban to detect and ban IPs that trigger excessive 503 errors due to rate limiting.

---

## Which Web Server is More Secure: Nginx or Apache?

Both Nginx and Apache are widely used and highly secure web servers when properly configured. However, there are some key differences:

1. **Performance under load:**
   - Nginx is generally more efficient at handling a large number of concurrent connections due to its event-driven architecture.
   - Apache uses a process-driven model, which can consume more resources under heavy load.

2. **Ease of configuration:**
   - Apache offers more flexibility with `.htaccess` files, allowing directory-level configuration.
   - Nginx requires central configuration, which can be more secure but less flexible.

3. **Security features:**
   - Both servers offer robust security features, but Nginx is often preferred for high-performance environments with DDoS mitigation needs.

**Recommendation:**
   - If your priority is high performance and scalability, **Nginx** may be a better choice.
   - If you need flexibility and ease of use, especially for smaller or more complex setups, **Apache** is a solid option.

---

## Conclusion

By setting up rate limiting with Nginx, Apache, and UFW, you can significantly reduce the risk of DDoS attacks affecting your server's availability. Regularly monitor and adjust the rate limits based on your server's usage patterns to maintain optimal performance.

Continue to the next section for more advanced DDoS mitigation techniques, such as reverse proxy configurations.

