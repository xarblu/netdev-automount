# netdev-automount
Automatically (un)mount network filesystems.

Uses existing mount configuration from `/etc/fstab`.

## Dependencies
`>=python-3.11`  
`mount`, `umount` and `mountpoint` (package `util-linux`)  
`getent` (package `glibc`)  
`ping` (package `iputils`)  
`NetworkManager` (required for auto-features)

## Installation
### A) Package  
If you're using Gentoo Linux you can install this from my overlay:  
`# eselect repository enable xarblu-overlay`  
`# emerge net-fs/netdev-automount`

### B) Manual Install  
Clone this repo  
`$ git clone https://github.com/xarblu/netdev-automount.git`  
Then make the script executable  
`$ chmod +x netdev-automount.py`  
If you want to use the NetworkManager auto features do:  
`sudo mkdir -p /etc/NetworkManager/dispatcher.d/pre-down.d`  
`sudo cp netdev-automount.py /etc/NetworkManager/dispatcher.d/30-netdev-automount.py`  
`sudo ln -s ../netdev-automount.py /etc/NetworkManager/dispatcher.d/pre-down.d/30-netdev-automount.py`  
`sudo systemctl enable NetworkManager-dispatcher.service`

## Behaviour/Usage
If called directly checks which hosts from `/etc/fstab` are reachable via ping and mount them.

If called from NetworkManager from `/{etc,usr/lib}/NetworkManager/dispatcher.d`  
assumes it is called as a dispatcher event.

## Configuration
The script pulls all mount data from `/etc/fstab` and thus shouldn't need any more configuration  
for basic usage. You may want to remove `x-systemd.automount` related options and add `noauto`  
just to avoid clashing with system automounting.

To use NetworkManager-dispatcher features configure hosts associated with connections in  
`/etc/nm-netdev-automount.toml` (created by script if not present).  
By default no (un)mount actions will be performed unless hosts are explicitly configured. 

`<connection>` is either UUID or NAME of the connection as given by `nmcli connection show`.  
The first match will be used and UUID is preferred.

`hosts` are as configured in fstab.
```
# [<connection>]  
# hosts = [ 'host_1', 'host_2', 'host_3' ]
```
