# Self-Hosted Personal Cloud: Google Drive Alternative on Headless Debian 13

A step-by-step implementation guide to deploying a private, highly secure, and automated cloud storage platform (Nextcloud AIO) on a fresh, headless (CLI-only) installation of Debian 13 (Trixie). 

This architecture leverages Tailscale for secure remote access without exposing ports to the public internet, Docker for service isolation, and Portainer for web-based container management.

---

## Architecture Blueprint

* Network Layer: Tailscale (WireGuard-based zero-configuration mesh VPN)
* Runtime Layer: Docker Engine (Stable release direct from upstream)
* Management Layer: Portainer Community Edition (LTS)
* Application Layer: Nextcloud All-in-One (AIO) Ecosystem

---

## Deployment Guide

### Phase 1: Establish Secure Remote Access (Tailscale)

Instead of modifying router firewalls or risking exposure to the public internet, Tailscale establishes an encrypted virtual network tunnel directly to your server.

1. Download and execute the official installation script:
   => Run command: curl -fsSL https://tailscale.com/install.sh | sh
   * Why: This automatically detects the host architecture, configures the Tailscale repository sources, imports package keys, and installs the network engine.

2. Authenticate the headless server:
   => Run command: sudo tailscale up
   * Action: The terminal will output a unique login URL. Copy this link, open it in your everyday laptop or phone's browser, authenticate with your provider, and authorize the server.
   * Result: Your server is assigned a permanent, private IP address beginning with 100.x.x.x (check using 'tailscale ip -4').

3. Client Connection:
   Install Tailscale on your daily smartphone or laptop and sign into the exact same account. Your devices can now securely communicate with the server from anywhere in the world.

---

### Phase 2: Deploy Container Infrastructure (Docker Engine)

Nextcloud and Portainer are containerized services. We will configure Docker using official upstream distribution keys for maximum stability on Debian 13.

1. Install system prerequisites and configure package signatures:
   => Run command: sudo apt update && sudo apt install -y ca-certificates curl gnupg
   => Run command: sudo install -m 0755 -d /etc/apt/keyrings
   => Run command: sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
   => Run command: sudo chmod a+r /etc/apt/keyrings/docker.asc

2. Map the official stable Docker source repository:
   => Run command: echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

3. Complete the Docker ecosystem installation:
   => Run command: sudo apt update
   => Run command: sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

4. Enable the service engine to start on machine boots:
   => Run command: sudo systemctl enable --now docker

---

### Phase 3: Launch Management Interface (Portainer)

Portainer replaces the terminal for everyday container management by offering a secure visual web console.

1. Create a persistent named disk volume:
   => Run command: docker volume create portainer_data
   * Why: Docker containers are inherently ephemeral. Creating an external volume guarantees your administration credentials and environment state persist through system reboots or container updates.

2. Run the Portainer container instance:
   => Run command:
   sudo docker run -d \
     -p 8000:8000 \
     -p 9443:9443 \
     --name portainer \
     --restart=always \
     -v /var/run/docker.sock:/var/run/docker.sock \
     -v portainer_data:/data \
     portainer/portainer-ce:lts

   * Key Parameter Explanation: The docker.sock flag links the host's Docker engine interface directly to Portainer, giving the UI permission to create, monitor, and scale apps on your Debian host.

3. Initialize Admin Access:
   On your client device browser, navigate to: https://<YOUR_SERVER_TAILSCALE_100_IP>:9443
   * Note: Bypass the browser self-signed SSL warning, set up your primary user account, and choose "Get Started" to link to the local engine context.

---

### Phase 4: Deploy Nextcloud All-in-One (AIO)

Nextcloud AIO encapsulates the primary cloud suite, automatic storage configurations, Redis processing caches, database parameters, and collaborative office toolsets inside a managed deployment pipeline.

1. Inside Portainer, navigate to Local -> Stacks -> Add stack.
2. Set the stack identity name to 'nextcloud-aio'.
3. In the Web editor field, paste the following composition text:

---
version: "3.8"

services:
  nextcloud-mastercontainer:
    image: ghcr.io/nextcloud-releases/all-in-one:latest
    container_name: nextcloud-aio-mastercontainer
    restart: always
    ports:
      - "80:80"
      - "8080:8080"
      - "8443:8443"
    environment:
      - APACHE_IP_BINDING=0.0.0.0
    volumes:
      - nextcloud_aio_mastercontainer:/mnt/docker-aio-config
      - /var/run/docker.sock:/var/run/docker.sock:ro

volumes:
  nextcloud_aio_mastercontainer:
    name: nextcloud_aio_mastercontainer
---

4. Click Deploy the stack.

---

### Phase 5: The Final Onboarding Wizard

1. Open your client browser and access the AIO configuration instance:
   👉 https://<YOUR_SERVER_TAILSCALE_100_IP>:8080

2. Crucial: Securely save the unique master password displayed on screen, and log into the AIO workspace.
3. Network Configuration: Copy your server's custom MagicDNS Domain Name from your Tailscale Web Console (e.g., your-hostname.xxxx.ts.net) and paste it into the domain lookup input.
4. Select your preferred application add-ons (Nextcloud Office, Mobile integration, Memories gallery, etc.).
5. Select Download and Start Containers. 
6. Once all services display green status markers, the system will output your primary Nextcloud Admin account login details.

---

## Client Application Integration

Download the official Nextcloud application clients for iOS, Android, Windows, macOS, or Linux. Sign in using your unique Tailscale MagicDNS address and your newly generated cloud credentials to activate automatic background camera uploads, automated folder sync loops, and complete file ownership.
