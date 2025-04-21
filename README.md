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
