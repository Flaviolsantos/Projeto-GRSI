# WAZUH.INOVA.PT

# 🖥️ Terminal (Termius/Putty)

◽ Change your hostname -> ` sudo hostnamectl set-hostname wazuh.inova.pt`

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

◽ First its needed to make the partition of the disks ➡️ ` gdisk /dev/xvdi` and do the same for xvdj and xvdk xvdl
      ▶️ ` gdisk /dev/xvdi` ➡️  o (new empty GUID partition table (GPT)(to clean all the partitions done/to confirm its clean) ➡️ n (new partition) ➡️ fd00 this option is done
      on after chossing the side of the partition ➡️ w to save the changes ➡️ Y (to confirm all the options).
      
◽ Now define the which Raid you going to use ⤵️in this machine was made RAID6 

`mdadm --create /dev/md0 --level 6 --raid-devices 4 /dev/xvdf1 /dev/xvdg1 /dev/xvdh1 /dev/xvdi1` ⤵️
(here you need to put all the partitions you want to use on the Raid, and since its an RAID6 you need atleast 4)(md0 is a name that you choose)


✔️Check the state of the RAID ➡️ `mdadm --detail /dev/md1` (after you do the command wait to reach 100%,do the command again until it shows that)

```
  State : clean
	Active Devices : 4
	Working Devices : 4
	Failed Devices : 0
	Spare Devices : 0
``` 


## LUCKS

this configurations is made so you can encrypt the volume

➡️ `cryptsetup luksFormat --hash=sha512 --key-size=512 --cipher=aes-xts-plain64 --verify-passphrase /dev/md1` 
(here you encrypting the md0(the keysize it can different there is other type of key sizes)

➡️ YES and write the Password you want, be carefull this will override data on /dev/md0 
➡️ `cryptsetup luksOpen /dev/md1 md1_crypt` (md0_crypt this can be any name you want)

## LVM

◽ Create a Physical Volume ➡️ `pvcreate /dev/mapper/md1_crypt` 

◽ Create a Volume Group ➡️ `vgcreate vg1 /dev/mapper/md1_crypt`

◽ Create Logical Volume ➡️ `lvcreate -n lv1 -l +100%FREE vg1`  (this one was made with 100% of the disk memory)


◽ Define which File System you going to use ➡️ mkfs.xfs /dev/vg0/lv0 


◽ Make a file for the mount ➡️ `mkdir /mnt/raid6`( i choose raid6 as name) 

◽ Mount the Logical Volume to the Directory ➡️ `mount /dev/vg1/lv1 /mnt/raid6`

## Optional ( or you do this or you need to mount every time the Machine is rebooted)

◽ In this step you need to copy the line associated with the mount command ➡️ `cat /etc/mtab` 

◽ Paste the line inside `/etc/fstab` ➡️ add ,nofail ❗ if dont,and the configs are wrong a rescue machiine will be needed


# Wazuh

◽ Install the Filebeat package ➡️ `apt-get install filebeat`

◽ Download the preconfigured Filebeat configuration file used to forward the Wazuh alerts to Elasticsearch ⤵️
`curl -so /etc/filebeat/filebeat.yml https://packages.wazuh.com/resources/4.2/open-distro/filebeat/7.x/filebeat_all_in_one.yml`

◽ Download the alerts template for Elasticsearch ⤵️

```
curl -so /etc/filebeat/wazuh-template.json https://raw.githubusercontent.com/wazuh/wazuh/4.2/extensions/elasticsearch/7.x/wazuh-template.json
chmod go+r /etc/filebeat/wazuh-template.json
```
◽ Download the Wazuh module for Filebeat ➡️ `curl -s https://packages.wazuh.com/4.x/filebeat/wazuh-filebeat-0.1.tar.gz | tar -xvz -C /usr/share/filebeat/module `

◽ Copy the Elasticsearch certificates into /etc/filebeat/certs ⤵️

```
mkdir /etc/filebeat/certs
cp ~/certs/root-ca.pem /etc/filebeat/certs/
mv ~/certs/filebeat* /etc/filebeat/certs/
```
◽ Enable and start the Filebeat service ⤵️
```
systemctl daemon-reload
systemctl enable filebeat
systemctl start filebeat
```

✔️ To ensure that Filebeat is successfully installed, run the following command ➡️ `filebeat test output`

An example response should look as follows:
```
Output

 elasticsearch: https://127.0.0.1:9200...
   parse url... OK
   connection...
     parse host... OK
     dns lookup... OK
     addresses: 127.0.0.1
     dial up... OK
   TLS...
     security: server's certificate chain verification is enabled
     handshake... OK
     TLS version: TLSv1.3
     dial up... OK
   talk to server... OK
   version: 7.10.2
```

◽ Install the Kibana package ➡️ `apt-get install opendistroforelasticsearch-kibana`

◽ Download the Kibana configuration file ⤵️

`curl -so /etc/kibana/kibana.yml https://packages.wazuh.com/resources/4.2/open-distro/kibana/7.x/kibana_all_in_one.yml`

◽ Create the /usr/share/kibana/data directory ⤵️
```

mkdir /usr/share/kibana/data
chown -R kibana:kibana /usr/share/kibana/data
```

◽ Install the Wazuh Kibana plugin. The installation of the plugin must be done from the Kibana home directory as follows:

```
cd /usr/share/kibana
sudo -u kibana /usr/share/kibana/bin/kibana-plugin install https://packages.wazuh.com/4.x/ui/kibana/wazuh_kibana-4.2.5_7.10.2-1.zip
```
◽ Copy the Elasticsearch certificates into /etc/kibana/certs:

```
mkdir /etc/kibana/certs
cp ~/certs/root-ca.pem /etc/kibana/certs/
mv ~/certs/kibana* /etc/kibana/certs/
chown kibana:kibana /etc/kibana/certs/*
```
◽ Link Kibana socket to privileged port 443 ➡️ `setcap 'cap_net_bind_service=+ep' /usr/share/kibana/node/bin/node`


◽ Enable and start the Kibana service:

```
systemctl daemon-reload
systemctl enable kibana
systemctl start kibana
```
◽ Access the web interface:

```
URL: https://<wazuh_server_ip>
user: admin
password: admin
Upon the first access to Kibana, the browser shows a warning message stating that the certificate was not issued by a trusted authority. An exception can be added in the advanced options of the web browser or, for increased security, the root-ca.pem file previously generated can be imported to the certificate manager of the browser. Alternatively, a certificate from a trusted authority can be configured.
```
◽ Note ➡️ It is highly recommended to change the default passwords of Elasticsearch for the users’ passwords. To perform this action, see the Elasticsearch tuning section.
