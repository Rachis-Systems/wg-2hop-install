# 2-Hop WireGuard Installer

**This project is a bash script for setting up [WireGuard](https://www.wireguard.com/) VPN on a server with customized configuration to route the traffic through a device behind NAT.**

![a diagram demonstrating the network structure](assets/structure-diagram.drawio.png)

<!--
Diagram made using Draw.io. The source `.drawio` file is available within the assets directory.
GIMP was used to add black outline after exporting the diagram as a PNG image. In order to have the diagram readable on systems with the light theme used.
-->

Consider that you want to access a **LAN Device** within a _remote_ apartment, or use the internet with a public residential IP in a specific country (**_"Regional"_ WAN**).

Usually you would open a public port and run a VPN server on a device within that apartment, but the challenge rises when the internet connection is behind **NAT** ([Network Address Translation](https://en.wikipedia.org/wiki/Network_address_translation), that's usually the case for residential subscribers).
In such a case opening a port would depend on the ISP and require modifications to the router that _may not be possible_.

To walkthrough that, a server running on the cloud would be used to act as a **relay**, allowing authorized clients to connect to it anywhere and forward (**relay**) their traffic into the target device within the apartment (acting as a **"gateway"**).

Thus the traffic would take _virtually_ **2-hops** to reach the apartment (hence the name of the project).

### wg-2hop-script.sh

A script was created to install WireGuard on a server and configure the system for it to act as a relay.

- Allows the creation & deletion of additional clients after installation.
- The first client created during installation, or the client with the IP `192.168.197.1` **is the special "gateway" client**.
- Based on [wireguard-install](https://github.com/angristan/wireguard-install) but modified to not conflict with existing `wireguard-install` installation.
- Firewall & Routing tables are configured to isolate the clients from each other and protect the other services running on the relay server.
- The subnet is hardcoded to `192.168.197.0/24` to suite [Windows ICS](https://en.wikipedia.org/wiki/Internet_Connection_Sharing) and precalculate some filtering rules.

#### Tunnel Firewall Configuration

When the tunnel is active, firewall rules are configured to:

- Isolate the relay server from the VPN peers traffic (prevent access to other services running on the same server).
- Isolate the traffic between the clients (clients are not allowed to communicate between each other).
- Disallow direct communication with the gateway client.
- Allow pinging the gateway client & the relay server.
- Allow the gateway client to ping any other client.

It should not interfere with other firewall configurations.

#### Tunnel Routing Configuration

When the tunnel is active, a custom routing table (`4224`) is used to prevent packets of the VPN from being routed to other interfaces.

Within the custom routing table, rules are added to forward the all traffic through the special "gateway" client. Except the traffic with the subnet mask of the VPN.

## Requirements

Supported distributions:

- AlmaLinux >= 8
- Arch Linux
- CentOS Stream >= 8
- Debian >= 10
- Fedora >= 32
- Oracle Linux
- Rocky Linux >= 8
- Ubuntu >= 18.04

## Usage

Download and execute the script. Answer the questions asked by the script and it will take care of the rest.

```bash
curl -O https://raw.githubusercontent.com/rachis-systems/wg-2hop-install/main/wg-2hop-install.sh
chmod +x wg-2hop-install.sh
./wg-2hop-install.sh
```

It will install WireGuard (kernel module and tools) on the server, configure it, create a systemd service and a client configuration file.

Run the script again to add or remove clients!

### "gateway" Client Configuration

It's the client with IP `192.168.137.1`, which is the first client created using the script by default. But that could be changed by selecting a different IP for the first client.

To setup a Windows client do the following:

1. Transfer the configuration file into the target device and import it and activate the VPN tunnel.
2. Search for `View network connections` in the start search bar and open that control panel.
![View network connections item in the search results](assets/network-connections.png)
3. Select the adapter with the network connection you want to share with the VPN, then open it's properties.
![WiFi Adapter with Properties option highlighted](assets/wifi-adapter.png)
4. Share the adapter's connection with the VPN adapter.
![Sharing WiFi connection with the target VPN adapter](assets/network-sharing.png)
    1. Switch to the **Sharing** tab.
    2. Check _"Allow other network users to connect through this computer's Internet connection"_.
    3. Select the VPN adapter (would have a name matching the configuration filename).
    4. Ensure that the _"Allow other network users to control or disable the shared Internet connection"_ box is **unchecked**.
    5. Confirm the changes by clicking _"OK"_.
5. Deactivate & Reactivate the VPN tunnel, the network sharing should work now.


## Contributing

## Discuss changes

Please open an issue before submitting a PR if you want to discuss a change, especially if it's a big one.

### Code formatting

We use [shellcheck](https://github.com/koalaman/shellcheck) and [shfmt](https://github.com/mvdan/sh) to enforce bash styling guidelines and good practices. They are executed for each commit / PR with GitHub Actions, so you can check the configuration [here](https://github.com/angristan/wireguard-install/blob/master/.github/workflows/lint.yml).

## Credits & License

This script is a modified version of the [wireguard-install](https://github.com/angristan/wireguard-install) script by [angristan](https://github.com/angristan) which is available under the [MIT License](https://raw.githubusercontent.com/angristan/wireguard-install/master/LICENSE).
