# Server Firewall

During installation Algo configures the Linux [Netfilter](https://en.wikipedia.org/wiki/Netfilter) firewall on the server. The rules added are required for AlgoVPN to work properly. The package `netfilter-persistent` is used to load the IPv4 and IPv6 rules files that Algo generates and stores in `/etc/iptables`. The rules for IPv6 are only generated if the server appears to be properly configured for IPv6. The use of conflicting firewall packages on the server such as `ufw` will likely break AlgoVPN.

