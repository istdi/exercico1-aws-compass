# exercico1-aws-compass
##Parte 1: Requisitos AWS

### permitir conexao e vpc
Vá em souche na AWS entre em VPC
##Clique em Create VPC:
Name tag: my-vpc-is-01
IPv4 CIDR block: 10.0.0.0/24

## Crie uma subnet:
Vá em Subnets e  Create Subnet.
Name tag: my-subnet-is-01
CIDR block: 10.0.0.0/28

## Crie um Internet Gateway:
Vá em Internet Gateway e Create Internet Gateway.
Anexe o Internet Gateway à VPC.
Atualize a Route Table para rotear o tráfego da subnet para o Internet Gateway:
Destino: 0.0.0.0/0

 ## Gerar Chave Pública
No console da AWS, vá  EC2 > Key Pairs.
Create Key Pair.
Nomeie a chave (ex: my-key) e faça o download do arquivo .pem.

## Criar Instância EC2
No console da AWS, vá  EC2 e Launch Instance.

## Escolha a AMI Amazon Linux 2.
Selecione o tipo de instância como t3.small.
Selecione a chave pública criada para conexão a instância
Configure o armazenamento como 16 GB SSD.

## Edit securyti
Name / PB - JUL 2024 / Instances e Volumes
CostCenter / C092000024 / Instances e Volumes
Project/ PB - JUL 2024 / Instances e Volumes

## Em Network Settings:
Porta 22/TCP (SSH) - source type Anywhere
Porta 80/TCP (HTTP) - source type Anywhere
Porta 443/TCP (HTTPS) - source type Anywhere
Porta 111/TCP e UDP (NFS) - source type Anywhere
Porta 2049/TCP e UDP (NFS) - source type Anywhere
Gere um Elastic IP e anexe à instância EC2.

## Instale o servidor NFS:
sudo yum install nfs-utils -y
sudo mkdir /mnt/nfs/israel-dsa
sudo chown -R ec2-user:ec2-user /mnt/nfs/
sudo echo "/mnt/alerta_apache *(rw,sync,no_root_squash,no_subtree_check)" | sudo tee -a /etc/exports
sudo systemctl start nfs-server
sudo systemctl enable nfs-server

## Crie um diretório no NFS com o seu nome:
mkdir /mnt/nfs/israel-dsa/alerta_apache/
### Subir Apache no Servidor
Instale o Apache:
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd

## Criar Script de Monitoramento
 script Bash status do Apache:
sudo nano monitor_apache.sh
Adicione o seguinte conteúdo:

#!/bin/bash
SERVICE="httpd"
STATUS=$(systemctl is-active $SERVICE)
DATE=$(date '+%Y-%m-%d %H:%M:%S')
DIR="/mnt/nfs/israel-dsa/alerta_apache"

if [ "$STATUS" = "active" ]; then
  echo "$DATE - $SERVICE - ONLINE - Serviço está funcionando" > $DIR/apache_online.txt
else
  echo "$DATE - $SERVICE - OFFLINE - Serviço está fora do ar" > $DIR/apache_offline.txt
fi

## Torne o script executável:
sudo chmod +x /usr/local/bin/monitor_apache.sh
###Automatizar o Script com Cron
Configure o cron para executar o script a cada 5 minutos:
crontab -e

## Adicione a seguinte linha:
*/5 * * * * /mnt/nfs/israel-dsa
