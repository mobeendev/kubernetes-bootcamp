#Terminal Commands to setup NFS Server and Client:
-------------------------------------

## Run on Server:
```
sudo apt install nfs-kernel-server nfs-common rpcbind
sudo mkdir -p nfs/storage/server/public
sudo chmod -R 777 nfs/storage/server/public
sudo vim /etc/exports
```

```
nfs/storage/server/public (rw,sync,no_subtree_check)
OR
nfs/storage/server/public *(rw,sync,no_subtree_check)
OR
/nfs/storage/server/public 172.16.56.146(rw,sync,no_subtree_check)
```
Allow Firewall
```
sudo ufw allow nfs
sudo reboot
showmount -e 
```


## Run on Client:
```
sudo mkdir nfs/storage/client
sudo chmod 777 nfs/storage/client
sudo apt install rpcbind nfs-common
sudo mount Server_IP_ADDRESS:/nfs/storage/server/public nfs/storage/client
```

e.g:
sudo mount 172.16.56.148:/home/tom/nfs/storage/server/public /home/jerry/nfs/storage/client


## Run on Server:
to check connected machines to server
```
sudo apt install net-tools   <<<<------- first install required tools
netstat | grep :nfs
```
