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
```
     listen 80 default_server;            comment this
     listen [::]:80 default_server;        comment this

      # SSL configuration
      #
     listen 443 ssl default_server;       uncomment this
     listen [::]:443 ssl default_server;  uncomment this
     
     # include snippets/snakeoil.conf;
         ssl_certificate /etc/ssl/certs/www.inova.pt.crt; add this
         ssl_certificate_key /etc/ssl/private/www.inova.pt.key; add this

              root /var/www/htmls; change html to htmls
   ```
    




◽ Make a file for HTTPS ➡️ `mkdir /var/www/htmls` ➡️ create an index.htmls and write something inside so you can check its right 

◽ Go to sites-enable ( to enable he site) ➡️ `cd /etc/nginx/sites-enable`

◽ Make a soft link for the seguro file like the default ➡️ `ln -s /etc/nginx/sites-available/seguro seguro`

◽ Restart the service ➡️ `systemctl restart nginx`

✔️ This will be checked in the sales and marketing machines

## FTP (proftpd)

◽ Install the service ➡️ `apt-get install proftpd -y`

◽ Start the service ➡️ `systemctl start proftpd && systemctl enable proftpd && systemctl status proftpd`

◽ Go to `nano proftpd.conf` 

```
#
# /etc/proftpd/proftpd.conf -- This is a basic ProFTPD configuration file.
# To really apply changes, reload proftpd after modifications, if
# it runs in daemon mode. It is not required in inetd/xinetd mode.
# 

# Includes DSO modules
Include /etc/proftpd/modules.conf

# Set off to disable IPv6 support which is annoying on IPv4 only boxes.
UseIPv6                         off    on -> off
# If set on you can experience a longer connection delay in many cases.
IdentLookups                    off

ServerName                      "www.inova.pt"  define your server name
# Set to inetd only if you would run proftpd by inetd/xinetd.
# Read README.Debian for more information on proper configuration.
ServerType                              standalone
DeferWelcome                    off

MultilineRFC2228                on
DefaultServer                   on
ShowSymlinks                    on

TimeoutNoTransfer               600
TimeoutStalled                  600
TimeoutIdle                     1200

DisplayLogin                    welcome.msg
DisplayChdir                    .message true
ListOptions                     "-l"

DenyFilter                      \*.*/

# Use this to jail all users in their homes 
 DefaultRoot                    ~

# Users require a valid shell listed in /etc/shells to login.
# Use this directive to release that constrain.
# RequireValidShell             off

# Port 21 is the standard FTP port.
Port                            21

# In some cases you have to specify passive ports range to by-pass
# firewall limitations. Ephemeral ports can be used for that, but
# feel free to use a more narrow range.
 PassivePorts                  10000 10100  change to ports 10000 10100

# If your host was NATted, this option is useful in order to
# allow passive tranfers to work. You have to use your public
# address and opening the passive ports used on your firewall as well.
# MasqueradeAddress             1.2.3.4

# This is useful for masquerading address with dynamic IPs:
# refresh any configured MasqueradeAddress directives every 8 hours
<IfModule mod_dynmasq.c>
# DynMasqRefresh 28800
</IfModule>

# To prevent DoS attacks, set the maximum number of child processes
# to 30.  If you need to allow more than 30 concurrent connections
# at once, simply increase this value.  Note that this ONLY works
# in standalone mode, in inetd mode you should use an inetd server
# that allows you to limit maximum number of processes per service
# (such as xinetd)
MaxInstances                    30

# Set the user and group that the server normally runs at.
User                            proftpd
Group                           nogroup

# Umask 022 is a good standard umask to prevent new files and dirs
# (second parm) from being group and world writable.
Umask                           022  022
# Normally, we want files to be overwriteable.
AllowOverwrite                  on

# Uncomment this if you are using NIS or LDAP via NSS to retrieve passwords:
 PersistentPasswd               on           change to on

# This is required to use both PAM-based authentication and local passwords
# AuthOrder                     mod_auth_pam.c* mod_auth_unix.c

# Be warned: use of this directive impacts CPU average load!
# Uncomment this if you like to see progress and transfer rate with ftpwho
# in downloads. That is not needed for uploads rates.
#
# UseSendFile                   off

TransferLog /var/log/proftpd/xferlog
SystemLog   /var/log/proftpd/proftpd.log

# Logging onto /var/log/lastlog is enabled but set to off by default
#UseLastlog on

# In order to keep log file dates consistent after chroot, use timezone info
# from /etc/localtime.  If this is not set, and proftpd is configured to
# chroot (e.g. DefaultRoot or <Anonymous>), it will use the non-daylight
# savings timezone regardless of whether DST is in effect.
#SetEnv TZ :/etc/localtime

<IfModule mod_quotatab.c>
QuotaEngine off
</IfModule>

<IfModule mod_ratio.c>
Ratios off
</IfModule>


# Delay engine reduces impact of the so-called Timing Attack described in
# http://www.securityfocus.com/bid/11430/discuss
# It is on by default. 
<IfModule mod_delay.c>
DelayEngine on
</IfModule>

<IfModule mod_ctrls.c>
ControlsEngine        off
ControlsMaxClients    2
ControlsLog           /var/log/proftpd/controls.log
ControlsInterval      5
ControlsSocket        /var/run/proftpd/proftpd.sock
</IfModule>

<IfModule mod_ctrls_admin.c>
AdminControlsEngine off
</IfModule>

#
# Alternative authentication frameworks
#
#Include /etc/proftpd/ldap.conf
#Include /etc/proftpd/sql.conf

#
# This is used for FTPS connections
#
Include /etc/proftpd/tls.conf

#
# Useful to keep VirtualHost/VirtualRoot directives separated
#
#Include /etc/proftpd/virtuals.conf

# A basic anonymous configuration, no upload directories.

# <Anonymous ~ftp>
#   User                                ftp
#   Group                               nogroup
#   # We want clients to be able to login with "anonymous" as well as "ftp"
#   UserAlias                   anonymous ftp
#   # Cosmetic changes, all files belongs to ftp user
#   DirFakeUser on ftp
#   DirFakeGroup on ftp
# 
#   RequireValidShell           off
# 
#   # Limit the maximum number of anonymous logins
#   MaxClients                  10
# 
#   # We want 'welcome.msg' displayed at login, and '.message' displayed
#   # in each newly chdired directory.
#   DisplayLogin                        welcome.msg
#   DisplayChdir                .message
# 
#   # Limit WRITE everywhere in the anonymous chroot
#   <Directory *>
#     <Limit WRITE>
#       DenyAll
#     </Limit>
#   </Directory>
# 
#   # Uncomment this if you're brave.
#   # <Directory incoming>
#   #   # Umask 022 is a good standard umask to prevent new files and dirs
#   #   # (second parm) from being group and world writable.
#   #   Umask                           022  022
#   #            <Limit READ WRITE>
#   #            DenyAll
#   #            </Limit>
#   #            <Limit STOR>
#   #            AllowAll
#   #            </Limit>
#   # </Directory>
# 
# </Anonymous>

# Include other custom configuration files
# !! Please note, that this statement will read /all/ file from this subdir,
# i.e. backup files created by your editor, too !!!
# Eventually create file patterns like this: /etc/proftpd/conf.d/*.conf
# 
Include /etc/proftpd/conf.d/

```

◽ Restart the service ➡️ `systemctl restart proftpd`


