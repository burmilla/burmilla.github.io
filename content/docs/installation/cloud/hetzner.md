---
bookToc: false
---
# Hetzner Cloud

BurmillaOS is available as an ISO Image

>**Note**: Deploying to Hetzner will incur charges.

## Creation
1. In the Hetzner Cloud portal, go to the project view.
1. Choose your project.
1. Click **Add Server.**
1. Select the Location.
1. Select any OS Images (e.g. Ubuntu 20.04) as this will be replaced later
1. Make sure your vServer has the [minimum hardware requirements for BurmillaOS](/#hardware-requirements).
1. Choose any options for network, volume, and backups.
1. Select or add an SSH key.
1. Provide a name to the server.
1. Click **Create & Buy now**

## Installation
1. Stop the server
1. Select the server and there select ISO Images
1. Mount the BurmillaOS Image
1. Start the Server and open the remote console
1. Install following the [bare metal](/docs/installation/install-to-disk) installation guide.
    ```bash
    ros install -d /dev/sda
    ```
1. After a successful installation stop the server and unmount the ISO
1. Reboot and enjoy !

> **Hint:** You can create a snapshot of the fresh install to use as a blueprint for further Servers

You can access the host via SSH after the Droplet is booted. The default user is `burmilla`.