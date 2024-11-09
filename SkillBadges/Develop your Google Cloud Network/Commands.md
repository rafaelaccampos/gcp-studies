# Define variáveis para a região, zona e ID do projeto na GCP
export REGION="us-east1"
export ZONE="us-east1-c"
export PROJECT_ID="qwiklabs-gcp-00-f861a17b0c14"

# Cria uma VPC personalizada chamada griffin-dev-vpc no projeto
gcloud compute networks create griffin-dev-vpc \
    --subnet-mode=custom \
    --project="$PROJECT_ID"

# Cria duas sub-redes na VPC de desenvolvimento:
gcloud compute networks subnets create griffin-dev-wp \
    --network=griffin-dev-vpc \
    --range=192.168.16.0/20 \
    --region="$REGION" \
    --project="$PROJECT_ID"
gcloud compute networks subnets create griffin-dev-mgmt \
    --network=griffin-dev-vpc \
    --range=192.168.32.0/20 \
    --region="$REGION" \
    --project="$PROJECT_ID"

# Cria uma VPC para o ambiente de produção
gcloud compute networks create griffin-prod-vpc \
    --subnet-mode=custom \
    --project="$PROJECT_ID"

# Cria duas sub-redes na VPC de produção
gcloud compute networks subnets create griffin-prod-wp \
    --network=griffin-prod-vpc \
    --range=192.168.48.0/20 \
    --region="$REGION" \
    --project="$PROJECT_ID"
gcloud compute networks subnets create griffin-prod-mgmt \
    --network=griffin-prod-vpc \
    --range=192.168.64.0/20 \
    --region="$REGION" \
    --project="$PROJECT_ID"

# Cria uma instância chamada bastion-host para gerenciamento seguro, permitindo a conexão SSH para os recursos de desenvolvimento e produção
gcloud compute instances create bastion-host \
    --zone="$ZONE" \
    --machine-type=n1-standard-1 \
    --network-interface=subnet=griffin-dev-mgmt \
    --network-interface=subnet=griffin-prod-mgmt \
    --tags=bastion \
    --project="$PROJECT_ID"

# Cria duas regras de firewall para permitir SSH para o bastion-host a partir de qualquer IP (0.0.0.0/0) nos ambientes de desenvolvimento e produção
gcloud compute firewall-rules create allow-ssh-bastion-dev \
    --direction=INGRESS \
    --priority=1000 \
    --network=griffin-dev-vpc \
    --action=ALLOW \
    --rules=tcp:22 \
    --source-ranges=0.0.0.0/0 \
    --target-tags=bastion \
    --project="$PROJECT_ID"
gcloud compute firewall-rules create allow-ssh-bastion-prod \
    --direction=INGRESS \
    --priority=1000 \
    --network=griffin-prod-vpc \
    --action=ALLOW \
    --rules=tcp:22 \
    --source-ranges=0.0.0.0/0 \
    --target-tags=bastion \
    --project="$PROJECT_ID"

# Estabelece uma conexão SSH com o bastion-host.
gcloud compute ssh bastion-host --zone="$ZONE" --project="$PROJECT_ID"

# Cria uma instância do Cloud SQL chamada griffin-dev-db usando MySQL 5.7 com 1 CPU e 4 GB de memória na região especificada
gcloud sql instances create griffin-dev-db \
    --database-version=MYSQL_5_7 \
    --cpu=1 \
    --memory=4GB \
    --region="$REGION" \
    --project="$PROJECT_ID"

# Define a senha para o usuário root do MySQL e conecta-se ao banco de dados.
gcloud sql users set-password root \
    --host=% \
    --instance=griffin-dev-db \
    --password=123
gcloud sql connect griffin-dev-db --user=root --project="$PROJECT_ID"

# Cria o banco de dados Wordpress, define um usuário wp_user com permissões totais para o banco e atualiza as permissões
CREATE DATABASE wordpress;
CREATE USER 'wp_user'@'%' IDENTIFIED BY 'stormwind_rules';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wp_user'@'%';
FLUSH PRIVILEGES;

# Cria um cluster Kubernetes griffin-dev com duas instâncias e2-standard-4, na zona e sub-rede griffin-dev-wp
gcloud container clusters create griffin-dev \
    --zone="$ZONE" \
    --num-nodes=2 \
    --machine-type=e2-standard-4 \
    --network=griffin-dev-vpc \
    --subnetwork=griffin-dev-wp \
    --enable-ip-alias \
    --project="$PROJECT_ID"

# Baixa arquivos de configuração do WordPress de um bucket de armazenamento.
gsutil cp -r gs://cloud-training/gsp321/wp-k8s .

# Configura o volume persistente para armazenar dados do WordPress e cria um Secret para guardar as credenciais do banco.
##Colocar no arquivo wp-env.yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: wordpress-volumeclaim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 200Gi
---
apiVersion: v1
kind: Secret
metadata:
  name: database
type: Opaque
stringData:
  username: wp_user
  password: stormwind_rules

# Cria um Secret Kubernetes db-user-pass com as credenciais do banco de dados.
kubectl create secret generic db-user-pass \
  --from-literal=username=wp_user \
  --from-literal=password=stormwind_rules

# Cria uma chave para o proxy do Cloud SQL, permitindo que o cluster Kubernetes acesse a instância do banco, e adiciona a chave como um Secret.
gcloud iam service-accounts keys create key.json \
  --iam-account=cloud-sql-proxy@$GOOGLE_CLOUD_PROJECT.iam.gserviceaccount.com

kubectl create secret generic cloudsql-instance-credentials \
  --from-file=key.json

cd wp-k8s

# Atualiza o arquivo wp-deployment.yaml com a string de conexão do banco, aplicando as configurações para criar e expor o serviço WordPress
substituir YOUR_INSTANCE no wp-deployment
pela connectionstring do sql

kubectl apply -f wp-deployment.yaml
kubectl get deployments
kubectl get pods
kubectl apply -f wp-service.yaml
kubectl get services

kubectl apply -f wp-env.yaml

# Cria um monitoramento de uptime para verificar a disponibilidade do endereço do Wordpress.
gcloud monitoring uptime create kraken-monitoring
--resource-type=uptime-url 
--resource-labels=host=35.243.153.0
--project="$PROJECT_ID"

# Concede a função de editor para outro engenheiro no projeto, permitindo que ele faça alterações.
gcloud projects add-iam-policy-binding qwiklabs-gcp-01-27ae9df7a342 \
  --member="user:second_engineer@example.com" \  # Substitua pelo email do engenheiro
  --role="roles/editor"