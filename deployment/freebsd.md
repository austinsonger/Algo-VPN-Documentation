# FreeBSD

## FreeBSD / HardenedBSD server setup

FreeBSD server support is a work in progress. For now, it is only possible to install Algo on existing FreeBSD 11 systems.

### System preparation

Ensure that the following kernel options are enabled:

```text
# sysctl kern.conftxt | grep -iE "IPSEC|crypto"
options	IPSEC
options IPSEC_NAT_T
device	crypto
```

### Available roles

* vpn
* ssh\_tunneling
* dns\_adblocking

### Additional variables

* rebuild\_kernel - set to `true` if you want to let Algo to rebuild your kernel if needed \(takes a lot of time\)

### Installation

```text
ansible-playbook main.yml -e "provider=local"
```

And follow the instructions

