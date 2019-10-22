# SSH Tunnel

If you turned on the optional SSH tunneling role, then local user accounts will be created for each user in `config.cfg` and SSH authorized\_key files for them will be in the `configs` directory \(user.ssh.pem\). SSH user accounts do not have shell access, cannot authenticate with a password, and only have limited tunneling options \(e.g., `ssh -N` is required\). This ensures that SSH users have the least access required to setup a tunnel and can perform no other actions on the Algo server.

Use the example command below to start an SSH tunnel by replacing `user` and `ip` with your own. Once the tunnel is setup, you can configure a browser or other application to use 127.0.0.1:1080 as a SOCKS proxy to route traffic through the Algo server.

`ssh -D 127.0.0.1:1080 -f -q -C -N user@ip -i configs/<server_ip>/ssh-tunnel/<user>.pem`

### SSH into Algo Server

Your Algo server is configured for key-only SSH access for administrative purposes. Open the Terminal app, `cd` into the `algo-master` directory where you originally downloaded Algo, and then use the command listed on the success message:

`ssh -i configs/algo.pem user@ip`

where `user` is either `root` or `ubuntu` as listed on the success message, and `ip` is the IP address of your Algo server. If you find yourself regularly logging into the server then it will be useful to load your Algo ssh key automatically. Add the following snippet to the bottom of `~/.bash_profile` to add it to your shell environment permanently.

`ssh-add ~/.ssh/algo > /dev/null 2>&1`

