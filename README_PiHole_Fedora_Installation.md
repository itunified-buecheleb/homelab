
# Pi-hole Installation on Fedora with Unbound and Firewall Configuration

This guide walks you through the installation process of **Pi-hole** on a Fedora-based system. It also covers how to run Pi-hole under its own user, configure **Unbound** as the DNS resolver, and set up **firewalld** to allow traffic to Pi-hole on port **443**.

## Prerequisites

- A fresh installation of Fedora 34+.
- Root (sudo) access to the system.

## Step 1: System Preparation

1. **Update the system**:

   Open a terminal and ensure your system is up to date:

   ```bash
   sudo dnf update -y
   sudo dnf upgrade -y
   ```

2. **Install necessary dependencies**:

   Install some essential packages required by Pi-hole:

   ```bash
   sudo dnf install -y curl wget sudo
   ```

3. **Install Unbound (Optional, for local DNS resolution)**:

   If you want to use **Unbound** for local DNS resolution, install it using:

   ```bash
   sudo dnf install unbound -y
   ```

## Step 2: Create a Separate User for Pi-hole

Pi-hole will run under its own user to isolate it from other system processes. The Pi-hole installation script will create the `pihole` user automatically.

To check if the user `pihole` exists, you can use:

```bash
id pihole
```

If not, the Pi-hole installer will create it automatically during the installation process.

## Step 3: Install Pi-hole

1. **Run the Pi-hole installation script**:

   Download and execute the Pi-hole installation script from the official repository:

   ```bash
   curl -sSL https://install.pi-hole.net | bash
   ```

   The script will handle the installation of Pi-hole and all its dependencies.

2. **Configure Pi-hole during installation**:

   - During the installation process, you will be prompted to choose the DNS provider (e.g., Google DNS, Cloudflare, OpenDNS).
   - The installation script will also ask you to configure the blocking lists, web interface settings, and more.

## Step 4: Configure Pi-hole to Run on Port 443

To secure Pi-hole and make it run over HTTPS on port 443, follow these steps:

1. **Edit the lighttpd configuration**:

   Open the Pi-hole web server configuration file for **lighttpd**:

   ```bash
   sudo vi /etc/lighttpd/lighttpd.conf
   ```

2. **Change the port to 443**:

   Look for the following line:

   ```bash
   server.modules += ( "mod_access" )
   ```

   Add the following configuration to bind Pi-hole to port 443:

   ```bash
   server.modules += ( "mod_ssl" )
   server.modules += ( "mod_rewrite" )

   $SERVER["socket"] == ":443" {
       ssl.engine = "enable"
       ssl.pemfile = "/etc/ssl/certs/pihole.pem"
       ssl.certfile = "/etc/ssl/certs/pihole.pem"
       ssl.keyfile = "/etc/ssl/private/pihole.key"
   }
   ```

   Save and close the file.

3. **Generate SSL certificates for Pi-hole**:

   If you don't have SSL certificates, you can create self-signed certificates for Pi-hole:

   ```bash
   sudo openssl req -new -x509 -days 365 -nodes -out /etc/ssl/certs/pihole.pem -keyout /etc/ssl/private/pihole.key
   ```

4. **Restart lighttpd**:

   Restart the Pi-hole web server to apply the changes:

   ```bash
   sudo systemctl restart lighttpd
   ```

## Step 5: Configure Firewall (firewalld)

1. **Install firewalld** (if not installed already):

   ```bash
   sudo dnf install firewalld -y
   ```

2. **Start and enable firewalld**:

   ```bash
   sudo systemctl start firewalld
   sudo systemctl enable firewalld
   ```

3. **Allow traffic on port 443**:

   To ensure Pi-hole is accessible over HTTPS, open port 443:

   ```bash
   sudo firewall-cmd --zone=public --add-port=443/tcp --permanent
   sudo firewall-cmd --reload
   ```

## Step 6: Access the Pi-hole Web Interface

Once the installation is complete, Pi-hole can be managed through its web interface:

1. Open your web browser and go to:

   ```
   https://<your-server-ip>/admin
   ```

   Replace `<your-server-ip>` with the IP address of your Fedora server.

2. Log in using the password set during the installation process.

## Step 7: Pi-hole Service Management

Pi-hole is set up to run as a service under the `pihole` user. 

1. **Check the status of the Pi-hole service**:

   You can verify if the Pi-hole service is running by executing:

   ```bash
   sudo systemctl status pihole-FTL
   ```

2. **Start or stop Pi-hole service**:

   - To start the Pi-hole service:

     ```bash
     sudo systemctl start pihole-FTL
     ```

   - To stop the Pi-hole service:

     ```bash
     sudo systemctl stop pihole-FTL
     ```

3. **Enable Pi-hole to start on boot**:

   To ensure Pi-hole starts automatically when the system boots, run:

   ```bash
   sudo systemctl enable pihole-FTL
   ```

4. **View Pi-hole logs**:

   You can monitor Pi-hole logs using the following command:

   ```bash
   sudo tail -f /var/log/pihole.log
   ```

## Additional Configuration

- You can customize Pi-hole by adding more blocklists via the web interface.
- For advanced configuration, consult the [Pi-hole documentation](https://docs.pi-hole.net/).

Enjoy your ad-blocking experience with Pi-hole on Fedora!

