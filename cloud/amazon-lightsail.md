---
description: 'TODO:'
---

# Amazon Lightsail

1. Create a key pair \(see: [here](https://www.digitalocean.com/docs/droplets/how-to/add-ssh-keys/create-with-openssh/) if you need help with this step\)
2. Log into Lightsail and upload your newly created SSH key to the region in which you plan to deploy your VPN server
3. Create a new $3.50/mo Ubuntu 16.04 LTS Lightsail instance in your desired location
4. Create and attach a static IP address to your new instance
5. Log into the newly created instance and run: `do-release-upgrade` - this will update the 16.04 instance to 18.04
6. Follow the directions in README.md to deploy the Algo server, selecting "Install Algo to existing Ubuntu 18.04 server" when prompted and using the user "ubuntu" when asked
7. Once Algo finishes installing, log into Lightsail and open UDP ports 500 and 4500.



