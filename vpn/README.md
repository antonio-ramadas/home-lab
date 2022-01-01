# VPN

## PiVPN

To abstract us from the complexity of setting up a VPN, [PiVPN](https://pivpn.io/) greatly simplifies the experience. The installation process is simple and the instructions clear.

PiVPN explains the two options it offers, OpenVPN and WireGuard. As an aside, I'm happy with WireGuard.

PiVPN is very intuitive. All commands (including adding, removing and listing clients) are listed just by executing `pivpn` without arguments.

### Using with a DNS

If we have a DNS entry resolving to the Raspberry Pi, then we most likely also want to pass it around as the host clients should connect to. Before starting to add clients (to avoid having to change the clients' configurations manually later on), we can just set `pivpnHOST` to the DNS entry on the file `/etc/pivpn/wireguard/setupVars.conf`. More information available on [PiVPN Dynamic DNS guide](https://docs.pivpn.io/guides/dynamic-dns/#duckdns).
