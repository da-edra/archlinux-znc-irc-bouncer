<div align="center">

![banner](banner.png)

# Hardened ZNC IRC bouncer with Tor

![forthebadge](https://forthebadge.com/images/badges/built-with-love.svg)
![forthebadge](https://forthebadge.com/images/badges/made-with-crayons.svg)
</div>

## About:

This repository creates a Hardened ZNC IRC Bouncer that uses Tor to connect to IRC networks like Freenode or OFTC.
Some of the information used to automate the creation of the IRC bouncer was taken from the amazing Tom Busby's article [Setting Up a ZNC IRC Bouncer to Use Tor](https://tom.busby.ninja/setting-up-znc-IRC-bouncer-to-use-tor/).

**ZNC**: [ZNC](https://wiki.znc.in/ZNC) is an advanced [IRC bouncer](http://en.wikipedia.org/wiki/BNC_%28software%29#IRC) that is left connected so an IRC client can disconnect/reconnect without losing the chat session. The configuration used in this repository allows multiple clients to connect to the ZNC server.

**Tor**: [Tor](https://www.torproject.org) enables anonymous communication by directing internet traffic through a free, worldwide, volunteer [overlay network](https://en.wikipedia.org/wiki/Overlay_network). The configuration in this repository forces ZNC's traffic to through the Tor network by using Proxychains so the IP address of the ZNC server remains hidden when connecting to IRC networks.

**Proxychains**: [Proxychains](https://github.com/rofl0r/proxychains-ng) preloader which hooks calls to sockets in dynamically linked programs and redirects it through one or more socks/http proxies. It is used in this repository to force ZNC to use the Tor network.

**Firewalld**: [Firewalld](https://firewalld.org/) provides a dynamically managed firewall with support for network/firewall zones that define the trust level of network connections or interfaces. It has support for IPv4, IPv6 firewall settings, ethernet bridges and IP sets. The configuration in this repository opens ports required for ZNC, SSH and Tor to work correctly.

**Packer**: [Packer](https://www.packer.io) is tool for creating identical machine images for multiple platforms from a single source configuration. A machine image is a single static unit that contains a pre-configured operating system and installed software which is used to quickly create new running machines. The configuration in this repository creates an [AMI](https://en.wikipedia.org/wiki/Amazon_Machine_Image) with ZNC, Tor, Proxychains and Firewalld installed and automates the configuration of these tools to work correctly.

**Terraform**: [Terraform](https://www.terraform.io/) is a tool for building, changing, and versioning infrastructure safely and efficiently. Terraform can manage existing and popular service providers as well as custom in-house solutions. The configuration in this repository creates an AWS EC2 t3.micro instance where the bouncer will be installed.

**AWS Free Tier**: The [AWS Free Tier](https://aws.amazon.com/free/) provides customers the ability to explore and try out AWS services free of charge up to specified limits for each service. The Free Tier is comprised of three different types of offerings, a 12-month Free Tier, an Always Free offer, and short term trials. The configuration in this repository allows you to have a free IRC bouncer for a whole year!

---

## Usage:

1. Configure the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html).
2. Install `packer` and `terraform` (ex. `pacman -S packer terraform`).
3. Create the AMI with `packer build`.
4. Export the `TF_VAR_ami_id` environment variable with the AMI-ID of the Machine you've just created (ex. `export TF_VAR_ami_id=<ami id>`) or enter the AMI ID during step 5.
5. Run `terraform apply`.
6. Once it has been deployed continue to the configuration section.

## Configuration (WIP):

1. Connect to the instance `ssh bouncie@<redacted> -p 45632`, you'll see the following when logging-in for the first time:

```
The authenticity of host '[<redacted>]:45632 ([<redacted>]:45632)' can't be established.
ED25519 key fingerprint is SHA256:Y/kl0Jtlog/xYXkGl4g4oN46M6uti8q43HkWa06/9lo.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[<redacted>]:45632' (ED25519) to the list of known hosts.
                .,aadd"'    `"bbaa,.
            ,ad8888P'          `Y8888ba,
         ,a88888888              88888888a,
       a88888888888              88888888888a
     a8888888888888b,          ,d8888888888888a
    d8888888888888888b,_    _,d8888888888888888b
   d88888888888888888888888888888888888888888888b
  d8888888888888888888888888888888888888888888888b
 I888888888888888888888888888888888888888888888888I
,88888888888888888888888888888888888888888888888888,
I8888888888888888PY8888888PY88888888888888888888888I
8888888888888888"  "88888"  "88888888888888888888888
8::::::::::::::'    `:::'    `:::::::::::::::::::::8
Ib:::::::::::"        "        `::::::' `:::::::::dI
`8888888888P            Y88888888888P     Y88888888'
 Ib:::::::'              `:::::::::'       `:::::dI
  Yb::::"                  ":::::"           "::dP
   Y88P                      Y8P               `P
    Y'                        "
                                `:::::::::::;8"
       "888888888888888888888888888888888888"
         `"8;::::::::::::::::::::::::::;8"'
            `"Ya;::::::::::::::::::;aP"'
                ``""YYbbaaaaddPP""''
You are required to change your password immediately (root enforced)
WARNING: Your password has expired.
You must change your password now and login again!
Changing password for user bouncie.
New password:
```

Upon first login you're required to set up a password for the `bouncie` user, you'll be disconnected after setting up the password.
3. Connect to the instance again, enable two-step authentication by running the [TOTP playbook](ansible/totp.yml) `ansible-playbook totp.yml` to enable *Time-based One-Time* passwords to SSH into the instance.
4. For security reasons the ZNC dashboard is only accessible from the instance that is running ZNC, in order to access the ZNC dashboard from your local machine you need to create an SSH tunel where you bind the port of the machine to a port on your machine ex. `ssh -N -L 8000:localhost:8000 bouncie@<redacted> -p 45632` then, you can access the dashboard from your web browser using the new bind port ex. `https://localhost:8000`.
5. Keep configuring ZNC to suit your needs using the dashboard.

## Next steps:

0. Map OFTC Tor hidden service to Proxychains.
1. Configure Fail2Ban.
2. Configure SELinux.
3. Enable ZNC dashboard via SSH tunneling (currently the dashboard is disabled for security reasons).
4. Enable a monitoring service (probably Grafana?).
5. Investigate a way to automatically configure ZNC.
6. Hack the planet.
