# Proxmox Common

## Export Import Container

    vzdump 112 --mode stop --compress zstd --dumpdir /var/lib/vz/dump/
    ### Add key to another proxmox server temporarily
    scp ./vzdump-lxc-112-...tar.zst root@<IP>:/var/lib/vz/dump/
    pct restore <NEW CT ID> /var/lib/vz/dump/vzdump-lxc-112-...tar.zst --storage local-lvm

