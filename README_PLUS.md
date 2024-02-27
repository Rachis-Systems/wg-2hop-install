
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