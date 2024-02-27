
## Modifications on the original script

- Changed the repository url in the script to `https://github.com/rami-sabbagh/wg-2hop-install`.
- Added a warning when the script is executed to note the user of the script being modified.
- Added an option for soft uninstallation, deletes the configuration only.
- Rename to `wg-2hop-install.sh`.
- Changed the default interface name from `wg0` into `wg2hop`.
- Changed the default subnet.
    - IPv4 from `10.66.66.0/24` into `10.66.88.0/24`.
    - IPv6 from `fd42:42:42::/64` into `fd42:42:40::/64`.
- Changed configuration filenames to avoid conflict with the original script.
    - Changed `/etc/wireguard/params` into `/etc/wireguard/params_2hop`.
- Changed the `PostUp`&`PostDown` options in the server config to configure the firewall differently.
- The first client (with ip `10.66.88.2`) has customized configuration on the server side, client side and the firewall.
    - The subnet `10.66.88.0` is not hardcoded, but the subnet mask must be always `/24`.
    - Customized `AllowedIPs` and added `PersistentKeepalive`.

## Footprint

_The modifications left on the system after execution._

- Installs `wireguard iptables resolvconf qrencode` on the system (if Debian/Ubuntu).
- `/etc/sysctl.d/wg_2hop.conf` for enabling packets forwarding on the server.
- The `/etc/wireguard` directory, for storing the server configuration.
    - `/etc/wireguard/params_2hop` for storing the answers of setup questions.
    - `/etc/wireguard/wg_2hop.conf` the configuration of the server tunnel.
- `~/*.conf` peers (clients) WireGuard configuration files.
    - **If the client name matches the name of a user on the system. That user's home directory would be used instead.**
- While the tunnel is active, modifications to the firewall are done, including the creation of `FORWARD_WG2HOP` chain.
    - The changes are undone when the tunnel is disconnected.
    - The changes are persisted using WireGuard configuration, disabling the tunnel or deleting it removes them.
    - The changes are not restored after reboot unless if the tunnel is set to start automatically (which is done by default using the script).