[![Build Status](https://travis-ci.org/madbuda/brasscrow.svg?branch=master)](https://travis-ci.org/madbuda/brasscrow)
BrassCrow
=========

* A single command sets up a brand new Ubuntu 16.04 server running a [wide variety of anti-censorship software](#services-provided) that can completely mask and encrypt all of your Internet traffic.
* BrassCrow natively supports the creation of new servers at [Amazon EC2](https://aws.amazon.com/ec2/), [DigitalOcean](https://www.digitalocean.com/),  [Exoscale](https://www.exoscale.ch), [Google Compute Engine](https://cloud.google.com/compute/). It also runs on any Ubuntu 16.04 server regardless of provider, and **hundreds** of instances can be configured simultaneously using this method.
* The process is completely automated and only takes about ten minutes.
* Once your BrassCrow server is running, you can give the custom connection instructions to friends, family members, and fellow activists. The connection instructions contain an embedded copy of the server's unique SSL certificate, so you only have to send them a single file.
* Each server is entirely self-contained and comes with absolutely everything that users need to get started, including cryptographically verified mirrors of all common clients. This renders any attempted censorship of default download locations completely ineffective.


More Features
-------------
* Nginx powers a password-protected and encrypted Gateway that serves as the starting point for new users. The Gateway is accessible over SSL, or as a Tor [hidden service](https://www.torproject.org/docs/hidden-services.html.en).
  * Step by step client configuration instructions are generated for each new server that BrassCrow creates. Users can quickly access these instructions through any web browser. 
  * The integrity of mirrored software is ensured using SHA-256 checksums, or by verifying GPG signatures if the project provides them. This protects users from downloading corrupted files.
  * Current Tor users can take advantage of the additional services BrassCrow sets up in order to transfer large files or to handle other traffic (e.g. BitTorrent) that isn't appropriate for the Tor network.
  * A unique password, SSL certificate, and SSL private key are generated for each BrassCrow Gateway. The Gateway instructions and certificate are transferred via SSH at the conclusion of BrassCrow's execution.
* Distinct services and multiple daemons provide an enormous amount of flexibility. If one connection method gets blocked there are numerous options available, most of which are resistant to Deep Packet Inspection.
* All software runs on ports that have been deliberately chosen to make simplistic port blocking unrealistic without causing massive collateral damage. OpenVPN, for example, does not run on its default port of 1194, but instead uses port 636, the standard port for LDAP/SSL connections that are used by companies worldwide.
  * *L2TP/IPsec is a notable exception to this rule because the ports cannot be changed without breaking client compatibility*
* The IP addresses of connecting clients are never logged. There's nothing to find if a server gets seized or shut down.

<a name="services-provided"></a>
Services Provided
-----------------
* L2TP/IPsec using [Libreswan](https://libreswan.org/) and [xl2tpd](https://www.xelerance.com/software/xl2tpd/)
  * A randomly chosen pre-shared key and password are generated.
  * Windows, OS X, Android, and iOS users can all connect using the native VPN support that is built into each operating system without installing any additional software.
* [Monit](https://mmonit.com/monit/)
  * Monitors process health and automatically restarts services in the unlikely event that they crash or become unresponsive.
* [OpenSSH](http://www.openssh.com/)
  * An unprivileged forwarding user and SSH keypair are generated for [sshuttle](https://github.com/sshuttle/sshuttle) and SOCKS capabilities.
  * Windows and Android SSH tunnels are also supported, and a copy of the keypair is exported in the .ppk format that PuTTY requires.
  * [Tinyproxy](https://banu.com/tinyproxy/) is installed and bound to localhost. It can be accessed over an SSH tunnel by programs that do not natively support SOCKS and that require an HTTP proxy, such as Twitter for Android.
* [OpenConnect](http://www.infradead.org/ocserv/index.html) / [Cisco AnyConnect](http://www.cisco.com/c/en/us/products/security/anyconnect-secure-mobility-client/index.html)
  * OpenConnect (ocserv) is an extremely high-performance and lightweight VPN server that also features full compatibility with the official Cisco AnyConnect clients.
  * The [protocol](http://www.infradead.org/ocserv/technical.html) is built on top of standards like HTTP, TLS, and DTLS, and it's one of the most popular and widely used VPN technologies among large multi-national corporations.
    * This means that in addition to its ease-of-use and speed, OpenConnect is also highly resistant to censorship and is almost never blocked.
* [OpenVPN](https://openvpn.net/index.php/open-source.html)
  * Self-contained "unified" .ovpn profiles are generated for easy client configuration using only a single file.
  * Both TCP and UDP connections are supported.
  * Multiple clients can easily share the same certificates and keys, but five separate sets are generated by default.
  * Client DNS resolution is handled via [Dnsmasq](http://www.thekelleys.org.uk/dnsmasq/doc.html) to prevent DNS leaks.
  * TLS Authentication is enabled which helps protect against active probing attacks. Traffic that does not have the proper HMAC is simply dropped.
* [Shadowsocks](http://shadowsocks.org/en/index.html)
  * The high-performance [libev variant](https://github.com/shadowsocks/shadowsocks-libev) is installed. This version is capable of handling thousands of simultaneous connections.
  * A QR code is generated that can be used to automatically configure the Android and iOS clients by simply taking a picture. You can tag '8.8.8.8' on that concrete wall, or you can glue the Shadowsocks instructions and some QR codes to it instead!
  * [One-time Authentication](https://shadowsocks.org/en/spec/one-time-auth.html) (OTA) is enabled for enhanced security and improved GFW evasion.
* [sslh](http://www.rutschle.net/tech/sslh.shtml)
  * Sslh is a protocol demultiplexer that allows Nginx, OpenSSH, and OpenVPN to share port 443. This provides an alternative connection option and means that you can still route traffic via OpenSSH and OpenVPN even if you are on a restrictive network that blocks all access to non-HTTP ports.
* [Stunnel](https://www.stunnel.org/index.html)
  * Listens for and wraps OpenVPN connections. This makes them look like standard SSL traffic and allows OpenVPN clients to successfully establish tunnels even in the presence of Deep Packet Inspection.
  * Unified profiles for stunnel-wrapped OpenVPN connections are generated alongside the direct connection profiles. Detailed instructions are also generated.
  * The stunnel certificate and key are exported in PKCS #12 format so they are compatible with other SSL tunneling applications. Notably, this enables [OpenVPN for Android](https://play.google.com/store/apps/details?id=de.blinkt.openvpn) to tunnel its traffic through [SSLDroid](https://play.google.com/store/apps/details?id=hu.blint.ssldroid). OpenVPN in China on a mobile device? Yes!
* [Tor](https://www.torproject.org/)
  * A [bridge relay](https://www.torproject.org/docs/bridges) is set up with a random nickname.
  * [Obfsproxy](https://www.torproject.org/projects/obfsproxy.html.en) is installed and configured with support for the obfs4 pluggable transport.
  * A BridgeQR code is generated that can be used to automatically configure [Orbot](https://play.google.com/store/apps/details?id=org.torproject.android) for Android.
* [UFW](https://wiki.ubuntu.com/UncomplicatedFirewall)
  * Firewall rules are configured for every service, and any traffic that is sent to an unauthorized port will be blocked.
* [unattended-upgrades](https://help.ubuntu.com/community/AutomaticSecurityUpdates)
  * Your BrassCrow server is configured to automatically install new security updates.
* [WireGuard](https://www.wireguard.io/)
  * Linux users can take advantage of this next-gen, simple, kernel-based, state-of-the-art VPN that also happens to be ridiculously fast.
  * In addition to the public-key crypto that WireGuard uses by default, a pre-shared key is also configured to help provide post-quantum resistance. Use the VPN of the future with an eye towards the future!

Installation
------------
Please read all installation instructions **carefully** before proceeding.

### Important Clarification ###
BrassCrow is based on [Ansible](http://www.ansible.com/home), an automation tool that is typically used to provision and configure files and packages on remote servers. BrassCrow automatically sets up **a remote server** with the VPN packages and configuration.

BrassCrow will spin up and deploy **a server** on your chosen hosting provider when you run **on your home machine** (e.g. your laptop). Usually, you **do not run BrassCrow on the remote server** as by default this would result in the deployment of another server from your server and render the first server redundant (whew!). Support for local provisioning (i.e. BrassCrow locally configuring the system on which it is installed) will be added soon.

### Prerequisites ###


* BrassCrow requires a BSD, Linux, or OS X system. Windows is not supported. 
* Python 2.7 is required. This comes standard on OS X, and is the default on almost all Linux and BSD distributions as well. If your distribution packages Python 3 instead, you will need to install version 2.7 in order for BrassCrow to work properly.
* Make sure an SSH key is present in ~/.ssh/id\_rsa.pub.
  * If you do not have an SSH key, you can generate one by using this command and following the defaults:

            ssh-keygen
* Install Git.
  * On Debian and Ubuntu

            sudo apt-get install git
  * On Fedora

            sudo yum install git
  * On OS X (via [Homebrew](http://brew.sh/))

            brew install git
* Install the [pip](https://pip.pypa.io/en/latest/) package management system for Python.
  * On Debian and Ubuntu (also installs the dependencies that are necessary to build Ansible and that are required by some modules)

            sudo apt-get install python-paramiko python-pip python-pycurl python-dev build-essential
  * On Fedora

            sudo yum install python-pip
  * On OS X

            sudo easy_install pip
            sudo pip install pycurl
            
* Install [Ansible](http://www.ansible.com/home).
  * On OS X (via [Homebrew](http://brew.sh/))

            brew update && brew install ansible
  * On BSD or Linux (via pip)

            sudo pip install ansible markupsafe
* Install the necessary Python libraries for your chosen cloud provider.
  * Amazon EC2

            sudo pip install boto
  * DigitalOcean

            sudo pip install dopy==0.3.5
  * Google

            sudo pip install "apache-libcloud>=0.17.0"
  * Exoscale
 
         sudo pip install cs
 

  * If you are using a Homebrew-installed version of Python you should also run these commands to make sure it can find the necessary libraries:

            mkdir -p ~/Library/Python/2.7/lib/python/site-packages
            echo '/usr/local/lib/python2.7/site-packages' > ~/Library/Python/2.7/lib/python/site-packages/homebrew.pth

### Execution ###
1. Clone the BrassCrow repository and enter the directory.

        git clone git@github.com:madbuda/brasscrow.git && cd brasscrow
2. Execute the BrassCrow script.

        ./BrassCrow
3. Follow the prompts to choose your provider, the physical region for the server, and its name. You will also be asked to enter API information.
4. Once login information and API keys are entered, BrassCrow will begin spinning up a new remote server.
5. Wait for the setup to complete (this usually takes around ten minutes) and look for the corresponding files in the 'generated-docs' folder in the BrassCrow repository directory. The HTML file will explain how to connect to the Gateway over SSL, or via the Tor hidden service. All instructions, files, mirrored clients, and keys for the new server can then be found on the Gateway. You are all done!

### Running BrassCrow on Other Providers ###

You can also run BrassCrow on any number of new Ubuntu 16.04 servers. Dedicated hardware? Great! Esoteric cloud provider? Awesome! To do this, simply edit the `inventory` file and uncomment the final two lines. Replace the sample IP with the address (or addresses) of the servers you wish to configure. Make sure you read through all of the documentation in the `inventory` file and update the `ansible.cfg` file if necessary. Then run the BrassCrow playbook directly:

    ansible-playbook playbooks/BrassCrow.yml

The servers should be accessible using SSH keys, and *root* is used as the connecting user by default (though this is simple to change while editing the inventory file).



