# Connection Problems

Look here if you deployed an Algo server but now have a problem connecting to it with a client.

#### I'm blocked or get CAPTCHAs when I access certain websites

This is normal.

When you deploy a Algo to a new cloud server, the address you are given may have been used before. In some cases, a malicious individual may have attacked others with that address and had it added to "IP reputation" feeds or simply a blacklist. In order to regain the trust for that address, you may be asked to enter CAPTCHAs to prove that you are a human, and not a Denial of Service \(DoS\) bot trying to attack others. This happens most frequently with Google. You can try entering the CAPTCHAs or you can try redeploying your Algo server to a new IP to resolve this issue.

In some cases, a website will block any visitors accessing their site through a cloud hosting provider due to previous, frequent DoS attacks originating from them. In these cases, there is not much you can do except deploy Algo to your own server or another IP that the website has not outright blocked.

#### I want to change the list of trusted Wifi networks on my Apple device

This setting is enforced on your client device via the Apple profile you put on it. You can edit the profile with new settings, then load it on your device to change the settings. You can use the [Apple Configurator](https://itunes.apple.com/us/app/apple-configurator-2/id1037126344?mt=12) to edit and resave the profile. Advanced users can edit the file directly in a text editor. Use the [Configuration Profile Reference](https://developer.apple.com/library/content/featuredarticles/iPhoneConfigurationProfileRef/Introduction/Introduction.html) for information about the file format and other available options. If you're not comfortable editing the profile, you can also simply redeploy a new Algo server with different settings to receive a new auto-generated profile.

#### Error: "The VPN Service payload could not be installed."

You tried to install the Apple profile on one of your devices and you received an error stating `The "VPN Service" payload could not be installed. The VPN service could not be created.` Client support for Algo VPN is limited to modern operating systems, e.g. macOS 10.11+, iOS 9+. Please upgrade your operating system and try again.

#### Little Snitch is broken when connected to the VPN

Little Snitch is not compatible with IPSEC VPNs due to a known bug in macOS and there is no solution. The Little Snitch "filter" does not get incoming packets from IPSEC VPNs and, therefore, cannot evaluate any rules over them. Their developers have filed a bug report with Apple but there has been no response. There is nothing they or Algo can do to resolve this problem on their own. You can read more about this problem in [issue \#134](https://github.com/trailofbits/algo/issues/134).

#### I can't get my router to connect to the Algo server

In order to connect to the Algo VPN server, your router must support IKEv2, ECC certificate-based authentication, and the cipher suite we use. See the ipsec.conf files we generate in the `config` folder for more information. Note that we do not officially support routers as clients for Algo VPN at this time, though patches and documentation for them are welcome \(for example, see open issues for [Ubiquiti](https://github.com/trailofbits/algo/issues/307) and [pfSense](https://github.com/trailofbits/algo/issues/292)\).

#### I can't get Network Manager to connect to the Algo server

You're trying to connect Ubuntu or Debian to the Algo server through the Network Manager GUI but it's not working. Many versions of Ubuntu and some older versions of Debian bundle a [broken version of Network Manager](https://github.com/trailofbits/algo/issues/263) without support for modern standards or the strongSwan server. You must upgrade to Ubuntu 17.04 or Debian 9 Stretch, each of which contain the required minimum version of Network Manager.

#### Various websites appear to be offline through the VPN

This issue appears occasionally due to issues with [MTU](https://en.wikipedia.org/wiki/Maximum_transmission_unit) size. Different networks may require the MTU to be within a specific range to correctly pass traffic. We made an effort to set the MTU to the most conservative, most compatible size by default but problems may still occur.

If either your Internet service provider or your chosen cloud service provider use an MTU smaller than the normal value of 1500 you can use the `reduce_mtu` option in the file `config.cfg` to correspondingly reduce the size of the VPN tunnels created by Algo. Algo will attempt to automatically set `reduce_mtu` based on the MTU found on the server at the time of deployment, but it cannot detect if the MTU is smaller on the client side of the connection.

If you change `reduce_mtu` you'll need to deploy a new Algo VPN.

To determine the value for `reduce_mtu` you should examine the MTU on your Algo VPN server's primary network interface \(see below\). You might algo want to run tests using `ping`, both on a local client _when not connected to the VPN_ and also on your Algo VPN server \(see below\). Then take the smallest MTU you find \(local or server side\), subtract it from 1500, and use that for `reduce_mtu`. An exception to this is if you find the smallest MTU is your local MTU at 1492, typical for PPPoE connections, then no MTU reduction should be necessary.

**Check the MTU on the Algo VPN server**

To check the MTU on your server, SSH in to it, run the command `ifconfig`, and look for the MTU of the main network interface. For example:

```text
ens4: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1460
```

The MTU shown here is 1460 instead of 1500. Therefore set `reduce_mtu: 40` in `config.cfg`. Algo should do this automatically.

**Determine the MTU using ping**

When using `ping` you increase the payload size with the "Don't Fragment" option set until it fails. The largest payload size that works, plus the `ping` overhead of 28, is the MTU of the connection.

**Example: Test on your Algo VPN server \(Ubuntu\)**

```text
$ ping -4 -s 1432 -c 1 -M do github.com
PING github.com (192.30.253.112) 1432(1460) bytes of data.
1440 bytes from lb-192-30-253-112-iad.github.com (192.30.253.112): icmp_seq=1 ttl=53 time=13.1 ms

--- github.com ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 13.135/13.135/13.135/0.000 ms

$ ping -4 -s 1433 -c 1 -M do github.com
PING github.com (192.30.253.113) 1433(1461) bytes of data.
ping: local error: Message too long, mtu=1460

--- github.com ping statistics ---
1 packets transmitted, 0 received, +1 errors, 100% packet loss, time 0ms
```

In this example the largest payload size that works is 1432. The `ping` overhead is 28 so the MTU is 1432 + 28 = 1460, which is 40 lower than the normal MTU of 1500. Therefore set `reduce_mtu: 40` in `config.cfg`.

**Example: Test on a macOS client not connected to your Algo VPN**

```text
$ ping -c 1 -D -s 1464 github.com
PING github.com (192.30.253.113): 1464 data bytes
1472 bytes from 192.30.253.113: icmp_seq=0 ttl=50 time=169.606 ms

--- github.com ping statistics ---
1 packets transmitted, 1 packets received, 0.0% packet loss
round-trip min/avg/max/stddev = 169.606/169.606/169.606/0.000 ms

$ ping -c 1 -D -s 1465 github.com
PING github.com (192.30.253.113): 1465 data bytes

--- github.com ping statistics ---
1 packets transmitted, 0 packets received, 100.0% packet loss
```

In this example the largest payload size that works is 1464. The `ping` overhead is 28 so the MTU is 1464 + 28 = 1492, which is typical for a PPPoE Internet connection and does not require an MTU adjustment. Therefore use the default of `reduce_mtu: 0` in `config.cfg`.

**Change the client MTU without redeploying the Algo VPN**

If you don't wish to deploy a new Algo VPN \(which is required to incorporate a change to `reduce_mtu`\) you can change the client side MTU of WireGuard clients and Linux IPsec clients without needing to make changes to your Algo VPN.

For WireGuard on Linux, or macOS \(when installed with `brew`\), you can specify the MTU yourself in the client configuration file \(typically `wg0.conf`\). Refer to the documentation \(see `man wg-quick`\).

For WireGuard on iOS and Android you can change the MTU in the app.

For IPsec on Linux you can change the MTU of your network interface to match the required MTU. For example:

```text
sudo ifconfig eth0 mtu 1440
```

To make the change take affect after a reboot, on Ubuntu 18.04 and later edit the relevant file in the `/etc/netplan` directory \(see `man netplan`\).

**Note for WireGuard iOS users**

As of WireGuard for iOS 0.0.20190107 the default MTU is 1280, a conservative value intended to allow mobile devices to continue to work as they switch between different networks which might have smaller than normal MTUs. In order to use this default MTU review the configuration in the WireGuard app and remove any value for MTU that might have been added automatically by Algo.

#### Clients appear stuck in a reconnection loop

If you're using 'Connect on Demand' on iOS and your client device appears stuck in a reconnection loop after switching from WiFi to LTE or vice versa, you may want to try disabling DoS protection in strongSwan.

The configuration value can be found in `/etc/strongswan.d/charon.conf`. After making the change you must reload or restart ipsec.

Example command:

```text
sed -i -e 's/#*.dos_protection = yes/dos_protection = no/' /etc/strongswan.d/charon.conf && ipsec restart
```

#### WireGuard: Clients can connect on Wifi but not LTE

Certain cloud providers \(like AWS Lightsail\) don't assign an IPv6 address to your server, but certain cellular carriers \(e.g. T-Mobile in the United States, [EE](https://community.ee.co.uk/t5/4G-and-mobile-data/IPv4-VPN-Connectivity/td-p/757881) in the United Kingdom\) operate an IPv6-only network. This somehow leads to the Wireguard app not being able to make a connection when transitioning to cell service. Go to the Wireguard app on the device when you're having problems with cell connectivity and select "Export log file" or similar option. If you see a long string of error messages like "`Failed to send data packet write udp6 [::]:49727->[2607:7700:0:2a:0:1:354:40ae]:51820: sendto: no route to host` then you might be having this problem.

Manually disconnecting and then reconnecting should restore your connection. To solve this, you need to either "force IPv4 connection" if available on your phone, or install an IPv4 APN, which might be available from your carrier tech support. T-mobile's is available [for iOS here under "iOS IPv4/IPv6 fix"](https://www.reddit.com/r/tmobile/wiki/index), and [here is a walkthrough for Android phones](https://www.myopenrouter.com/article/vpn-connections-not-working-t-mobile-heres-how-fix).

#### IPsec: Difficulty connecting through router

Some routers treat IPsec connections specially because older versions of IPsec did not work properly through [NAT](https://en.wikipedia.org/wiki/Network_address_translation). If you're having problems connecting to your AlgoVPN through a specific router using IPsec you might need to change some settings on the router.

**Change the "VPN Passthrough" settings**

If your router has a setting called something like "VPN Passthrough" or "IPsec Passthrough" try changing the setting to a different value.

**Change the default pfSense NAT rules**

If your router runs [pfSense](https://www.pfsense.org/) and a single IPsec client can connect but you have issues when using multiple clients, you'll need to change the **Outbound NAT** mode to **Manual Outbound NAT** and disable the rule that specifies **Static Port** for IKE \(UDP port 500\). See [Outbound NAT](https://docs.netgate.com/pfsense/en/latest/book/nat/outbound-nat.html#outbound-nat) in the [pfSense Book](https://docs.netgate.com/pfsense/en/latest/book).

