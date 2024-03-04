# 2-Hop WireGuard Installer

![Lint](https://github.com/rachis-systems/wg-2hop-install/workflows/Lint/badge.svg)

**This project is a bash script for setting up [WireGuard](https://www.wireguard.com/) VPN on a server with customized configuration to route the traffic through a device behind NAT**

Thus the traffic would take 2-hops to reach the gateway (hence the name of the project).

![a diagram demonstrating the network structure](assets/structure-diagram.drawio.png)

WireGuard is a point-to-point VPN that can be used in different ways. Here the client forwards all its traffic through an encrypted tunnel to the server.
Then the server will forward the traffic through an encrypted tunnel again into a specific peer (client) which would apply NAT (Network Address Translation) to the original client's traffic so it will appear as if the client is browsing the web with the specific peer's IP.

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

## Contributing

## Discuss changes

Please open an issue before submitting a PR if you want to discuss a change, especially if it's a big one.

### Code formatting

We use [shellcheck](https://github.com/koalaman/shellcheck) and [shfmt](https://github.com/mvdan/sh) to enforce bash styling guidelines and good practices. They are executed for each commit / PR with GitHub Actions, so you can check the configuration [here](https://github.com/angristan/wireguard-install/blob/master/.github/workflows/lint.yml).

## Credits & License

This script is a modified version of the [wireguard-install](https://github.com/angristan/wireguard-install) script by [angristan](https://github.com/angristan) which is available under the [MIT License](https://raw.githubusercontent.com/angristan/wireguard-install/master/LICENSE).
