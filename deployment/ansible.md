# Ansible



## Deployment from Ansible

Before you begin, make sure you have installed all the dependencies necessary for your operating system as described in the [README](https://github.com/trailofbits/algo/blob/master/README.md).

You can deploy Algo non-interactively by running the Ansible playbooks directly with `ansible-playbook`.

`ansible-playbook` accepts variables via the `-e` or `--extra-vars` option. You can pass variables as space separated key=value pairs. Algo requires certain variables that are listed below. You can also use the `--skip-tags` option to skip certain parts of the install, such as `iptables` \(overwrite iptables rules\), `ipsec` \(install strongSwan\), `wireguard` \(install Wireguard\). We don't recommend using the `-t` option as it will only include the tagged portions of the deployment, and skip certain necessary roles \(such as `common`\).

Here is a full example for DigitalOcean:

```text
ansible-playbook main.yml -e "provider=digitalocean
                                server_name=algo
                                ondemand_cellular=false
                                ondemand_wifi=false
                                dns_adblocking=true
                                ssh_tunneling=true
                                store_pki=true
                                region=ams3
                                do_token=token"
```

See below for more information about variables and roles.

#### Variables

* `provider` - \(Required\) The provider to use. See possible values below
* `server_name` - \(Required\) Server name. Default: algo
* `ondemand_cellular` \(Optional\) VPN On Demand when connected to cellular networks with IPsec. Default: false
* `ondemand_wifi` - \(Optional. See `ondemand_wifi_exclude`\) VPN On Demand when connected to WiFi networks with IPsec. Default: false
* `ondemand_wifi_exclude` \(Required if `ondemand_wifi` set\) - WiFi networks to exclude from using the VPN. Comma-separated values
* `dns_adblocking` - \(Optional\) Enables dnscrypt-proxy adblocking. Default: false
* `ssh_tunneling` - \(Optional\) Enable SSH tunneling for each user. Default: false
* `store_cakey` - \(Optional\) Whether or not keep the CA key \(required to add users in the future, but less secure\). Default: false

If any of the above variables are unspecified, ansible will ask the user to input them.

#### Ansible roles

Cloud roles can be activated by specifying an extra variable `provider`.

Cloud roles:

* role: cloud-digitalocean, [provider: digitalocean](https://github.com/trailofbits/algo/blob/master/docs/deploy-from-ansible.md#digital-ocean)
* role: cloud-ec2, [provider: ec2](https://github.com/trailofbits/algo/blob/master/docs/deploy-from-ansible.md#amazon-ec2)
* role: cloud-gce, [provider: gce](https://github.com/trailofbits/algo/blob/master/docs/deploy-from-ansible.md#google-compute-engine)
* role: cloud-vultr, [provider: vultr](https://github.com/trailofbits/algo/blob/master/docs/deploy-from-ansible.md#vultr)
* role: cloud-azure, [provider: azure](https://github.com/trailofbits/algo/blob/master/docs/deploy-from-ansible.md#azure)
* role: cloud-lightsail, [provider: lightsail](https://github.com/trailofbits/algo/blob/master/docs/deploy-from-ansible.md#lightsail)
* role: cloud-scaleway, [provider: scaleway](https://github.com/trailofbits/algo/blob/master/docs/deploy-from-ansible.md#scaleway)
* role: cloud-openstack, [provider: openstack](https://github.com/trailofbits/algo/blob/master/docs/deploy-from-ansible.md#openstack)
* role: cloud-cloudstack, [provider: cloudstack](https://github.com/trailofbits/algo/blob/master/docs/deploy-from-ansible.md#cloudstack)
* role: cloud-hetzner, [provider: hetzner](https://github.com/trailofbits/algo/blob/master/docs/deploy-from-ansible.md#hetzner)

Server roles:

* role: strongswan
  * Installs [strongSwan](https://www.strongswan.org/)
  * Enables AppArmor, limits CPU and memory access, and drops user privileges
  * Builds a Certificate Authority \(CA\) with [easy-rsa-ipsec](https://github.com/ValdikSS/easy-rsa-ipsec) and creates one client certificate per user
  * Bundles the appropriate certificates into Apple mobileconfig profiles for each user
* role: dns\_adblocking
  * Installs DNS encryption through [dnscrypt-proxy](https://github.com/jedisct1/dnscrypt-proxy) with blacklists to be updated daily from `adblock_lists` in `config.cfg` - note this will occur even if `dns_encryption` in `config.cfg` is set to `false`
  * Constrains dnscrypt-proxy with AppArmor and cgroups CPU and memory limitations
* role: ssh\_tunneling
  * Adds a restricted `algo` group with no shell access and limited SSH forwarding options
  * Creates one limited, local account and an SSH public key for each user
* role: wireguard
  * Installs a [Wireguard](https://www.wireguard.com/) server, with a startup script, and automatic checks for upgrades
  * Creates wireguard.conf files for Linux clients as well as QR codes for Apple/Android clients

Note: The `strongswan` role generates Apple profiles with On-Demand Wifi and Cellular if you pass the following variables:

* ondemand\_wifi: true
* ondemand\_wifi\_exclude: HomeNet,OfficeWifi
* ondemand\_cellular: true

#### Local Installation

* role: local, provider: local

This role is intended to be run for local install onto an Ubuntu server, or onto an unsupported cloud provider's Ubuntu instance. Required variables:

* server - IP address of your server \(or "localhost" if deploying to the local machine\)
* endpoint - public IP address of the server you're installing on
* ssh\_user - name of the SSH user you will use to install on the machine \(passwordless login required\). If `server=localhost`, this isn't required.
* ca\_password - Password for the private CA key

Note that by default, the iptables rules on your existing server will be overwritten. If you don't want to overwrite the iptables rules, you can use the `--skip-tags iptables` flag.

#### Digital Ocean

Required variables:

* do\_token
* region

Possible options can be gathered calling to [https://api.digitalocean.com/v2/regions](https://api.digitalocean.com/v2/regions)

#### Amazon EC2

Required variables:

* aws\_access\_key: `AKIA...`
* aws\_secret\_key
* region: e.g. `us-east-1`

Possible options can be gathered via cli `aws ec2 describe-regions`

Additional variables:

* [encrypted](https://aws.amazon.com/blogs/aws/new-encrypted-ebs-boot-volumes/) - Encrypted EBS boot volume. Boolean \(Default: false\)

**Minimum required IAM permissions for deployment:**

```text
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PreDeployment",
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeImages",
                "ec2:DescribeKeyPairs",
                "ec2:DescribeRegions",
                "ec2:ImportKeyPair",
                "ec2:CopyImage"
            ],
            "Resource": [
                "*"
            ]
        },
        {
            "Sid": "DeployCloudFormationStack",
            "Effect": "Allow",
            "Action": [
                "cloudformation:CreateStack",
                "cloudformation:UpdateStack",
                "cloudformation:DescribeStacks",
                "cloudformation:DescribeStackEvents",
                "cloudformation:ListStackResources"
            ],
            "Resource": [
                "*"
            ]
        },
        {
            "Sid": "CloudFormationEC2Access",
            "Effect": "Allow",
            "Action": [
                "ec2:CreateInternetGateway",
                "ec2:DescribeVpcs",
                "ec2:CreateVpc",
                "ec2:DescribeInternetGateways",
                "ec2:ModifyVpcAttribute",
                "ec2:createTags",
                "ec2:CreateSubnet",
                "ec2:Associate*",
                "ec2:CreateRouteTable",
                "ec2:AttachInternetGateway",
                "ec2:DescribeRouteTables",
                "ec2:DescribeSubnets",
                "ec2:ModifySubnetAttribute",
                "ec2:CreateRoute",
                "ec2:CreateSecurityGroup",
                "ec2:DescribeSecurityGroups",
                "ec2:AuthorizeSecurityGroupIngress",
                "ec2:RunInstances",
                "ec2:DescribeInstances",
                "ec2:AllocateAddress",
                "ec2:DescribeAddresses"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```

#### Google Compute Engine

Required variables:

* gce\_credentials\_file: e.g. /configs/gce.json if you use the [GCE docs](https://trailofbits.github.io/algo/cloud-gce.html) - can also be defined in environment as GCE\_CREDENTIALS\_FILE\_PATH
* [region](https://cloud.google.com/compute/docs/regions-zones/): e.g. `useast-1`

#### Vultr

Required variables:

* [vultr\_config](https://trailofbits.github.io/algo/cloud-vultr.html): /path/to/.vultr.ini
* [region](https://api.vultr.com/v1/regions/list): e.g. `Chicago`, `'New Jersey'`

#### Azure

Required variables:

* azure\_secret
* azure\_tenant
* azure\_client\_id
* azure\_subscription\_id
* [region](https://azure.microsoft.com/en-us/global-infrastructure/regions/)

#### Lightsail

Required variables:

* aws\_access\_key: `AKIA...`
* aws\_secret\_key
* region: e.g. `us-east-1`

Possible options can be gathered via cli `aws lightsail get-regions`

**Minimum required IAM permissions for deployment:**

```text
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "LightsailDeployment",
            "Effect": "Allow",
            "Action": [
                "lightsail:GetRegions",
                "lightsail:GetInstance",
                "lightsail:CreateInstances",
                "lightsail:OpenInstancePublicPorts"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```

#### Scaleway

Required variables:

* [scaleway\_token](https://www.scaleway.com/docs/generate-an-api-token/)
* region: e.g. `ams1`, `par1`

#### OpenStack

You need to source the rc file prior to run Algo. Download it from the OpenStack dashboard-&gt;Compute-&gt;API Access and source it in the shell \(eg: source /tmp/dhc-openrc.sh\)

#### CloudStack

Required variables:

* [cs\_config](https://trailofbits.github.io/algo/cloud-cloudstack.html): /path/to/.cloudstack.ini
* cs\_region: e.g. `exoscale`
* cs\_zones: e.g. `ch-gva2`

The first two can also be defined in your environment, using the variables `CLOUDSTACK_CONFIG` and `CLOUDSTACK_REGION`.

#### Hetzner

Required variables:

* hcloud\_token: Your [API token](https://trailofbits.github.io/algo/cloud-hetzner.html#api-token) - can also be defined in the environment as HCLOUD\_TOKEN
* region: e.g. `nbg1`

#### Update users

Playbook:

```text
users.yml
```

Required variables:

* server - IP or hostname to access the server via SSH
* ca\_password - Password to access the CA key

Tags required:

* update-users

