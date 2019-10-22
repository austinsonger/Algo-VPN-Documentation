# User Management

_If you chose to save the CA key during the deploy process,_ then Algo's own scripts can easily add and remove users from the VPN server.

1. Update the `users` list in your `config.cfg`
2. Open a terminal, `cd` to the algo directory, and activate the virtual environment with `source .env/bin/activate`
3. Run the command: `./algo update-users`

After this process completes, the Algo VPN server will contain only the users listed in the `config.cfg` file.

