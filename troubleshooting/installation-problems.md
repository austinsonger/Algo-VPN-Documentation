# Installation Problems

Look here if you have a problem running the installer to set up a new Algo server.

#### Python version is not supported

The minimum Python version required to run Algo is 3.6. Most modern operation systems should have it by default, but if the OS you are using doesn't meet the requirements, you have to upgrade. See the official documentation for your OS, or manual download it from [https://www.python.org/downloads/](https://www.python.org/downloads/). Otherwise, you may [deploy from docker](https://github.com/trailofbits/algo/blob/master/docs/deploy-from-docker.md)

#### Error: "You have not agreed to the Xcode license agreements"

On macOS, you tried to install the dependencies with pip and encountered the following error:

```text
Downloading cffi-1.9.1.tar.gz (407kB): 407kB downloaded
  Running setup.py (path:/private/tmp/pip_build_root/cffi/setup.py) egg_info for package cffi

You have not agreed to the Xcode license agreements, please run 'xcodebuild -license' (for user-level acceptance) or 'sudo xcodebuild -license' (for system-wide acceptance) from within a Terminal window to review and agree to the Xcode license agreements.

    No working compiler found, or bogus compiler options
    passed to the compiler from Python's distutils module.
    See the error messages above.

----------------------------------------
Cleaning up...
Command python setup.py egg_info failed with error code 1 in /private/tmp/pip_build_root/cffi
Storing debug log for failure in /Users/algore/Library/Logs/pip.log
```

The Xcode compiler is installed but requires you to accept its license agreement prior to using it. Run `xcodebuild -license` to agree and then retry installing the dependencies.

#### Error: checking whether the C compiler works... no

On macOS, you tried to install the dependencies with pip and encountered the following error:

```text
Failed building wheel for pycrypto
Running setup.py clean for pycrypto
Failed to build pycrypto
...
copying lib/Crypto/Signature/PKCS1_v1_5.py -> build/lib.macosx-10.6-intel-2.7/Crypto/Signature
running build_ext
running build_configure
checking for gcc... gcc
checking whether the C compiler works... no
configure: error: in '/private/var/folders/3f/q33hl6_x6_nfyjg29fcl9qdr0000gp/T/pip-build-DB5VZp/pycrypto': configure: error: C compiler cannot create executables See config.log for more details
Traceback (most recent call last):
File "", line 1, in
...
cmd_obj.run()
File "/private/var/folders/3f/q33hl6_x6_nfyjg29fcl9qdr0000gp/T/pip-build-DB5VZp/pycrypto/setup.py", line 278, in run
raise RuntimeError("autoconf error")
RuntimeError: autoconf error
```

You don't have a working compiler installed. You should install the XCode compiler by opening your terminal and running `xcode-select --install`.

#### Error: "fatal error: 'openssl/opensslv.h' file not found"

On macOS, you tried to install `cryptography` and encountered the following error:

```text
build/temp.macosx-10.12-intel-2.7/_openssl.c:434:10: fatal error: 'openssl/opensslv.h' file not found

#include <openssl/opensslv.h>

        ^

1 error generated.

error: command 'cc' failed with exit status 1

----------------------------------------
Cleaning up...
Command /usr/bin/python -c "import setuptools, tokenize;__file__='/private/tmp/pip_build_root/cryptography/setup.py';exec(compile(getattr(tokenize, 'open', open)(__file__).read().replace('\r\n', '\n'), __file__, 'exec'))" install --record /tmp/pip-sREEE5-record/install-record.txt --single-version-externally-managed --compile failed with error code 1 in /private/tmp/pip_build_root/cryptography
Storing debug log for failure in /Users/algore/Library/Logs/pip.log
```

You are running an old version of `pip` that cannot download the binary `cryptography` dependency. Upgrade to a new version of `pip` by running `sudo python3 -m pip install -U pip`.

#### Error: "ansible-playbook: command not found"

You tried to install Algo and you see an error that reads "ansible-playbook: command not found."

You did not finish step 4 in the installation instructions, "[Install Algo's remaining dependencies](https://github.com/trailofbits/algo#deploy-the-algo-server)." Algo depends on [Ansible](https://github.com/ansible/ansible), an automation framework, and this error indicates that you do not have Ansible installed. Ansible is installed by `pip` when you run `python3 -m pip install -r requirements.txt`. You must complete the installation instructions to run the Algo server deployment process.

#### Fatal: "Failed to validate the SSL certificate"

You received a message like this:

```text
fatal: [localhost]: FAILED! => {"changed": false, "msg": "Failed to validate the SSL certificate for api.digitalocean.com:443. Make sure your managed systems have a valid CA certificate installed. You can use validate_certs=False if you do not need to confirm the servers identity but this is unsafe and not recommended. Paths checked for this platform: /etc/ssl/certs, /etc/ansible, /usr/local/etc/openssl. The exception msg was: [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1076).", "status": -1, "url": "https://api.digitalocean.com/v2/regions"}
```

Your local system does not have a CA certificate that can validate the cloud provider's API. Are you using MacPorts instead of Homebrew? The MacPorts openssl installation does not include a CA certificate, but you can fix this by installing the [curl-ca-bundle](https://andatche.com/articles/2012/02/fixing-ssl-ca-certificates-with-openssl-from-macports/) port with `port install curl-ca-bundle`. That should do the trick.

#### Could not fetch URL ... TLSV1\_ALERT\_PROTOCOL\_VERSION

You tried to install Algo and you received an error like this one:

```text
Could not fetch URL https://pypi.python.org/simple/secretstorage/: There was a problem confirming the ssl certificate: [SSL: TLSV1_ALERT_PROTOCOL_VERSION] tlsv1 alert protocol version (_ssl.c:590) - skipping
  Could not find a version that satisfies the requirement SecretStorage<3 (from -r requirements.txt (line 2)) (from versions: )
No matching distribution found for SecretStorage<3 (from -r requirements.txt (line 2))
```

It's time to upgrade your python.

`brew upgrade python3`

You can also download python 3.7.x from python.org.

#### Bad owner or permissions on .ssh

You tried to run Algo and it quickly exits with an error about a bad owner or permissions:

```text
fatal: [104.236.2.94]: UNREACHABLE! => {"changed": false, "msg": "Failed to connect to the host via ssh: Bad owner or permissions on /home/user/.ssh/config\r\n", "unreachable": true}
```

You need to reset the permissions on your `.ssh` directory. Run `chmod 700 /home/user/.ssh` and then `chmod 600 /home/user/.ssh/config`. You may need to repeat this for other files mentioned in the error message.

#### The region you want is not available

Algo downloads the regions from the supported cloud providers \(other than Microsoft Azure\) listed in the first menu using APIs. If the region you want isn't available, the cloud provider has probably taken it offline for some reason. You should investigate further with your cloud provider.

If there's a specific region you want to install to in Microsoft Azure that isn't available, you should [file an issue](https://github.com/trailofbits/algo/issues/new), give us information about what region is missing, and we'll add it.

#### AWS: SSH permission denied with an ECDSA key

You tried to deploy Algo to AWS and you received an error like this one:

```text
TASK [Copy the algo ssh key to the local ssh directory] ************************
ok: [localhost -> localhost]

PLAY [Configure the server and install required software] **********************

TASK [Check the system] ********************************************************
fatal: [X.X.X.X]: UNREACHABLE! => {"changed": false, "msg": "Failed to connect to the host via ssh: Warning: Permanently added 'X.X.X.X' (ECDSA) to the list of known hosts.\r\nPermission denied (publickey).\r\n", "unreachable": true}
```

You previously deployed Algo to a hosting provider other than AWS, and Algo created an ECDSA keypair at that time. You are now deploying to AWS which [does not support ECDSA keys](https://aws.amazon.com/certificate-manager/faqs/) via their API. As a result, the deploy has failed.

In order to fix this issue, delete the `algo.pem` and `algo.pem.pub` keys from your `configs` directory and run the deploy again. If AWS is selected, Algo will now generate new RSA ssh keys which are compatible with the AWS API.

#### AWS: "Deploy the template fails" with CREATE\_FAILED

You tried to deploy Algo to AWS and you received an error like this one:

```text
TASK [cloud-ec2 : Make a cloudformation template] ******************************
changed: [localhost]

TASK [cloud-ec2 : Deploy the template] *****************************************
fatal: [localhost]: FAILED! => {"changed": true, "events": ["StackEvent AWS::CloudFormation::Stack algopvpn1 ROLLBACK_COMPLETE", "StackEvent AWS::EC2::VPC VPC DELETE_COMPLETE", "StackEvent AWS::EC2::InternetGateway InternetGateway DELETE_COMPLETE", "StackEvent AWS::CloudFormation::Stack algopvpn1 ROLLBACK_IN_PROGRESS", "StackEvent AWS::EC2::VPC VPC CREATE_FAILED", "StackEvent AWS::EC2::VPC VPC CREATE_IN_PROGRESS", "StackEvent AWS::EC2::InternetGateway InternetGateway CREATE_FAILED", "StackEvent AWS::EC2::InternetGateway InternetGateway CREATE_IN_PROGRESS", "StackEvent AWS::CloudFormation::Stack algopvpn1 CREATE_IN_PROGRESS"], "failed": true, "output": "Problem with CREATE. Rollback complete", "stack_outputs": {}, "stack_resources": [{"last_updated_time": null, "logical_resource_id": "InternetGateway", "physical_resource_id": null, "resource_type": "AWS::EC2::InternetGateway", "status": "DELETE_COMPLETE", "status_reason": null}, {"last_updated_time": null, "logical_resource_id": "VPC", "physical_resource_id": null, "resource_type": "AWS::EC2::VPC", "status": "DELETE_COMPLETE", "status_reason": null}]}
```

Algo builds a [Cloudformation](https://aws.amazon.com/cloudformation/) template to deploy to AWS. You can find the entire contents of the Cloudformation template in `configs/algo.yml`. In order to troubleshoot this issue, login to the AWS console, go to the Cloudformation service, find the failed deployment, click the events tab, and find the corresponding "CREATE\_FAILED" events. Note that all AWS resources created by Algo are tagged with `Environment => Algo` for easy identification.

In many cases, failed deployments are the result of [service limits](http://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html) being reached, such as "CREATE\_FAILED AWS::EC2::VPC VPC The maximum number of VPCs has been reached." In these cases, you must either [delete the VPCs from previous deployments](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/working-with-vpcs.html#VPC_Deleting), or [contact AWS support](https://console.aws.amazon.com/support/home?region=us-east-1#/case/create?issueType=service-limit-increase&limitType=service-code-direct-connect) to increase the limits on your account.

#### AWS: not authorized to perform: cloudformation:UpdateStack

You tried to deploy Algo to AWS and you received an error like this one:

```text
TASK [cloud-ec2 : Deploy the template] *****************************************
fatal: [localhost]: FAILED! => {"changed": false, "failed": true, "msg": "User: arn:aws:iam::082851645362:user/algo is not authorized to perform: cloudformation:UpdateStack on resource: arn:aws:cloudformation:us-east-1:082851645362:stack/algo/*"}
```

This error indicates you already have Algo deployed to Cloudformation. Need to [delete it](https://github.com/trailofbits/algo/blob/master/docs/cloud-amazon-ec2.md#cleanup) first, then re-deploy.

#### DigitalOcean: error tagging resource

You tried to deploy Algo to DigitalOcean and you received an error like this one:

```text
TASK [cloud-digitalocean : Tag the droplet] ************************************
failed: [localhost] (item=staging) => {"failed": true, "item": "staging", "msg": "error tagging resource '73204383': param is missing or the value is empty: resources"}
failed: [localhost] (item=dbserver) => {"failed": true, "item": "dbserver", "msg": "error tagging resource '73204383': param is missing or the value is empty: resources"}
```

The error is caused because Digital Ocean changed its API to treat the tag argument as a string instead of a number.

1. Download [doctl](https://github.com/digitalocean/doctl)
2. Run `doctl auth init`; it will ask you for your token which you can get \(or generate\) on the API tab at DigitalOcean
3. Once you are authorized on DO, you can run `doctl compute tag list` to see the list of tags
4. Run `doctl compute tag delete environment:algo --force` to delete the environment:algo tag
5. Finally run `doctl compute tag list` to make sure that the tag has been deleted
6. Run algo as directed

#### Windows: The value of parameter linuxConfiguration.ssh.publicKeys.keyData is invalid

You tried to deploy Algo from Windows and you received an error like this one:

```text
TASK [cloud-azure : Create an instance].
fatal: [localhost]: FAILED! => {"changed": false,
"msg": "Error creating or updating virtual machine AlgoVPN - Azure Error:
InvalidParameter\n
Message: The value of parameter linuxConfiguration.ssh.publicKeys.keyData is invalid.\n
Target: linuxConfiguration.ssh.publicKeys.keyData"}
```

This is related to [the chmod issue](https://github.com/Microsoft/WSL/issues/81) inside /mnt directory which is NTFS. The fix is to place Algo outside of /mnt directory.

#### Docker: Failed to connect to the host via ssh

You tried to deploy Algo from Docker and you received an error like this one:

```text
Failed to connect to the host via ssh:
Warning: Permanently added 'xxx.xxx.xxx.xxx' (ECDSA) to the list of known hosts.\r\n
Control socket connect(/root/.ansible/cp/6d9d22e981): Connection refused\r\n
Failed to connect to new control master\r\n
```

You need to add the following to the ansible.cfg in repo root:

```text
[ssh_connection]
control_path_dir=/dev/shm/ansible_control_path
```

#### Wireguard: Unable to find 'configs/...' in expected paths

You tried to run Algo and you received an error like this one:

```text
TASK [wireguard : Generate public keys] ********************************************************************************
[WARNING]: Unable to find 'configs/xxx.xxx.xxx.xxx/wireguard//private/dan' in expected paths.

fatal: [localhost]: FAILED! => {"msg": "An unhandled exception occurred while running the lookup plugin 'file'. Error was a <class 'ansible.errors.AnsibleError'>, original message: could not locate file in lookup: configs/xxx.xxx.xxx.xxx/wireguard//private/dan"}
```

This error is usually hit when using the local install option on a server that isn't Ubuntu 18.04. You should upgrade your server to Ubuntu 18.04. If this doesn't work, try removing `*.lock` files at /etc/wireguard/ as follows:

```text
sudo rm -rf /etc/wireguard/*.lock
```

Then immediately re-run `./algo`.

#### Ubuntu Error: "unable to write 'random state'" when generating CA password

When running Algo, you received an error like this:

```text
TASK [common : Generate password for the CA key] ***********************************************************************************************************************************************************
fatal: [xxx.xxx.xxx.xxx -> localhost]: FAILED! => {"changed": true, "cmd": "openssl rand -hex 16", "delta": "0:00:00.024776", "end": "2018-11-26 13:13:55.879921", "msg": "non-zero return code", "rc": 1, "start": "2018-11-26 13:13:55.855145", "stderr": "unable to write 'random state'", "stderr_lines": ["unable to write 'random state'"], "stdout": "xxxxxxxxxxxxxxxxxxx", "stdout_lines": ["xxxxxxxxxxxxxxxxxxx"]}
```

This happens when your user does not have ownership of the `$HOME/.rnd` file, which is a seed for randomization. To fix this issue, give your user ownership of the file with this command:

```text
sudo chown $USER:$USER $HOME/.rnd
```

Now, run Algo again.



