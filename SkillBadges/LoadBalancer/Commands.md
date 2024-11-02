# Define a região padrão como "us-west4"
export REGION="us-west4"
# Define a zona padrão como "us-west4-b"
export ZONE="us-west4-b"

# Cria uma instância (VM) chamada "nucleus-jumphost-161" na zona especificada
gcloud compute instances create nucleus-jumphost-161 \
    --zone="$ZONE" \
    --machine-type=e2-micro \
    --image-family=debian-11 \
    --image-project=debian-cloud

# Cria um template de instância chamado "nucleus-nginx-template" com um script de inicialização que instala e inicia o Nginx
gcloud compute instance-templates create nucleus-nginx-template \ 
    --machine-type=e2-medium \
    --metadata=startup-script='#! /bin/bash
apt-get update
apt-get install -y nginx
service nginx start
sed -i -- "s/nginx/Google Cloud Platform - $HOSTNAME/" /var/www/html/index.nginx-debian.html' \
    --boot-disk-type=pd-standard \
    --boot-disk-size=10GB \
    --image-family=debian-11 \
    --image-project=debian-cloud

# Cria um grupo de instâncias gerenciado chamado "nucleus-nginx-group" com 2 instâncias baseadas no template criado
gcloud compute instance-groups managed create nucleus-nginx-group \
    --base-instance-name nucleus-nginx \
    --template=nucleus-nginx-template \
    --size=2 \
    --zone="$ZONE"

# Cria uma regra de firewall para permitir tráfego TCP na porta 80
gcloud compute firewall-rules create permit-tcp-rule-906 \
  --network=default \
  --action=allow \
  --rules=tcp:80

# Cria uma verificação de integridade HTTP na porta 80 chamada "nucleus-health-check"
gcloud compute health-checks create http nucleus-health-check \
    --port=80

# Reserva um endereço IP global para o balanceador de carga
gcloud compute addresses create nucleus-lb-ipv4 \
    --ip-version=IPV4 \
    --global

# Cria um serviço de backend chamado "nucleus-backend-service" com a verificação de integridade configurada
gcloud compute backend-services create nucleus-backend-service \
    --protocol=HTTP \
    --health-checks=nucleus-health-check \
    --port-name=http \
    --global

# Adiciona o grupo de instâncias "nucleus-nginx-group" ao backend do balanceador de carga
gcloud compute backend-services add-backend nucleus-backend-service \
    --instance-group=nucleus-nginx-group \
    --instance-group-zone="$ZONE" \
    --global

# Cria um URL map chamado "nucleus-url-map" apontando para o serviço backend padrão
gcloud compute url-maps create nucleus-url-map \
    --default-service=nucleus-backend-service

# Cria um proxy HTTP chamado "nucleus-http-proxy" associado ao URL map
gcloud compute target-http-proxies create nucleus-http-proxy \
    --url-map=nucleus-url-map

# Cria uma regra de encaminhamento para o balanceador de carga, vinculando o proxy HTTP ao IP reservado na porta 80
gcloud compute forwarding-rules create nucleus-http-rule \
    --global \
    --target-http-proxy=nucleus-http-proxy \
    --ports=80 \
    --address=nucleus-lb-ipv4

# Define uma porta nomeada "http" para o grupo de instâncias, associando-a à porta 80
gcloud compute instance-groups set-named-ports nucleus-nginx-group \
    --named-ports=http:80 \
    --zone="$ZONE" 

# Cria uma regra de firewall para permitir acesso HTTP de fora da rede na porta 80
gcloud compute firewall-rules create allow-http-from-internet \
    --direction=INGRESS \
    --action=ALLOW \
    --rules=tcp:80
