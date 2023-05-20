# Raspberry Pi Wi-Fi Hotspot Setup

This repository provides instructions on setting up your Raspberry Pi as a Wi-Fi hotspot that uses the internet from your existing Wi-Fi network, similar to a range extender. By following these steps, you can share your internet connection with other devices by connecting them to your Raspberry Pi's Wi-Fi hotspot.

## Prerequisites

- Raspberry Pi with Raspbian (or a compatible operating system) installed.
- Internet connection available through Ethernet or another Wi-Fi network on the Raspberry Pi.

## Additional files

- `/etc/dhcpcd.conf`: Contains the configuration for the static IP address of the Wi-Fi interface.

- `/etc/dnsmasq.conf`: Contains the configuration for the DHCP server.

- `/etc/dnsmasq.conf.orig`: This file is a backup of the original dnsmasq.conf file created during the setup process.

- `/etc/hostapd/hostapd.conf`: Contains the configuration for the Wi-Fi access point.

## Steps

1. Update your Raspberry Pi:

   ```shell
   sudo apt update
   sudo apt upgrade
   ```

2. Install the necessary software packages:

   ```shell
   sudo apt install hostapd dnsmasq
   ```

   Install `hostapd` (if it doesn't exists)

   ```shell
   sudo apt install hostapd
   ```

   This command will install the `hostapd` package, which is required for setting up the Wi-Fi hotspot.

3. Configure a static IP address for the Wi-Fi interface:

   Open the `/etc/dhcpcd.conf` file:

   ```shell
   sudo nano /etc/dhcpcd.conf
   ```

   Add the following lines at the end of the file:

   ```java
   interface wlan0
   static ip_address=192.168.4.1/24
   nohook wpa_supplicant
   ```

   Save and exit the file.

4. Configure the DHCP server:

   Create a backup of the default `dnsmasq.conf` file (if it exists):

   ```shell
   sudo mv /etc/dnsmasq.conf /etc/dnsmasq.conf.orig
   ```

   Create a new `dnsmasq.conf` file:

   ```shell
   sudo nano /etc/dnsmasq.conf
   ```

   Add the following lines to the file:

   ```go
   interface=wlan0
   dhcp-range=192.168.4.2,192.168.4.20,255.255.255.0,24h
   ```

5. Configure the access point:

   Create and edit the `hostapd` configuration file (if it exists):

   ```shell
   sudo nano /etc/hostapd/hostapd.conf
   ```

   Add the following lines to the file:

   ```makefile
   interface=wlan0
   ssid=YourSSID
   hw_mode=g
   channel=7
   wmm_enabled=0
   macaddr_acl=0
   auth_algs=1
   ignore_broadcast_ssid=0
   wpa=2
   wpa_passphrase=YourPassword
   wpa_key_mgmt=WPA-PSK
   wpa_pairwise=TKIP
   rsn_pairwise=CCMP
   ```

   Save and exit the file.

   <!-- If the `/etc/hostapd/hostapd.conf` file does not exist -->

   In some Raspberry Pi configurations, the `hostapd` service is started using a different method that doesn't involve modifying the `/etc/default/hostapd` file. In such cases, the `hostapd` service can still be started with the correct configuration file (`/etc/hostapd/hostapd.conf`) by specifying it directly.

   You can proceed with the remaining steps without modifying the `/etc/default/hostapd` file. Just make sure to start the hostapd service using the appropriate command, specifying the configuration file explicitly:

   ```shell
   sudo hostapd /etc/hostapd/hostapd.conf
   ```

   This command will start the hostapd service with the specified configuration file.

6. Configure the `hostapd` startup file:

   Open the `hostapd` default configuration file:

   ```shell
   sudo nano /etc/default/hostapd
   ```

   Change the `#DAEMON_CONF=""` line to:

   ```makefile
   DAEMON_CONF="/etc/hostapd/hostapd.conf"
   ```

   Save and exit the file.

7. Enable IP forwarding:

   Open the `/etc/sysctl.conf` file:

   ```shell
   sudo nano /etc/sysctl.conf
   ```

   Uncomment the line `#net.ipv4.ip_forward=1` by removing the `#` at the beginning.

   Save and exit the file.

   Apply the changes:

   ```shell
   sudo sh -c "echo 1 > /proc/sys/net/ipv4/ip_forward"
   ```

8. Set up network address translation (NAT):

   ```shell
   sudo iptables -t nat -
   ```
