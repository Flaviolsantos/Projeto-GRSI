# ☁️ AWS

# VPC

👉 East-1 ➡️ Default VPC (172.31.0.0/16) Enta

👉 West-2 ➡️ Default VPC (172.31.0.0/16) Inova

## Subnets

◽ East-1

👉 2 Subnets in the same zone ➡️ 172.31.96.0/24 and 172.31.128.0/24

◽ West-2 

👉 2 Subnets in the same zone ➡️ 172.31.64.0/24 and 172.31.80.0/24


# EC2

## Network

◽East-1

👉 Create Network ➡️ Select Subnet 172.31.128.0/24

◽West-2

👉 Create Network ➡️ Select Subnet 172.31.80.0/24


# Instances

## ◽ East-1

⚪ control.enta.pt

👉 Create Instance ➡️ t2.small ➡️ VPC select default of the same zone of the subnets created ; Subnet select 172.31.16.0/24 (default of that zone), eth0 172.31.16.100 ; add eth1 172.31.96.100
    ➡️ Select the Securtiy Group ➡️  Create Instante 
   
   👉 Select control.enta.pt ➡️ Actions ➡️ Network ➡️ Attach Network 172.31.128.0/24
   
⚪ www.enta.pt 

👉 Create Instance ➡️ t2.micro ➡️ VPC select Default ➡️ Subnet 172.31.96.0/24 ➡️ eth0 172.31.96.101 ➡️ Select the Securtiy Group ➡️ Create Instante

⚪ central.enta.pt 

👉 Create Instance ➡️ t2.micro ➡️  VPC select Default ➡️ Subnet 172.31.128.0/24 ➡️ eht0 172.31.128.101 ➡️ Select the Securtiy Group ➡️ Create Instante

⚪ wazuh.enta.pt 

👉 Create Instance ➡️ t2.micro ➡️  VPC select Default ➡️ Subnet 172.31.128.0/24 ➡️ eht0 172.31.128.102 ➡️ Select the Securtiy Group ➡️ Create Instante

⚪ sales.enta.pt 

👉 Create Instance ➡️ t2.micro ➡️  VPC select Default ➡️ Subnet 172.31.128.0/24 ➡️ eht0 172.31.128.103 ➡️ Select the Securtiy Group ➡️ Create Instante

⚪ marketing.enta.pt

👉 Create Instance ➡️ t2.micro ➡️  VPC select Default ➡️ Subnet 172.31.128.0/24 ➡️ eht0 172.31.128.104 ➡️ Select the Securtiy Group ➡️ Create Instante

## ◽ West-2

⚪ control.inova.pt

👉 Create Instance ➡️ t2.small ➡️ VPC select default of the same zone of the subnets created ; Subnet select 172.31.16.0/24( default subtnet of that zone), eth0 172.31.16.100 ; add eth1 172.31.64.100
    ➡️ Select the Securtiy Group ➡️  Create Instante 
   
   👉 Select control.enta.pt ➡️ Actions ➡️ Network ➡️ Attach Network 172.31.80.0/24
   
⚪ www.inova.pt 

👉 Create Instance ➡️ t2.micro ➡️ VPC select Default ➡️ Subnet 172.31.64.0/24 ➡️ eth0 172.31.64.101 ➡️ Select the Securtiy Group ➡️ Create Instante

⚪ central.inova.pt 

👉 Create Instance ➡️ t2.micro ➡️  VPC select Default ➡️ Subnet 172.31.80.0/24 ➡️ eht0 172.31.80.101 ➡️ Select the Securtiy Group ➡️ Create Instante

⚪ wazuh.inova.pt 

👉 Create Instance ➡️ t2.micro ➡️  VPC select Default ➡️ Subnet 172.31.80.0/24 ➡️ eht0 172.31.80.102 ➡️ Select the Securtiy Group ➡️ Create Instante

⚪ sales.inova.pt 

👉 Create Instance ➡️ t2.micro ➡️  VPC select Default ➡️ Subnet 172.31.80.0/24 ➡️ eht0 172.31.80.103 ➡️ Select the Securtiy Group ➡️ Create Instante

⚪ marketing.inova.pt

👉 Create Instance ➡️ t2.micro ➡️  VPC select Default ➡️ Subnet 172.31.80.0/24 ➡️ eht0 172.31.80.104 ➡️ Select the Securtiy Group ➡️ Create Instante


⚪ remote.client.pt made on any VPC 


## 💾 Volumes 

◽ East-1

👉 Create 3 volumes and attach them to central.enta.pt

👉 Create 4 volume and attach them to wazuh.enat.py

◽ West-1 

👉 Create 3 volumes and attach them to central.inova.pt 

👉 Create 4 volumes and attach them to wazuh.inova.pt


## Elastic IPs

👉 Create 3 Elastic Ips ➡️ 1 for the Primary Network on Control.enta.pt , 1 for the Primary Network on Control.inova.pt and 1 for remote.client.pt

## 🔐 Security Groups

👉 ENTA ➡️ Ip of your Network, the 3 Private Ips (172.16.0.0/12 ,10.0.0.0,192.168.0.0/16) ,the Public Ips from Inova and remote.client.pt

👉 INOVA ➡️ Ip of your Network, the 3 Private Ips (172.16.0.0/12 ,10.0.0.0,192.168.0.0/16) ,the Public Ips from Enta and remote.client.pt

👉 REMOTE ➡️ Ip of your Network, the 3 Private Ips (172.16.0.0/12 ,10.0.0.0,192.168.0.0/16) ,the Public Ips from Enta and Inova



















