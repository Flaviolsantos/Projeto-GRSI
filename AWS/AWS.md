# âï¸ AWS

# VPC

ð East-1 â¡ï¸ Default VPC (172.31.0.0/16) Enta

ð West-2 â¡ï¸ Default VPC (172.31.0.0/16) Inova

## Subnets

â½ East-1

ð 2 Subnets in the same zone â¡ï¸ 172.31.96.0/24 and 172.31.128.0/24

â½ West-2 

ð 2 Subnets in the same zone â¡ï¸ 172.31.64.0/24 and 172.31.80.0/24


# EC2

## Network

â½East-1

ð Create Network â¡ï¸ Select Subnet 172.31.128.0/24

â½West-2

ð Create Network â¡ï¸ Select Subnet 172.31.80.0/24


# Instances

## â½ East-1

âª control.enta.pt

ð Create Instance â¡ï¸ t2.small â¡ï¸ VPC select default of the same zone of the subnets created ; Subnet select 172.31.16.0/24 (default of that zone), eth0 172.31.16.100 ; add eth1 172.31.96.100
    â¡ï¸ Select the Securtiy Group â¡ï¸  Create Instante 
   
   ð Select control.enta.pt â¡ï¸ Actions â¡ï¸ Network â¡ï¸ Attach Network 172.31.128.0/24
   
âª www.enta.pt 

ð Create Instance â¡ï¸ t2.micro â¡ï¸ VPC select Default â¡ï¸ Subnet 172.31.96.0/24 â¡ï¸ eth0 172.31.96.101 â¡ï¸ Select the Securtiy Group â¡ï¸ Create Instante

âª central.enta.pt 

ð Create Instance â¡ï¸ t2.micro â¡ï¸  VPC select Default â¡ï¸ Subnet 172.31.128.0/24 â¡ï¸ eht0 172.31.128.101 â¡ï¸ Select the Securtiy Group â¡ï¸ Create Instante

âª wazuh.enta.pt 

ð Create Instance â¡ï¸ t2.micro â¡ï¸  VPC select Default â¡ï¸ Subnet 172.31.128.0/24 â¡ï¸ eht0 172.31.128.102 â¡ï¸ Select the Securtiy Group â¡ï¸ Create Instante

âª sales.enta.pt 

ð Create Instance â¡ï¸ t2.micro â¡ï¸  VPC select Default â¡ï¸ Subnet 172.31.128.0/24 â¡ï¸ eht0 172.31.128.103 â¡ï¸ Select the Securtiy Group â¡ï¸ Create Instante

âª marketing.enta.pt

ð Create Instance â¡ï¸ t2.micro â¡ï¸  VPC select Default â¡ï¸ Subnet 172.31.128.0/24 â¡ï¸ eht0 172.31.128.104 â¡ï¸ Select the Securtiy Group â¡ï¸ Create Instante

## â½ West-2

âª control.inova.pt

ð Create Instance â¡ï¸ t2.small â¡ï¸ VPC select default of the same zone of the subnets created ; Subnet select 172.31.16.0/24( default subtnet of that zone), eth0 172.31.16.100 ; add eth1 172.31.64.100
    â¡ï¸ Select the Securtiy Group â¡ï¸  Create Instante 
   
   ð Select control.enta.pt â¡ï¸ Actions â¡ï¸ Network â¡ï¸ Attach Network 172.31.80.0/24
   
âª www.inova.pt 

ð Create Instance â¡ï¸ t2.micro â¡ï¸ VPC select Default â¡ï¸ Subnet 172.31.64.0/24 â¡ï¸ eth0 172.31.64.101 â¡ï¸ Select the Securtiy Group â¡ï¸ Create Instante

âª central.inova.pt 

ð Create Instance â¡ï¸ t2.micro â¡ï¸  VPC select Default â¡ï¸ Subnet 172.31.80.0/24 â¡ï¸ eht0 172.31.80.101 â¡ï¸ Select the Securtiy Group â¡ï¸ Create Instante

âª wazuh.inova.pt 

ð Create Instance â¡ï¸ t2.micro â¡ï¸  VPC select Default â¡ï¸ Subnet 172.31.80.0/24 â¡ï¸ eht0 172.31.80.102 â¡ï¸ Select the Securtiy Group â¡ï¸ Create Instante

âª sales.inova.pt 

ð Create Instance â¡ï¸ t2.micro â¡ï¸  VPC select Default â¡ï¸ Subnet 172.31.80.0/24 â¡ï¸ eht0 172.31.80.103 â¡ï¸ Select the Securtiy Group â¡ï¸ Create Instante

âª marketing.inova.pt

ð Create Instance â¡ï¸ t2.micro â¡ï¸  VPC select Default â¡ï¸ Subnet 172.31.80.0/24 â¡ï¸ eht0 172.31.80.104 â¡ï¸ Select the Securtiy Group â¡ï¸ Create Instante


âª remote.client.pt made on any VPC 


## ð¾ Volumes 

â½ East-1

ð Create 3 volumes and attach them to central.enta.pt

ð Create 4 volume and attach them to wazuh.enat.py

â½ West-1 

ð Create 3 volumes and attach them to central.inova.pt 

ð Create 4 volumes and attach them to wazuh.inova.pt


## Elastic IPs

ð Create 3 Elastic Ips â¡ï¸ 1 for the Primary Network on Control.enta.pt , 1 for the Primary Network on Control.inova.pt and 1 for remote.client.pt

## ð Security Groups

ð ENTA â¡ï¸ Ip of your Network, the 3 Private Ips (172.16.0.0/12 ,10.0.0.0,192.168.0.0/16) ,the Public Ips from Inova and remote.client.pt

ð INOVA â¡ï¸ Ip of your Network, the 3 Private Ips (172.16.0.0/12 ,10.0.0.0,192.168.0.0/16) ,the Public Ips from Enta and remote.client.pt

ð REMOTE â¡ï¸ Ip of your Network, the 3 Private Ips (172.16.0.0/12 ,10.0.0.0,192.168.0.0/16) ,the Public Ips from Enta and Inova



















