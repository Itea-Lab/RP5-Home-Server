# Setup-Tailscale-for-Raspberry-Pi-5
A VPN is secure, flexible, and lets you access your Raspberry Pi like you're on the same local network—even from anywhere in the world.
Thre are a few good VPN options, but I highly recommend Tailscale if you want something fast and super easy to set up. Tailscale is a VPN built on WireGuard that automatically sets up a secure mesh network between your devices.  
Here’s how you can do it:  
1) First, go to https://tailscale.com/ and create an account > Sign up using Google / Github (choose your option)
2) Install Tailscale client on your device from https://tailscale.com/download  
Once complete, Tailscale icon should appear in your system tray > Right-click the icon > Log in using credentials you used to create the account  
Tailscale will then ask you to authorize device to join you tailnet VPN > Click Connect > Then you will be able to view your device on Machines dashboard.
3) If you have already enabled SSH on Raspberry Pi then move on to step 4. If you have not, then follow these steps.  
Run `sudo raspi-config` to open Raspberry Pi Configuration Tool Interface
> Select Interface Options > SSH > Yes > Ok
4) To install Tailscale client on your Raspberry Pi, use:
```
sudo apt update
sudo apt upgrade -y
curl -fsSL https://tailscale.com/install.sh | sh
```
Then start Tailscale using:
```
sudo tailscale up
```
You can find your Tailscale IPv4 address by running:
```
tailscale ip -4
```
5) Test the connection
```
ssh <pi_username>@<tailscale_ip>
```
or 
Enable SSH via Tailscale using:
```
sudo tailscale up --ssh
```
This allow you to directly SSH your Pi from a browser  
> Go to admin console > Click on Menu option > SSH to machine
It will then ask for login credential > SSH > Tailscale will open SSH session browser window

# Setup-Cockpit-for-Raspberry-Pi-5
If you want a GUI for managing you Raspberry Pi, you can you **Cockpit**, a beginner-friendly web-based graphical interface for servers
1) Visit https://cockpit-project.org/running.html and select specific installation for you OS, or you can use
```
sudo apt install cockpit -y
```
2) Enable and start the Cockpit service
```
sudo systemctl enable --now cockpit.socket
```
This command enables Cockpit to start on boot and starts it immediately
3) Try to access Cockpit web interface by open you web browser and navigate to 
```
https://<tailscale-ip>:9090
```
# (This step is optional) Enabling HTTPS for Cockpit via Tailscale
While Tailscale provides end-to-end encryption between devices using WireGuard, browsers and other tools may still flag connections as insecure if the web interface (like Cockpit) isn't served over HTTPS. This is because, despite the underlying encryption, the absence of a TLS certificate for the web service can trigger browser warnings. To address this, Tailscale offers a feature to provision TLS certificates for services within your tailnet, enabling secure HTTPS access. Here's how you can set it up  
1) Log in to your Tailscale Admin Console
> DNS Settings > Enable **Magic DNS** and **HTTPS Certificates** 
2) To obtain a TLS Certificate for your Pi, run:
```
sudo tailscale cert <hostname>.ts.net
```
Make sure to replace it with your machine's qualified domain name  
That command should have placed two files in your system:
```
/var/lib/tailscale/certs/<hostname>.ts.net.crt (the certificate)  
/var/lib/tailscale/certs/<hostname>.ts.net.key (the private key)
```
3) We then config Cockpit to use Tailscale TLS certificates
Your Raspberry pi might already had this directory
> /etc/cockpit/ws-certs.d
If not, then you can create one, using:
```
sudo mkdir -p /etc/cockpit/ws-certs.d
```
We then create a Symlink to update the files Tailscale generated
```
sudo ln -s /var/lib/tailscale/certs/<hostname>.ts.net.crt /etc/cockpit/ws-certs.d/10-tailscale.cert
sudo ln -s /var/lib/tailscale/certs/<hostname>.ts.net.key /etc/cockpit/ws-certs.d/10-tailscale.key
```
Restart Cockpit to apply changes
```
sudo systemctl restart cockpit
```
5) Test Cockit again with HTTPS
```
https://<hostname>.ts.net:9090
```
