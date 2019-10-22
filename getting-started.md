# Getting Started

1. **Get a copy of Algo.** The Algo scripts will be installed on your local system. There are two ways to get a copy:
   * Download the [ZIP file](https://github.com/trailofbits/algo/archive/master.zip). Unzip the file to create a directory named `algo-master` containing the Algo scripts.
   * Run the command `git clone https://github.com/trailofbits/algo.git` to create a directory named `algo` containing the Algo scripts.
2. **Install Algo's core dependencies.** Algo requires that **Python 3** and at least one supporting package are installed on your system.
   * **macOS:** Apple does not provide Python 3 with macOS. There are two ways to obtain it:

     * Use the [Homebrew](https://brew.sh/) package manager. After installing Homebrew install Python 3 by running `brew install python3`.
     * Download and install the latest stable [Python 3 package](https://www.python.org/downloads/mac-osx/). Be sure to run the included _Install Certificates_ command from Finder.

     Once Python 3 is installed on your Mac, from Terminal run:

     ```text
     python3 -m pip install --upgrade virtualenv
     ```

   * **Linux:** Recent releases of Ubuntu, Debian, and Fedora come with Python 3 already installed. Make sure your system is up-to-date and install the supporting package\(s\):

     * Ubuntu and Debian:

     ```text
     sudo apt install -y python3-virtualenv
     ```

     * Fedora:

     ```text
     sudo dnf install -y python3-virtualenv
     ```

     * Red Hat and CentOS 7 and later \(for earlier versions see this documentation\):

     ```text
     sudo yum -y install epel-release
     sudo yum install -y python36-virtualenv
     ```

   * **Windows:** Use the Windows Subsystem for Linux \(WSL\) to create your own copy of Ubuntu running under Windows from which to install and run Algo. See the Windows documentation.
3. **Install Algo's remaining dependencies.** You'll need to run these commands from the Algo directory each time you download a new copy of Algo. In a Terminal window `cd` into the `algo-master` \(ZIP file\) or `algo` \(`git clone`\) directory and run:

   ```text
   python3 -m virtualenv --python="$(command -v python3)" .env &&
     source .env/bin/activate &&
     python3 -m pip install -U pip virtualenv &&
     python3 -m pip install -r requirements.txt
   ```

   On Fedora add the option `--system-site-packages` to the first command above. On macOS install the C compiler if prompted.

4. **List the users to create.** Open the file `config.cfg` in your favorite text editor. Specify the users you wish to create in the `users` list. Create a unique user for each device you plan to connect to your VPN. If you want to be able to add or delete users later, you **must** select `yes` at the `Do you want to retain the keys (PKI)?` prompt during the deployment.
5. **Start the deployment.** Return to your terminal. In the Algo directory, run `./algo` and follow the instructions. There are several optional features available. None are required for a fully functional VPN server. These optional features are described in greater detail in here.

That's it! You will get the message below when the server deployment process completes. Take note of the p12 \(user certificate\) password and the CA key in case you need them later, **they will only be displayed this time**.

