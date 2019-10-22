# VPN Client Configuration



Certificates and configuration files that users will need are placed in the `configs` directory. Make sure to secure these files since many contain private keys. All files are saved under a subdirectory named with the IP address of your new Algo VPN server.

#### Apple Devices

WireGuard is used to provide VPN services on Apple devices. Algo generates a WireGuard configuration file, `wireguard/<username>.conf`, and a QR code, `wireguard/<username>.png`, for each user defined in `config.cfg`.

On iOS, install the [WireGuard](https://itunes.apple.com/us/app/wireguard/id1441195209?mt=8) app from the iOS App Store. Then, use the WireGuard app to scan the QR code or AirDrop the configuration file to the device.

On macOS Mojave or later, install the [WireGuard](https://itunes.apple.com/us/app/wireguard/id1451685025?mt=12) app from the Mac App Store. WireGuard will appear in the menu bar once you run the app. Click on the WireGuard icon, choose **Import tunnel\(s\) from file...**, then select the appropriate WireGuard configuration file.

On either iOS or macOS, you can enable "Connect on Demand" and/or exclude certain trusted Wi-Fi networks \(such as your home or work\) by editing the tunnel configuration in the WireGuard app. \(Algo can't do this automatically for you.\)

Installing WireGuard is a little more complicated on older version of macOS. See [Using macOS as a Client with WireGuard](https://github.com/trailofbits/algo/blob/master/docs/client-macos-wireguard.md).

If you prefer to use the built-in IPSEC VPN on Apple devices, or need "Connect on Demand" or excluded Wi-Fi networks automatically configured, then see [Using Apple Devices as a Client with IPSEC](https://github.com/trailofbits/algo/blob/master/docs/client-apple-ipsec.md).

#### Android Devices

WireGuard is used to provide VPN services on Android. Install the [WireGuard VPN Client](https://play.google.com/store/apps/details?id=com.wireguard.android). Import the corresponding `wireguard/<name>.conf` file to your device, then setup a new connection with it. See the [Android setup instructions](https://github.com/trailofbits/algo/blob/master/docs/client-android.md) for more detailed walkthrough.

#### Windows

WireGuard is used to provide VPN services on Windows. Algo generates a WireGuard configuration file, `wireguard/<username>.conf`, for each user defined in `config.cfg`.

Install the [WireGuard VPN Client](https://www.wireguard.com/install/#windows-7-8-81-10-2012-2016-2019). Import the generated `wireguard/<username>.conf` file to your device, then setup a new connection with it.

#### Linux WireGuard Clients

WireGuard works great with Linux clients. See [this page](https://github.com/trailofbits/algo/blob/master/docs/client-linux-wireguard.md) for an example of how to configure WireGuard on Ubuntu.

#### Linux strongSwan IPsec Clients \(e.g., OpenWRT, Ubuntu Server, etc.\)

Please see [this page](https://github.com/trailofbits/algo/blob/master/docs/client-linux-ipsec.md).

#### Other Devices

Depending on the platform, you may need one or multiple of the following files.

* ipsec/manual/cacert.pem: CA Certificate
* ipsec/manual/.p12: User Certificate and Private Key \(in PKCS\#12 format\)
* ipsec/manual/.conf: strongSwan client configuration
* ipsec/manual/.secrets: strongSwan client configuration
* ipsec/apple/.mobileconfig: Apple Profile
* wireguard/.conf: WireGuard configuration profile
* wireguard/.png: WireGuard configuration QR code

