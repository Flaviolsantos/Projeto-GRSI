# SALES.INOVA.PT

# 🖥️ Terminal (Termius/Putty)

◽ Change your hostname -> ` sudo hostnamectl set-hostname central.inova.pt`

◽ Change to Superuser -> `sudo su - `


## NAT

◽ Go to `nano /etc/netplan/50.ymal` and add ⤵️

```
dhcp4-overrides:
    use-dns: false
    use-routes: false
routes:
  - to: default
    via: 172.31.80.100
nameservers:
    addresses: [172.31.16.100]

```

◽ After the file above is edited do `netplan try` to save the configruations

✔️ To check if its working do ping 1.1.1.1

◽ Update the machine ➡️ `apt update && apt -y upgrade`

 
 ## NFS-Client
 
 ◽ Install Service ➡️ `apt install nfs-common`
 
 ◽ Make the Directory the same as the NFS server ➡️ `mkdir /mnt/raid5`
 
 ◽ Mount the directory from server to the Directory of Client ➡️ `mount -t nfs 172.31.80.101:/mnt/raid5 /mnt/raid5`
 
 ◽ Optional ➡️ if you want to save insted of mount everytime you reboot the machine
 
 ```
cat /etc/mtab -> copy the line associated to the mount
nano /etc/fstab -> paste inside and put ,nofail !!!!!!!
```

 
 ## NIS-Client 
 
◽ Go to `/etc/hosts` ➡️ and put the same as the nis server

◽ Install the Service ➡️ `apt install nis -y` ➡️ put your domain (inova.pt)
 
◽ Go to `nano /etc/yp.conf` ⤵️ add so the client know who is the server

```
domain inova.pt server sales.inova.pt

```
◽ Go to `nano /etc/nsswitch.conf` ⤵️ add "nis" like the lines below
   
 ```
 # line 7: add like follows
passwd:         files systemd nis
group:          files systemd nis
shadow:         files nis
gshadow:        files

hosts:          files dns nis
```
◽ Go to `nano /etc/pam.d/common-session` ➡️ This step is done in case there is no home directory its done automaticly
   ADD ➡️ `session optional        pam_mkhomedir.so skel=/etc/skel umask=077`


◽ Restart the Service ➡️ ` systemctl restart nis`

 ✔️ To check if its owrking try to login in any of the users added
 
 ## Graphic Interface

``` 
sudo apt install -y xfce4 xfce4-goodies

sudo apt install -y xrdp chromium-browser filezilla

sudo adduser xrdp ssl-cert

echo xfce4-session > ~/.xsession
```
◽ Give the users the permitions for the Graphic Interface
 
``` 
cp /home/ubuntu/.xsession /mnt/raid5/.xsession
cp /home/ubuntu/.xsession /mnt/raid5/sales/.xsession
cp /home/ubuntu/.xsession /mnt/raid5/marketing/.xsession
chown sales:sales /mnt/raid5/sales/.xsession
chown marketing:marketing /mnt/raid5/marketing/.xsession
```  

   



























