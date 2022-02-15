# CENTRAL.INOVA.PT

# 🖥️ Terminal (Termius/Putty)

◽ Change your hostname -> ` sudo hostnamectl set-hostname central.inova.pt`

◽ Change to Superuser -> `sudo su - `

◽ Update the machine ➡️ `apt update && apt -y upgrade`


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

## Certificates

◽ Its needed to add the ca in the server so you can verify your certificates
◽ Go to ➡️ `cd /usr/local/share/ca-certificates/` 
◽ Add your ca.crt ➡️ `nano ca.crt` and put the ca from control.inova.pt
◽ Make sure to update the ca ➡️ `update-ca-certificates`

# 💽 RAID

## RAID > LUKS > LVM

## RAID

◽ First its needed to make the partition of the disks ➡️ ` gdisk /dev/xvdf` and do the same for xvdg and xvdh 
      ▶️ ` gdisk /dev/xvdf` ➡️  o (new empty GUID partition table (GPT)(to clean all the partitions done/to confirm its clean) ➡️ n (new partition) ➡️ fd00 this option is done
      on after chossing the side of the partition ➡️ w to save the changes ➡️ Y (to confirm all the options).
      
◽ Now define the which Raid you going to use ⤵️in this machine was made RAID5 

`mdadm --create /dev/md0 --level 5 --raid-devices 3 /dev/xvdf1 /dev/xvdg1 /dev/xvdh1 /dev/xvdi1` ⤵️
(here you need to put all the partitions you want to use on the Raid, and since its an RAID5 you need atleast 3)(md0 is a name that you choose)


✔️Check the state of the RAID ➡️ `mdadm --detail /dev/md0` (after you do the command wait to reach 100%,do the command again until it shows that)

```
  State : clean
	Active Devices : 4
	Working Devices : 4
	Failed Devices : 0
	Spare Devices : 0
``` 


## LUCKS

this configurations is made so you can encrypt the volume

➡️ `cryptsetup luksFormat --hash=sha512 --key-size=512 --cipher=aes-xts-plain64 --verify-passphrase /dev/md0` 
(here you encrypting the md0(the keysize it can different there is other type of key sizes)

➡️ YES and write the Password you want, be carefull this will override data on /dev/md0 
➡️ `cryptsetup luksOpen /dev/md0 md0_crypt` (md0_crypt this can be any name you want)

## LVM

◽ Create a Physical Volume ➡️ `pvcreate /dev/mapper/md0_crypt` 

◽ Create a Volume Group ➡️ `vgcreate vg0 /dev/mapper/md0_crypt`

◽ Create Logical Volume ➡️ `lvcreate -n lv0 -l +100%FREE vg0`  (this one was made with 100% of the disk memory)


◽ Define which Fylt System you going to use ➡️ mkfs.xfs /dev/vg0/lv0 


◽ Make a file for the mount ➡️ `mkdir /mnt/raid5`( i choose raid5 as name) 

◽ Mount the Logical Volume to the Directory ➡️ `mount /dev/vg0/lv0 /mnt/raid5`

## Optional ( or you do this or you need to mount every time the Machine is rebooted)

◽ In this step you need to copy the line associated with the mount command ➡️ `cat /etc/mtab` 

◽ Paste the line inside `/etc/fstab` ➡️ add ,nofail ❗ if dont,and the configs are wrong a rescue machiine will be needed

# NFS-Server 

◽ Install the service ➡️ `apt install nfs-kernel-server`

◽ Go to the `etc/exports` and add `/mnt/raid5 *(rw,sync,no_subtree_check,no_root_squash)` ➡️ ( this is made to define which file will be exportent to the nfs clients)

◽ Export the file ➡️ `exportfs -a`

◽ Enable the service ➡️ `systemctl enable --now nfs-kernel-server`

◽ add the users for the clients ⤵️

```
adduser sales --home /mnt/raid5/sales
adduser marketing --home /mnt/raid5/marketing
```

## NFS Client will be done in Marketing and Sales


# NIS-Server 

◽ Install the service ➡️ ` apt -y install nis ` ➡️ Put your domain ( inova.pt)
 
◽ Go to `/etc/default/nis` ➡️ This step is made to declare if the server is master or slave or even a client

```
Change line#6 put NISSERVER=master

Change line#9 change yes to no
```

◽ Go to `/etc/ypserv.securenets` ➡️ This step is made to declare which are the subnets allowed on the nis server

```
Comment 0.0.0.0         0.0.0.0
ADD     255.255.255.0   172.31.80.0 

```

◽ Go to `nano /var/yp/Makefile` 

```
# Should we merge the passwd file with the shadow file ?
# MERGE_PASSWD=true|false
MERGE_PASSWD=true

# Should we merge the group file with the gshadow file ?
# MERGE_GROUP=true|false
MERGE_GROUP=true
```

◽ Go to `/etc/hosts` ➡️ This Step is made to tell which is your hosts ❗ this step is necessary for NIS to work

◽ Restart the service ➡️ `systemctl restart rpcbind nis`

◽ Update your NIS database ` /usr/lib/yp/ypinit -m` ➡️ Ctrl+d ➡️ Y

◽ If you added users in local server, apply them to NIS database ⤵️

```
cd /var/yp
make
```

## NIS-Client ( Will be done in Marketing and Sales)


# Mail Server (POSTFIX+DOVECOT)

◽ Install the Service ➡️ ` apt install postfix sasl2-bin` ➡️ Select Internet Site ➡️ put your domain (inova.pt)


◽ Reconfigurate postfix ➡️ `dpkg-reconfigure postfix` ➡️ select 2nd option ➡️ put your domain ➡️ "ubuntu" ➡️ in this option dont change anything ➡️ "no" ➡️ in this option clean all ➡️ "0" ➡️ "+" ➡️ and select ipv4,ipv6 or both

◽ Go to `nano /etc/default/saslauthd` 
```
START = yes
OPTIONS = “ copy line 54 and put it here”
```

## POSTFIX

◽ Go to `nano /etc/postfix/sasl/smtpd.conf` and Add ⤵️

```
put the next 2 lines inside

   pwcheck_method: saslauthd
   mech_list: PLAIN LOGIN
```

◽ To enable STARTTLS and SSL go to ➡️ `nano /etc/postfix/master.cf` ❗
```
uncomment on the 1st group
     submission inet n       -       y       -       -       smtpd
     -o syslog_name=postfix/submission
     -o smtpd_tls_security_level=encrypt
     -o smtpd_sasl_auth_enable=yes
add this -> -o smtpd_client_restrictions=permit_sasl_authenticated,reject

   uncomment on 2nd group
   smtps     inet  n       -       y       -       -       smtpd
   -o syslog_name=postfix/smtps
   -o smtpd_tls_wrappermode=yes
   -o smtpd_sasl_auth_enable=yes
add this -> -o smtpd_client_restrictions=permit_sasl_authenticated,reject
```
◽ To add your certificates so you can use STARTTLS or SSL➡️ `nano /etc/postfix/main.cf` ➡️ and change the names for the names of your certificates 
                                                                                                                                                   ↩️
```
smtpd_tls_cert_file=/etc/ssl/certs/mail.inova.pt.crt
smtpd_tls_key_file=/etc/ssl/private/mail.inova.pt.key
```

## DOVECOT

◽To give Authorization➡️ `nano /etc/dovecot/conf.d/10-auth.conf`

   ```
   disable_plaintext_auth = yes -> uncomment and change yes -> no
```

◽ To define for which Directory your mails will go ➡️`nano /etc/dovecot/conf.d/10-mail.conf`

```
   uncomment mail_location = maildir:~/Maildir
   comment -> mail_location= mbox:~/mail :INBOX =/var/mail/%u
```

◽ To add your Certificates so you can use STARTTLS and SSL ➡️ `nano /etc/dovecot/conf.d/10-ssl.conf` 
```
ssl_cert = </etc/ssl/certs/mail.inova.pt.crt (change for the name of your certificate)
ssl_key = </etc/ssl/private/mail.inova.pt.key (change for the name of your certificate's key)
```
◽ To create automatic the Maildir everytime you create a user 
                                                               ↩️
```                                                              
cd  /etc/skel
do the next command -> maildirmake.dovecot Maildir
```

◽ To give permitions to postfix ➡️ `adduser postfix sasl` ❗
◽ To give permitions to dovecot ➡️ `adduser dovecot sasl` ❗


◽ Restart the system ➡️ `systemctl restart saslauthd postfix dovecot`

## Client ( will be done on Marketing and Sales)


































































































