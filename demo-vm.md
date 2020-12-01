---
layout: wiki
title: Demo VM
---
## Demo VM

### Requirements

 - Public key configured with your GitHub account
 - Agent forwarding enabled
 - Vagrant
 - VirtualBox
 - Windows users will probably need [vagrant-vbguest](https://github.com/dotless-de/vagrant-vbguest) plugin to make shared directories work

### Usage

 1. Download or clone `https://github.com/lizards-and-pumpkins/dev-vm` repository
 2. Change to repository directory and run `vagrant up`
 3. Add `192.168.56.121 demo.lizardsandpumpkins.com.loc` to your local hosts file
 4. Navigate your browser to http://demo.lizardsandpumpkins.com.loc/

### Troubleshooting

**Problem:** Shared directory could not be mounted during `vagrant up` command with following error:

> // TODO: Add error message

**Solution:** Install [vagrant-vbguest](https://github.com/dotless-de/vagrant-vbguest) plugin.
