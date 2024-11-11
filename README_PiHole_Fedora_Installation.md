
# Pi-hole Installation on Fedora

This guide walks you through the installation process of Pi-hole on a Fedora-based system. Pi-hole will run under its own user, `pihole`, for security and management reasons.

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

## Step 2: Create a separate user for Pi-hole

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

## Step 4: Access the Pi-hole Web Interface

Once the installation is complete, Pi-hole can be managed through its web interface:

1. Open your web browser and go to:

   ```
   http://<your-server-ip>/admin
   ```

   Replace `<your-server-ip>` with the IP address of your Fedora server.

2. Log in using the password set during the installation process.

## Step 5: Pi-hole Service Management

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
