# NFS Server

Install NFS Server:
```bash
sudo apt install nfs-kernel-server
```

Check status for NFS server:
```bash
sudo systemctl status nfs-server
```

Edit mount path:
```bash
sudo vim /etc/exports
```

Share director:
```
/mnt 192.168.0.0/24 (rw,sync,no_subtree_check)
```

Export file system for start sharing:
```bash
sudo exportfs -a
```
