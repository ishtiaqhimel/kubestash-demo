# Installing NFS server
## Step-1: Update and Upgrade the System
Before installing any software, it's always a good practice to update system. To do this, open terminal and run following command 
```bash
$ sudo apt update && sudo apt upgrade
```
## Step-2: Install NFS Server
To install NFS server package, run following command
```bash
$ sudo apt install nfs-kernel-server
```
# Configuring NFS server
## Step-1: Make shared NFS directory
First of all, we will create a directory named `shared` that is going to be shared by all client systems. To do so, write out the following command
```bash
$ sudo mkdir /shared
```
## Step-2: Set directory permissions
Next, set the permissions of the created `shared` directory so that all client machines can easily access it
```bash
$ sudo chown -R nobody:nogroup /shared/
```
## Step-3: Set file permissions
Set the file permissions as required. In our case, we have allocated the read, write, and execute permissions to the “nfs_share” directory files
```bash
$ sudo chmod 777 /shared/
```
## Step-4: Configure NFS Exports
Once NFS server package is installed, we need to configure NFS exports. Exports are directories or files that are shared with remote clients. To configure exports, we need to edit exports file located in /etc/exports directory
```bash
$ sudo nano /etc/exports
```
Now, it is up to you whether you want to grant access to the entire subnet, single or multiple clients. For instance, we will permit an entire subnet `10.0.2.15/24` to access the NFS share
```bash
$ /shared 10.0.2.15/24(rw,sync,no_subtree_check)
```
- `rw`: Grants read and write access.
- `sync`: Data is written to the disk synchronously, which makes it slower but safer.
- `no_subtree_check`: Disables subtree checking, simplifying permissions.

We can also use an asterisk (*) instead of `<IP_or_subnet>` to allow any client to connect. However, this is not recommended for security reasons.
## Step-5: Exporting NFS directory
After editing the /etc/exports file, we need to export them to make them available to remote clients. Apply the changes and export the shared directory by running the following command
```bash
$ sudo exportfs -a
```
This command exports all directories listed in `/shared` file.
## Step-6: Start the NFS Server
Start the NFS server service
```bash
$ sudo systemctl start nfs-kernel-server
```
## Step-7: Grant Firewall access
Next, grant the Firewall access to the client system with the following “ufw” command
```bash
$ sudo ufw allow from 10.0.2.15/24 to any port nfs
```
## Step-8: Enable Firewall
Enable Firewall with “ufw” command and “enable” option
```bash
$ sudo ufw enable
```
## Step-9: Check Firewall status
Next, verify that the Firewall is configured to allow the access through the port `2049`
```bash
$ sudo ufw status
```
## Step-9: Check NFS Server status
You can check the status of the NFS server to ensure it's running without errors
```bash
$ sudo systemctl status nfs-kernel-server
```
