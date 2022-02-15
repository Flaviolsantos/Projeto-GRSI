# WWW.ENTA.PT

## 👉 FTP,HTTP,HTTPS 

🖥️ Terminal

◽ Change your hostname and domain ➡️ `sudo hostname set-hostnamectl www.enta.pt`

◽ Go to superuser ➡️ ` sudo su -`

◽ Update the machine ➡️ ` yum update`

## NAT 

◽ Go to `nano /etc/sysconfig/network-scripts/ifcfg-eth0` Add ⤵️ so you tell your Machine which is your Gateway and your DNS

``` 
GATEWAY=172.31.96.100
DNS1=172.31.16.100
DNS2=8.8.8.8
```

◽ Restart the Network ➡️ `systemctl restart network` ❗

✔️ do ping 1.1.1.1 to check if its working

## Certificates

◽ You need to change the defualt CA of your Machine so it recognises the certificates made by control.inova.pt ➡️ `nano /etc/pki/ca-trust/source/anchors/ca.crt` ➡️
➡️ put your ca.crt from control.inova.pt there ➡️ save the change ` update-ca-trust`  ❗

◽ Get your certificates from control.inova.pt and put then on /etc/ssl ➡️ but first you need to make a soft link for private because on Amazon Linux doenst exist ⤵️

```
cd /etc/ssl
ln -s /etc/pki/tls/private private

```
◽ Put your Certificates on /etc/ssl/certs and your keys on /etc/ssl/private ❗

## ▶️ FTP (vsftpd)

◽ Install FTP ➡️ `yum install vsftp`

◽ Go to ➡️ `nano /etc/vsftpd/vsftpd.conf `

```
# Example config file /etc/vsftpd/vsftpd.conf
#
# The default compiled in settings are fairly paranoid. This sample file
# loosens things up a bit, to make the ftp daemon more usable.
# Please see vsftpd.conf.5 for all compiled in defaults.
#
# READ THIS: This example file is NOT an exhaustive list of vsftpd options.
# Please read the vsftpd.conf.5 manual page to get a full idea of vsftpd's
# capabilities.
#
# Allow anonymous FTP? (Beware - allowed by default if you comment this out).
anonymous_enable=YES -> change to NO 
#
# Uncomment this to allow local users to log in.
# When SELinux is enforcing check for SE bool ftp_home_dir
local_enable=YES
#
# Uncomment this to enable any form of FTP write command.
write_enable=YES
#
# Default umask for local users is 077. You may wish to change this to 022,
# if your users expect that (022 is used by most other ftpd's)
local_umask=022
#
# Uncomment this to allow the anonymous FTP user to upload files. This only
# has an effect if the above global write enable is activated. Also, you will
# obviously need to create a directory writable by the FTP user.
# When SELinux is enforcing check for SE bool allow_ftpd_anon_write, allow_ftpd_full_access
#anon_upload_enable=YES
#
# Uncomment this if you want the anonymous FTP user to be able to create
# new directories.
#anon_mkdir_write_enable=YES
#
# Activate directory messages - messages given to remote users when they
# go into a certain directory.
dirmessage_enable=YES
#
# Activate logging of uploads/downloads.
xferlog_enable=YES
#
# Make sure PORT transfer connections originate from port 20 (ftp-data).
connect_from_port_20=YES
#
# If you want, you can arrange for uploaded anonymous files to be owned by
# a different user. Note! Using "root" for uploaded files is not
# recommended!
#chown_uploads=YES
#chown_username=whoever
#
# You may override where the log file goes if you like. The default is shown
# below.
#xferlog_file=/var/log/xferlog
#
# If you want, you can have your log file in standard ftpd xferlog format.
# Note that the default log file location is /var/log/xferlog in this case.
xferlog_std_format=YES
#
# You may change the default value for timing out an idle session.
#idle_session_timeout=600
#
# You may change the default value for timing out a data connection.
#data_connection_timeout=120
#
# It is recommended that you define on your system a unique user which the
# ftp server can use as a totally isolated and unprivileged user.
#nopriv_user=ftpsecure
#
# Enable this and the server will recognise asynchronous ABOR requests. Not
# recommended for security (the code is non-trivial). Not enabling it,
# however, may confuse older FTP clients.
#async_abor_enable=YES
#
# By default the server will pretend to allow ASCII mode but in fact ignore
# the request. Turn on the below options to have the server actually do ASCII
# mangling on files when in ASCII mode. The vsftpd.conf(5) man page explains
# the behaviour when these options are disabled.
# Beware that on some FTP servers, ASCII support allows a denial of service
# attack (DoS) via the command "SIZE /big/file" in ASCII mode. vsftpd
# predicted this attack and has always been safe, reporting the size of the
# raw file.
# ASCII mangling is a horrible feature of the protocol.
ascii_upload_enable=YES         UNCOMMENT THIS -> allow ascii mode 
ascii_download_enable=YES       UNCOMMENT THIS -> allow ascii mode 
#
# You may fully customise the login banner string:
#ftpd_banner=Welcome to blah FTP service.
#
# You may specify a file of disallowed anonymous e-mail addresses. Apparently
# useful for combatting certain DoS attacks.
#deny_email_enable=YES
# (default follows)
#banned_email_file=/etc/vsftpd/banned_emails
#
# You may specify an explicit list of local users to chroot() to their home
# directory. If chroot_local_user is YES, then this list becomes a list of
# users to NOT chroot().
# (Warning! chroot'ing can be very dangerous. If using chroot, make sure that
# the user does not have write access to the top level directory within the
# chroot)
chroot_local_user=YES       UNCOMMENT THIS -> enable chroot
chroot_list_enable=YES      UNCOMMENT THIS -> enable chroot
# (default follows)
chroot_list_file=/etc/vsftpd/chroot_list     UNCOMMENT THIS -> specify chroot list
#
# You may activate the "-R" option to the builtin ls. This is disabled by
# default to avoid remote users being able to cause excessive I/O on large
# sites. However, some broken FTP clients such as "ncftp" and "mirror" assume
# the presence of the "-R" option, so there is a strong case for enabling it.
ls_recurse_enable=YES            UNCOMMENT THIS
#
# When "listen" directive is enabled, vsftpd runs in standalone mode and
# listens on IPv4 sockets. This directive cannot be used in conjunction
# with the listen_ipv6 directive.
listen=YES
#
# This directive enables listening on IPv6 sockets. By default, listening
# on the IPv6 "any" address (::) will accept connections from both IPv6
# and IPv4 clients. It is not necessary to listen on *both* IPv4 and IPv6
# sockets. If you want that (perhaps because you want to listen on specific
# addresses) then you must run two copies of vsftpd with two configuration
# files.
# Make sure, that one of the listen options is commented !!
listen_ipv6=NO

# ADD THIS FOR YOUR CERTIFICATE

rsa_cert_file=/etc/ssl/certs/ftp.enta.pt.crt
rsa_private_key_file=/etc/ssl/private/ftp.enta.pt.key
ssl_enable=YES

# ADD THIS SO FOR IMPLICIT SSL ;
pam_service_name=vsftpd
userlist_enable=YES
tcp_wrappers=YES

          pasv_enable=YES
          pasv_min_port=10000
          pasv_max_port=10100

          allow_anon_ssl=NO
          force_local_data_ssl=YES
          force_local_logins_ssl=YES
          ssl_tlsv1=YES
          ssl_sslv2=NO
          ssl_sslv3=NO
          require_ssl_reuse=NO
          ssl_ciphers=HIGH

          implicit_ssl=YES
          listen_port=990

# FOR EXPLICIT SSL REMOVE LAST 2 LINES

```

◽ Restart VSFTPD ➡️ `systemctl restart vsftpd`

◽ Check the Status to see if everything is alright ➡️ `systemctl status vsftpd`


## HTTP/HTTPS (APACHE2)

◽ Install APACHE ➡️ `yum install httpd`

◽ Go to ` nano /etc/httpd/conf/httpd.conf` ➡️ for HTTP

```
# line 86: change to admin's email address
ServerAdmin root@enta.pt
# line 95: change to your server's name
ServerName www.enta.pt:80
# line 151: change
AllowOverride All

```

Go to `nano /etc/httpd/conf.d/ssl.conf` ➡️ For HTTPS

```
# line 100: add -> SSLCertificateFile /etc/pki/tls/certs/www.enta.pt.crt
# line 107: add -> SSLCertificateKeyFile /etc/pki/tls/private/www.enta.pt.key

```

◽ make file for htmls for HTTPS ➡️ `cp -r /var/www/html /var/www/htmls` ➡️ Go to /var/www/htmls and do `nano index.html` and write something just to verify when you search on a browser

◽ Restart the httpd ➡️ `systemctl restart httpd`

✔️ To check if HTTP,HTTPS,FTP you need a Client ( Sales and Marketing will be our Clients)(will do on marketing-enta-pt.md and sales-enta-pt.md )






