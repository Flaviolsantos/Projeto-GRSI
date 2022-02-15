# WWW.INOVA.PT

# 🖥️ Terminal (Termius/Putty)

◽ Change your hostname -> ` sudo hostnamectl set-hostname www.inova.pt`

◽ Change to Superuser -> `sudo su - `

◽ Update the machine ➡️ `apt update && apt -y upgrade`


## NAT

◽ Go to `nano /etc/netplan/50.ymal` and add ->

```
dhcp4-overrides:
    use-dns: false
    use-routes: false
routes:
  - to: default
    via: 172.31.64.100
nameservers:
    addresses: [172.31.16.100]

```

◽ After the file above is edited do `netplan` try to save the configruations

✔️ To check if its working do ping 1.1.1.1

## Certificates

◽ Its needed to add the ca in the server so you can verify your certificates
◽ Go to ➡️ `cd /usr/local/share/ca-certificates/` 
◽ Add your ca.crt ➡️ `nano ca.crt` and put the ca from control.inova.pt
◽ Make sure to update the ca ➡️ `update-ca-certificates`
## HTTP/HTTPS

◽ Install nginx ➡️ `apt install nginx`

◽ Start the service ➡️ `systemctl start nginx`

◽ Go to `cd /etc/nginx/sites-availeble`

◽ Make a copy of file default with other name (seguro is the name i choose) ➡️ `cp default seguro`

◽ Go to snippets ` cd /etc/nginx/snippes` ➡️ `nano snakeoil.conf` ➡️ cp the lines with the cert and key , change the name of the cert for the name of your own cert (www.inova.pt)

◽ Go to seguro ➡️ `nano /etc/nginx/sites-available/seguro` 




◽ Make a file for HTTPS ➡️ `mkdir /var/www/htmls` ➡️ create an index.htmls and write something inside so you can check its right 

◽ Go to sites-enable ( to enable he site) ➡️ `cd /etc/nginx/sites-enable`

◽ Make a soft link for the seguro file like the default ➡️ `ln -s /etc/nginx/sites-available/seguro seguro`

◽ Restart the service ➡️ `systemctl restart nginx`

✔️ This will be checked in the sales and marketing machines

## FTP (proftpd)

◽ Install the service ➡️ `apt-get install proftpd -y`

◽ Start the service ➡️ `systemctl start proftpd && systemctl enable proftpd && systemctl status proftpd`

◽ Restart the service ➡️ `systemctl restart proftpd`


